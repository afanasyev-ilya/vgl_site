---
layout: default
---

Please, use the following installation instructions to start using VGL.

## Requirements

In order to use VGL you need to install:
* GCC compiler, version >= 8.3.0
* NCC compiler, version >= 3.1.0
* ASL (Advanced Scientific Library), version 2.0.0

* _(additional for GPU API)_ In order to use VGL GPU API you must also have CUDA toolkit, version >= 10.0 

## Installation

##### NEC SX-Aurora TSUBASA API
To start using VGL, please, follow the steps.

1. Download VGL:

    - source files from GitHub:
        > git clone https://github.com/afanasyev-ilya/VectorGraphLibrary

    - or .tar arcieve from this website:

        > wget https://afanasyev-ilya.github.io/vgl_site/VectorGraphLibrary.zip
        >                               
        > wget https://afanasyev-ilya.github.io/vgl_site/VectorGraphLibrary.tar.gz

2. Check sample applications in _apps_ folder:

    > cd VectorGraphLibrary/apps
    >
    > vim bfs/bfs.cpp

3. Modify Makefile.nec if neccesarry:
    > vim Makefile.nec
    
    you may change compiler paths:
    > VE_CXX = nc++
    > 
    > VH_CXX = g++
    
    or the path to ASL:
    > -I /opt/nec/ve/nlc/2.0.0/include/

4. Build VGL samples:
    - all sample applications:
        > make -f Makefile.nec all
    
    - or a specific application (for example shortest paths):
        > make -f Makefile.nec sssp

##### NVIDIA GPU API

## Start using VGL

To start using VGL, please check our introduction guides: 

- Developing first algorithm using VGL: [Breadth-First Search](./bfs_example.html)
