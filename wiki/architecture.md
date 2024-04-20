# Analysis of Bottlenecks in Stencil Architecture using OpenCL-Based FPGA Platform for Stencil Computation and Its Optimization Methodology on Arria 10

In our analysis of the proposed stencil architecture, we examine the potential bottlenecks and optimization strategies for the OpenCL-based FPGA platform targeting stencil computation on the Arria 10.

## Bottleneck Analysis

We begin by assessing the key parameters impacting performance:

- **Required Bandwidth (Breq)**:
  - Defined as the product of (Sinput + Soutput) * Pcell * Fcore, where Pcell = 1 and Sinput = Soutput = 4 for float (32 bits) data type.
  - With a core frequency (Fcore) of 240 MHz, Breq is calculated to be 1920 Mbytes/s.

- **Memory Interface Bandwidth**:
  - The Arria 10 datasheet specifies 7 DDR4 memory interfaces, each with a 32-bit width and a frequency of 2400 Mbps.
  - Assuming 100% efficiency, this translates to a bandwidth of 300 Mbytes/s. Considering a more realistic efficiency of 80%, the effective bandwidth reduces to 240 Mbytes/s.

## Scenario Assessment

Based on the calculated values, we find ourselves in scenario 2 of computation time, where the required bandwidth exceeds the memory bandwidth.

- **Impact of Sequential Memory Access**:
  - Using a "naive" sequential memory access method, the algorithm could be 8x slower. However, leveraging all 7 memory interfaces in parallel could reduce this slowdown to approximately 1.1429x.

- **Execution Time Equation**:
  - The total execution time (t) is determined by the equation: t = Itotal * Fmem * ((q - 1) * N^2 + N * Md), where Itotal represents the total iterations, Fmem denotes the memory frequency, q represents the ratio of Breq to memory bandwidth, N signifies the grid size, and Md represents the memory access latency.

## Observations and Considerations

- **Dynamic Clock Frequency Adjustment**:
  - In scenario 2, reducing the clock frequency does not affect the accelerator's execution time, offering potential energy efficiency gains.

- **Temporal Parallelism**:
  - Until the terms within the parentheses become similar in size, temporal parallelism serves as the primary source of computation time reduction.

- **Memory Bandwidth Optimization**:
  - Exploring methods to reduce bandwidth requirements per cycle, such as tiling, could significantly enhance performance.

- **Internal Memory Utilization**:
  - Optimizing the design to utilize internal FPGA memory resources, such as M20K or MLAB, offers potential performance gains. However, this approach may limit grid size due to the smaller capacity of internal memory compared to external memory.

## Stratix 10 Comparison

Considering the Stratix 10, which offers a bandwidth of 2666 Mbps across 10 interfaces, the possibility of utilizing all interfaces to overcome bandwidth limitations arises. However, it's crucial to weigh the benefits against optimizing kernel design to leverage internal FPGA memory resources for potentially greater performance gains.

