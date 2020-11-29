---
layout: default
---

VGL provides several highly-optimised graph applications, aimed to solve the following graph problems:

1. Breadth-First Search (BFS)

2. Connected Components (CC)

3. Page Rank (PR)

4. Single Source Shortest Paths (SSSP)

5. Strongly Connected Components (SCC)

Implementations of algorithms are located in _./algorithms_ folder, while executables in the _./apps_ folder. These applications can be compiled and executed as follows.

### Compilation

It is possible to compile either all applications at once:

cd apps

make -f Makefile.nec all

or:

make -f Makefile.nec cc bfs

### Running VGL applications

| Flag | Additional arguments | Description | 
|-------|--------|---------|
| -load | ./path/to/file/in/vgraph/format | Execute the application on graph in th provided file. If -load flag is not specified, a synthetic graph is generated.   |
| -check | none | Compare computational results of optimized parallel algorithm with its sequential counterpart. |
| -it | [n - integer number] | Execute n iterations of the algorithm. |
| -s | [s - integer number] | Set the amount of vertices in generated synthetic graph equal to 2^s |
| -e | [e - integer number] | Set the amount of edges in generated synthetic graph equal to e*2^s |
| -type | [rmat, ru] | Generate synthetic graph of RMAT or uniform-random type |

For example, Breadth-First application can be launched on synthetic RMAT graph with 1 million vertices and 32 million edges:
./bfs_sx -s 20 -e 32 -type rmat -it 10 -check

or 

./bfs_sx -load ./input_graphs/soc_lj.vgraph -it 10 -check

### Converting graphs into VGL format