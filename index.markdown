---
layout: default
---

VGL is a high-performance graph processing framework, designed for modern NEC SX-Aurora TSUBASA vector architecture. 
NEC SX-Aurora TSUBASA is equipped with world highest memory bandwidth of 1.2-1.5 TB/s,
which allows to significantly solving various graph problems.
VGL provides simple computational and data abstractions, which allow 
to easily express many graph algorithms.

VGL automatically resolves the following issues:

1. automatic vectorisation of graph algorithms using vector instructions of maximal length;

2. parallel graph-processing using all SX-Aurora Vector cores;

3. optimal workload balancing when processing various scale-free graph;

4. good memory access pattern inside vector instructions;

5. applying graph pre-processing techniques in order to significantly accelerate solving various graph problems.


Thus, VGL user is able to express many graph algorithms using only 20-100 lines of code, 
compared to 1000+ lines of code, typical for manually-optimized (hardwired) implementations.
In the meantime, VGL user may be completely unfamiliar with the principles of creating efficient programs for vector systems.


### VGL cross-platform API

In addition, VGL provides a unified interface for porting VGL-based implementations for other other architectures with 
high-bandwidth memory, such as NVIDIA GPUs. Using cross-platform API a VGL user can launch graph algorithms implementations
on different architectures with only minor code modifications (for example inserting __device__ directive 
before lambda functions).

### Using VGL

1. [Download](./VectorGraphLibrary.zip) and [install](./installation.html) VGL.

2. Check VGL [samples(./bfs_example.html).

3. Check [documentation](./documentation.html) to learn more about VGL computational and its data abstractions.

4. Check to learn more about VGL [performance](./performance.html) compared to existing state-of-the-art 
multicore CPU and NVIDIA GPU graph-processing frameworks and libraries.

5. If you plan using VGL for graph analytics, please [cite](./cite.html) our works. 
