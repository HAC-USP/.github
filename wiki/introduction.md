# Introduction

## Accelerating Algorithms

The aim of this document is to situate ourselves amidst the complexity of this project. There is a wealth of information swirling around in our minds, yet a lack of clarity on how to proceed. A good way to start is by succinctly answering the question: what are we doing?

### What We Are Doing

In general terms, we are accelerating the seismic inversion algorithm using FPGAs. We aim to investigate the possibility of surpassing GPUs (Why GPUs?) either in performance (GFlops) or in energy efficiency (GFlop/Watt).

Specifically, we are currently implementing wave propagation in the acoustic model (a step in the seismic inversion algorithm) on the FPGA using a framework for programming heterogeneous platforms called Intel's oneAPI. We will then compare the performance with optimized versions of wave propagation for GPU and CPU.


