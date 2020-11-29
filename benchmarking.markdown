---
layout: default
---

VGL provides bash scripts, which automatically benchmark different graph applications (BFS, PR, SSSP, CC, etc.), implemented in VGL.
In order to benchmark all applications, the following commands should be used:

1. > cd VectorGraphLibrary/apps/bin

2. > chmod 777 ./testing_scripts/

3. > ./testing_scripts/prepare_vgl_real_world_graphs.sh
   
This script downloads several real-world graphs from SNAP collection and converts
them into preprocessed VGL format. VGL graphs are saved into ./apps/bin/input_graphs folder, while 
SNAP source graphs are saved into ./apps/bin/source_graphs folder.
 
4. > ./testing_scripts/prepare_vgl_synthetic_graphs.sh
   
This script generates synthetic RMAT and uniform random real-world graphs and converts
them into preprocessed VGL format. VGL graphs are saved into ./apps/bin/input_graphs folder.

5. > ./testing_scripts/verify_all_apps.sh
   
This script verifies the correctness of VGL installation. As an output it generates nec_check.csv file,
which can be imported in excel.
![alt text]({{site.data_url}}/assets/img/check_table.png)

6. > ./testing_scripts/benchmark_all_apps.sh
   
This script benchmarks all graph applications, currently implemented in VGL.
As an output it generates nec_stats.csv file, which can be imported in excel.
![alt text]({{site.data_url}}/assets/img/performance_table.png)