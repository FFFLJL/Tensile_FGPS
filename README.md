This repo contains code for the paper [A Fine-grained Prefetching Scheme for DGEMM Kernels on GPU with Auto-tuning Compatibility](https://ieeexplore.ieee.org/document/9820693)

AMD auto-tuning for creating a benchmark-driven backend library for GEMMs, GEMM-like problems (such as batched GEMM), N-dimensional tensor contractions, and anything else that multiplies two multi-dimensional objects together on a GPU.

See [Tensile Wiki](https://github.com/RadeonOpenCompute/Tensile/wiki) for documentation.

We design a fine-grained prefetching scheme(FGPS) which improves the thread level parallelism by balancing the usage of VGPR resource, Then we integrate FGPS into AMD auto-tuning tool--Tensile base on v4.17.0, so that the DGEMM sizethat benefits from FGPS can automatically generate betterassembly kernel.

One can add a line '-FGPS=\[False, True\]' to the yaml configure file.

This v1.0 now only support the '-ThreadTile=\[4\*4, 6\*4, 4\*6\] '.

