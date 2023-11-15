This repo contains code for the paper [A Fine-grained Prefetching Scheme for DGEMM Kernels on GPU with Auto-tuning Compatibility](https://ieeexplore.ieee.org/document/9820693)

AMD auto-tuning for creating a benchmark-driven backend library for GEMMs, GEMM-like problems (such as batched GEMM), N-dimensional tensor contractions, and anything else that multiplies two multi-dimensional objects together on a GPU.

See [Tensile Wiki](https://github.com/RadeonOpenCompute/Tensile/wiki) for documentation.

We design a fine-grained prefetching scheme(FGPS) which improves the thread level parallelism by balancing the usage of VGPR resource, Then we integrate FGPS into AMD auto-tuning tool -- base on [Tensile v4.17.0](https://github.com/ROCmSoftwarePlatform/Tensile/tree/master-rocm-2.9), so that the DGEMM sizethat benefits from FGPS can automatically generate betterassembly kernel.


One can add a line '-FGPS=\[False, True\]' to the yaml configure file.

This v1.0 now only support the '-ThreadTile=\[4\*4, 6\*4, 4\*6\] '.

```
GlobalParameters:
  MinimumRequiredVersion: 4.9.0
  PrintLevel: 2
  ForceRedoBenchmarkProblems: True
  ForceRedoLibraryLogic: True
  ForceRedoLibraryClient: True
  CMakeBuildType: Release
  EnqueuesPerSync: 1
  SyncsPerBenchmark: 2
  LibraryPrintDebug: False
  NumElementsToValidate: 0
  ValidationMaxToPrint: 4
  ValidationPrintValids: False
  ShortNames: False
  MergeFiles: True
  Platform: 0
  Device: 0
  KernelTime: True
  PinClocks: False
  SleepPercent: 0
  DataInitTypeBeta : 2
  CEqualD: True

BenchmarkProblems:
  -#DGEMM
    - # ProblemType
      OperationType: GEMM
      DataType: d
      TransposeA: True
      TransposeB: True
      UseBeta: True
      Batched: True

    - # BenchmarkProblemSizeGroup - k=256,384
      InitialSolutionParameters:
      BenchmarkCommonParameters:
        - BufferLoad: [True]
        - BufferStore: [True]
        - KernelLanguage: ["Assembly"]
        - EdgeType: ["ShiftPtr"]
        - LoopTail: [True]
      ForkParameters:
        - FractionalLoad: [1, 2]
        - PrefetchGlobalRead: [True]
        - PrefetchLocalRead: [True]
        - PersistentKernel: [0]
        - SuppressNoLoadLoop: [True, False]
        - StaggerU: [0, 8, 16, 32]
        - StaggerUStride: [ 128, 256 ]
        - ThreadTile:
          - [ 4, 4 ]
          - [ 4, 6 ]
          - [ 6, 4 ]
        - WorkGroup:
          - [ 32, 16,  1 ]
          - [ 16, 16,  1 ]
          - [ 16, 32,  1 ]
        - WorkGroupMapping: [1, 8]
        - DepthU: [ 8 ]
        - LdsPadA: [-1, 0, 1, 2]
        - LdsPadB: [-1, 0, 1, 2]
        - VectorWidth: [-1]
        - FGPS: [True, False]
      BenchmarkForkParameters:
      JoinParameters:
      BenchmarkJoinParameters:
      BenchmarkFinalParameters:
        - ProblemSizes:
          - Exact: [ 16384, 128, 1, 128 ]

LibraryLogic:

LibraryClient:
```
