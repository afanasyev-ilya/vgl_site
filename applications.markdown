---
layout: default
---

VGL provides several highly-optimised graph applications, aimed to solve the following graph problems:

1. Breadth-First Search (BFS)

2. Connected Components (CC)

3. Page Rank (PR)

4. Single Source Shortest Paths (SSSP)

5. Strongly Connected Components (SCC)

These applications can be compiled and executed as follows.

### Compilation

It is possible to compile either all applications at once:

make -f Makefile.nec all

Or only the specific ones:

make -f Makefile.nec cc bfs

### Execution

| flag | description | arguments |
|-------|--------|---------|
| -load | ./path/to/file/in/vgraph/format | Load graph from  |
| -check | none | Compare computational results of optimized parallel algorithm with its sequential counterpart. |
| -it | [number of iterations] | fuji |

### Exporting graph from file

Each application launched on graph, loaded from file:

./bfs_sx -load ./input_graphs/rmat_20_32.vgraph -it 10 -check

Or it can automatically generate a synthetic graph of the required type:

./bfs_sx -load ./input_graphs/