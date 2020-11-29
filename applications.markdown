---
layout: default
---

VGL provides several highly-optimised graph applications, aimed to solve the following graph problems:

1. Breadth-First Search (BFS)

2. Connected Components (CC)

3. Page Rank (PR)

4. Single Source Shortest Paths (SSSP)

5. Strongly Connected Components (SCC)

Implementations of algorithms are located in _./algorithms_ folder, while executables 
are located in the _./apps_ folder. 

| Application | Makefile target name | NEC SX-Aurora TSUBASA executable | NVIDIA GPU executable |
|-------|--------|---------|
| Breadth-First Search | bfs| bfs_sx | bfs_cu |
| Connected Components | cc | cc_sx | cc_cu |
| Page Rank | pr | pr_sx | pr_cu |
| Single Source Shortest Paths | sssp | sssp_sx | sssp_cu |
| Strongly Connected Components | scc | scc_sx | scc_cu |

### Compilation

It is possible to compile either all applications at once:

> cd apps

> make -f Makefile.nec all

or only a specific application:

> make -f Makefile.nec bfs

### Running VGL applications

| Flag | Additional arguments | Description | 
|-------|--------|---------|
| -load | ./path/to/file/in/vgraph/format | Execute the application on graph in the provided file. If -load flag is not specified, a synthetic RMAT graph with 2^10 vertices and 2^14 edges is generated.   |
| -check | none | Compare results, calculated by optimized parallel algorithm with a sequential sequential version of similar algorithm. |
| -it | [n - integer number] | Execute n iterations of the algorithm. |
| -s | [s - integer number] | Set the amount of vertices in generated synthetic graph equal to 2^s |
| -e | [e - integer number] | Set the amount of edges in generated synthetic graph equal to e*2^s |
| -type | [rmat, ru] | Generate synthetic graph of a specified type (RMAT or uniform random) |

For example, Breadth-First application can be launched on synthetic RMAT graph with 1 million vertices and 32 million edges in the following way:

> cd bin

> ./bfs_sx -s 20 -e 32 -type rmat -it 10 -check

or 

> ./bfs_sx -load ./input_graphs/soc_lj.vgraph -it 10 -check

In addition, some applications have additional flags, which allow to choose different parallel algorithms.

**SSSP** : -all-active, -partial-active, -push, -pull;

**CC** : -shiloach_vishkin, -bfs_based;

**BFS** : -top-down (-td), -direction-optimizing (-do);

### Converting graphs into VGL format

VGL provides _./create_vgl_graphs_ program, which allows to convert a graph into VGL representation and save it as
a binary file. _./create_vgl_graphs_ program should be executed on Vector Host, and has the following flags and arguments:

| Flag | Additional arguments | Description | 
|-------|--------|---------|
| -s | [s - integer number] | Set the amount of vertices in generated synthetic graph equal to 2^s |
| -e | [e - integer number] | Set the amount of edges in generated synthetic graph equal to e*2^s |
| -type | [rmat, ru] | Generate synthetic graph of RMAT or uniform-random type |
| -format | [vect_csr] | Specify format of the output graph. By default VGL binary format (vect_csr) is used. |
| -file | /path/to/output/file | Specify file name of the output graph |
| -convert | /path/to/text/file/with/graph/in/EL/format/ | Convert external graph from the provided .txt file (for example from downloaded SNAP collection) into the specified output format. If this option is specified, synthetic graph is NOT generated. |

For example, LiveJournal graph can nbe imported from SNAP using the following commands:

> wget https://snap.stanford.edu/data/soc-LiveJournal1.txt.gz --no-check-certificate
>
> gunzip soc-LiveJournal1.txt.gz
>
> ./create_vgl_graphs_sx -convert ./source_graphs/soc-LiveJournal1.txt -file ./input_graphs/soc_lj -format vect_csr

Synthetic RMAT graph with 1 million vertices and 32 million edges can be generated in the following way: 

> ./create_vgl_graphs_sx -s 20 -e 32 -type rmat -file ./input_graphs/rmat_20_32 -format vect_csr
