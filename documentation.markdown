---
layout: default
---

The VGL framework is designed to easily implement various iterative graph algorithms, such as:
 
1. BFS (breadth-first search),
 
2. SSSP(single source shortest paths),
 
3. PR(page rank),
 
4. CC(connected components),
 
5. SCC(strongly connected components), 

6. and many others. 

In VGL, 4 data abstractions and 4 computational abstractions are used.

### Data abstractions

#### 1. Graph
A graph is the main data-abstraction of the VGL framework. Graphs in VGL are stored in the optimized and preprocessed format (VectCSR),
which is based on the CSR (Compressed Sparse Row) representation. 
The VGL framework provides a convenient interface for working with both directed and undirected graphs.
For directed graphs, outgoing and incoming edges are stored for each vertex.
For undirected graphs, all edges are stored as outgoing.

#### 2. Frontier
Frontier of graph vertices is the second important data-abstraction in VGL. Frontier is a specific 
subset of graph vertices, which satisfy some user-defined conditions. Many graph algorithms (such as BFS) on each 
iterations process only specific vertices, which can be implemented in VGL using frontier abstractions.
"
Frontier in VGL can be one of 3 types: "all-active", "dense", or "mixed". 
"All-active" frontiers include all graph vertices and allow us to significantly 
reduce an overhead for the frontier maintenance. "All-active" frontiers are beneficial
for implementing graph algorithms, in which all graph vertices and edges have to be processed 
at each algorithm iteration (e.g. Bellman-Ford). "Dense" and "mixed" frontiers respectively represent cases 
when most/few graph vertices belong to the frontier.

![Branching]({{site.data_url}}/assets/img/data_abstractions.png)

#### 3 Vertices Array
The VerticesArray abstraction allows to store information about graph vertices,
for example current level of each vertex (in BFS algorithms) or current distances 
to each vertex (in shortest paths algorithms). VerticesArray abstraction has a 
straightforward implementation using aligned arrays.

#### 4. Edges Array
The EdgesArray abstraction allows to store information about graph edges.
Using EdgesArray VGL supports storing weighted graphs. This way weighted
edges are stored as a structure of arrays, providing better memory access
pattern for each vector instruction, which loads information about graph edges.


## Computational abstractions

#### 1. Advance

The advance abstractions is the main tool of traversing graph in VGL. 

**Input**: graph (in VectCSR format), frontier, vertex_preprocess_op, edge_op, vertex_postprocess_op.
**Output**: None (user-defined lambda functions typically updates some VerticesArrays or EdgesArrays).

During its execution, the advance applies vertex_preprocess_op to each vertex of input frontier, edge_op to each 
of its adjacent edges, and then vertex_postprocess_op to the vertex again. 
It is guaranteed that the execution of vertex_preprocess_op, edge-processing, and vertex_postprocess_op operations
for each vertex are serialized. However, all edge op operations for each adjacent edge are executed in parallel.
In addition, all frontier vertices can also be processed in parallel. 
 
Typically, vertex pre-process (or post-process) operations are used to initialize (or finalize) some data-processing,
to be performed during edge traversals. For example, in the page rank algorithm, the edge op operation can be defined to accumulate new ranks, 
while postprocess operations can be implemented to save the result using the input of dangling nodes and the coefficients of the page rank.

![Branching]({{site.data_url}}/assets/img/computational_abstractions.png)

Prototype of the Advance abstraction and it's lambda-functions:
```c++
auto EDGE_OP = [] (int src_id, int dst_id, int local_edge_pos, long long int global_edge_pos, int vector_index,
                         DelayedWriteNEC &delayed_write) {};
auto VERTEX_OP = [] (int src_id, int connections_count, int vector_index, DelayedWriteNEC &delayed_write){};

template <typename _TVertexValue, typename _TEdgeWeight, typename EdgeOperation, typename VertexPreprocessOperation,
             typename VertexPostprocessOperation>
void advance(ExtendedCSRGraph<_TVertexValue, _TEdgeWeight> &_graph,
             FrontierNEC &_frontier, EdgeOperation &&edge_op,
             VertexPreprocessOperation &&vertex_preprocess_op, 
             VertexPostprocessOperation &&vertex_postprocess_op);
```

#### 2. Compute

The compute abstraction can be used for a wide range of operations over graph vertices: 
initializing distances in shortest paths, implementing the “hook” phase in connected component
algorithms, and many others.

**Input**: graph (in VectCSR format), frontier, user-defined compute operation.
**Output**: None (user-defined lambda function compute_op typically updates some VerticesArrays).

Prototype of the Compute abstraction and it's lambda-functions:
```c++
auto COMPUTE_OP = [] (int src_id, int connections_count, int vector_index){};

template <typename _TVertexValue, typename _TEdgeWeight, typename ComputeOperation>
void compute(ExtendedCSRGraph<_TVertexValue, _TEdgeWeight> &_graph, 
             FrontierNEC &_frontier, 
             ComputeOperation &&compute_op);
```

#### 3. Reduce
This Reduce abstraction can be used for a large number of applications: estimating future frontier size in BFS,
calculating dangling nodes inputs in page rank, etc. The conventional frameworks, such as Gunrock, implement 
functionality of the reduce abstraction using a combination of the compute abstraction
(or its analogues) and user-defined operations with atomic instructions. 
However, atomic operations on the SX-Aurora architecture easily becomes a performance 
bottleneck compared to GPUs and multicore CPUs. Thus, the atomic operation must be avoided 
in the VGL framework. Furthermore, the reduction implementation for the SX-Aurora architecture 
is much more efficient compared to highly-optimized thrust and "modernGPU" GPU libraries, 
which is the main reason behind implementing the reduce as a separate abstraction in the VGL.

**Input**: graph (in VectCSR format), frontier, user-defined reduction operation (reduce_op), reduction type.
**Output**: reduction value.

Prototype of the Reduce abstraction and it's lambda-functions:
```c++
auto REDUCE_OP = [] (int src_id, int connections_count, int vector_index){};

enum REDUCE_TYPE
{
    REDUCE_SUM = 0,
    REDUCE_MAX = 1,
    REDUCE_MIN = 1,
    REDUCE_AVG = 3
};

template <typename _T, typename ReduceOperation>
_T reduce(VectCSRGraph &_graph,
          FrontierNEC &_frontier,
          ReduceOperation &&reduce_op,
          REDUCE_TYPE _reduce_type);
```

#### 4. Generate New Frontier

The generate new frontier abstraction allows a user to create a new subset of vertices,
which can be later processed by other abstractions (advance, compute, reduce). 

**Input**: graph (in VectCSR format), user-defined condition (cond).
**Output**: frontier, which includes vertices with IN_FRONTIER flag returned from cond.

Prototype of the Generate_new_frontier abstraction and it's lambda-functions:
```c++
auto COND = [] (int src_id, int connections_count)->int

template <typename _TVertexValue, typename _TEdgeWeight, typename Condition>
void generate_new_frontier(ExtendedCSRGraph<_TVertexValue, _TEdgeWeight> &_graph, 
                          FrontierNEC &_frontier, 
                          Condition &&cond);
```