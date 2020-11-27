---
layout: default
---

**VGL** is a **high-performance graph processing framework**, designed for modern **NEC SX-Aurora 
TSUBASA** vector architecture. 
NEC SX-Aurora TSUBASA is equipped with world highest memory bandwidth of 1.2-1.5 TB/s,
which allows to significantly accelerate solving graph-processing.

**VGL significantly outperforms many state-of the art graph-processing frameworks** for modern multicore
CPUs and NVIDIA GPUs, such as Gunrock, CuSHA, Ligra, Galois, GAPBS, as shown in [performance](./performance.html).

VGL **automatically** resolves the following issues:

1. **automatic vectorisation** of graph algorithms using vector instructions of maximal length;

2. **parallel graph-processing** using all SX-Aurora Vector cores;

3. optimal parallel **workload balancing** when processing different scale-free graphs;

4. efficient (**coalesced**) memory access pattern when loading information about graph edges;

5. applying **graph pre-processing** techniques, which allow to use LLC cache more efficiently when 
loading information about edges.


VGL provides simple computational and data abstractions, which allow to easily express various iterative graph algorithms. 
Many graph algorithms in VGL can be written in **under 100 lines of code**, while VGL user may be completely unaware how to
create efficient programs for vector systems.

### VGL cross-platform API

In addition, VGL provides **a unified interface for porting VGL-based implementations for other architectures with 
high-bandwidth memory**, such as NVIDIA GPUs. With cross-platform API VGL-based implementations can be launched 
on different architectures (currently NEC SX-Aurora TSUBASA and NVIDIA GPUs) with only minor code modifications 
(for example inserting __device__ directive before lambda functions).

### Using VGL

1. [Download](./VectorGraphLibrary.zip) and [install](./installation.html) VGL.

2. Check VGL [samples](./bfs_example.html) to learn more about how different graph algorithms 
can be expressed via VGL data abstractions.

3. Check [documentation](./documentation.html) to learn more about VGL computational and its data abstractions.

4. Check to learn more about VGL [performance](./performance.html) compared to existing state-of-the-art 
multicore CPU and NVIDIA GPU graph-processing frameworks and libraries.

5. If you plan using VGL for graph analytics, please [cite](./cite.html) our works. 
