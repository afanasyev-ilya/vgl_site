---
layout: default
---

Here we will demonstrate how to implement a conventional
 top-down Breadth-First Search (BFS) algorithm using VGL.
 
First, create you sample _my_bfs.cpp_ file, and put there the following parts.

```c++
// Specify that we want to use NEC SX-Aurora TSUBASA API part. 
// This is the only mandatory constants to specify.
#define __USE_NEC_SX_AURORA__

// Specify graph splitting threshold ([TODO VGL reference]). 
// It is not mandatory, but 256 value is desired for high-performance of BFS algorithm 
#define VECTOR_CORE_THRESHOLD_VALUE 256
```

Second, include VGL header file, which automatically includes all necessary API parts 
and functions. It is important to specify all the required constants (from the previous part)
before including VGL header.

```c++
#include "graph_library.h"
```

Third, implement your algorithm in main() function using VGL API.

```c++
int main(int argc, const char * argv[])
{
    // Declare graph in optimized Vect CSR representation
    VectCSRGraph graph;
    
    // you can initialize VectCSR in different ways. Here we generate 
    // a random edges list graph, and copy it to Vect CSR graph. Format conversion may take some time.
    EdgesListGraph el_graph;
    int vertices_count = 1024*1024;
    int edges_count = 32*vertices_count;
    GraphGenerationAPI::random_uniform(el_graph, vertices_count, edges_count, DIRECTED_GRAPH);

    // Warning! Graph vertices is reordered and renumbered here. You can use special VGL API functions to renumber vertices. 
    graph.import(el_graph);

    // define BFS-levels for each graph vertex
    VerticesArray<int> levels(graph, SCATTER);
    
    // Define class, which includes all VGL computaional abstractions
    GraphAbstractionsNEC graph_API(graph, SCATTER);

    // Define frontier - a subset, which contains vertices visited on each BFS level
    FrontierNEC frontier(graph, SCATTER);

    // We will launch BFS form vertex number 10 of original graph. Renumber API has to be used here,
    // since vertices of Vect CSR graph are renumbered.
    int source_vertex = _graph.reorder(10, ORIGINAL, SCATTER);

    // Use Compute abstraction to initialize initial levels for each vertex
    auto init_levels = [levels, source_vertex] (int src_id, int connections_count, int vector_index)
    {
        if(src_id == source_vertex)
            levels[source_vertex] = 1;
        else
            levels[src_id] = -1;
    };
    // Use all-active frontier to initialize each graph vertex
    frontier.set_all_active();
    graph_API.compute(graph, frontier, init_levels);

    // Clear the frontier.
    frontier.clear();
    // Add source vertex to the frontier (now it contains only this vertex).
    frontier.add_vertex(source_vertex);

    int current_level = 1;
    // Loop over BFS levels. If level (frontier) contains no vertices, stop the algorithm.
    while(frontier.size() > 0)
    {
        // For each vertex, visit all its outgoing edges (scatter direction).
        auto edge_op = [&levels, &current_level](int src_id, int dst_id, int local_edge_pos,
                long long int global_edge_pos, int vector_index, DelayedWriteNEC &delayed_write)
        {   
            // Load adjacent vertex.
            int dst_level = levels[dst_id];
            
            // If adjacent vertex is unvisited, mark it as visited.
            if(dst_level == -1)
            {
                levels[dst_id] = current_level + 1;
            }
        };
        graph_API.scatter(graph, frontier, edge_op, EMPTY_VERTEX_OP, EMPTY_VERTEX_OP,
                          edge_op, EMPTY_VERTEX_OP, EMPTY_VERTEX_OP);

        // Generate a new level of graph vertices, which have been visited in scatter abstraction.
        auto on_next_level = [&levels, current_level] (int src_id, int connections_count)->int
        {
            int result = -1;
            if(levels[src_id] == (current_level + 1))
                result = -1;
            return result;
        };
        graph_API.generate_new_frontier(graph, frontier, on_next_level);

        current_level++;
    }
}
```