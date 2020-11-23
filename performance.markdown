---
layout: default
---

Performance of VGL-based implementations has been evaluated against various existing CPU- and GPU-based 
graph-processing frameworks and libraries.

**Page Rank performance**
![Branching]({{site.url}}/assets/img/vgl_pr_perf.png)

**Single Source Shortest Paths performance**
![Branching]({{site.url}}/assets/img/vgl_sssp_perf.png)

**Connected Components performance**
![Branching]({{site.url}}/assets/img/vgl_cc_perf.png)

**Breadth-First Search performance**
![Branching]({{site.url}}/assets/img/vgl_bfs_perf.png)

VGL achieves up to 14 times acceleration compared to multicore CPU implementations, such as 
Ligra, Galois, and GAPBS graph libraries. 
Such a significant performance difference is caused by different peak memory bandwidth values
for these platforms: 90 GB/s for Intel Xeon versus 1.2 TB/s for NEC SX-Aurora TSUBASA.
The fact that the performance difference is approximately proportional to bandwidths
values proves the potential of using systems with high-bandwidth memory for graph applications.


In most cases VGL achieves up to 3x better performance compared to modern GPU implementations, launched on V100 GPUs,
such as Gunrock, NVGRAPH and cuSHA.
Such acceleration can be explained by the combination of the following factors. 
First, SX-Aurora TSUBASA has a slightly higher theoretical memory bandwidth, which, 
of course, contributes to the performance difference. Second, VGL uses graph preprocessing 
techniques (clus- terization), which allows to significantly increase LLC hit rate 
when processing indirect memory accesses, and in addition, improves parallel workload balancing.
frameworks, such as Gunrock, do not use preprocessing-based optimisations. 
Finally, SX-Aurora TSUBASA has a significantly larger LLC cache (compared to NVIDIA GPUs), 
which allows to store a significant part of most frequently accessed graph vertices, 
since all the graphs used for the performance evaluation are scale-free. Unfortunately, 
it is hard to conclude which factor provides a higher contribution to the performance increase.


The performance results have been obtained on the cluster equipped with: 
(1) 12-core Intel(R)Xeon(R) Gold 6126 CPU of Intel Skylake architecture,
(2) NVIDIA V100 GPU of Volta architecture and (3) Vector Engine SX-Aurora TSUBASA Type 10B, 
installed in different cluster partitions. 