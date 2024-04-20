# Project Progress and Challenges

## First Steps

### Initial Implementation

The project commenced with the implementation of two-dimensional acoustic wave propagation in OpenCL by Lucas Cilento. Initially, the code was extended to operate in a three-dimensional grid. Enhancements were made to the OpenCL kernel to incorporate support for wave sources using the Ricker wavelet formula and microphones. Additionally, tools for visualizing the results of wave propagation, such as the generated pressure field and microphone temporal series, were developed. João Pedro Belga contributed to the implementation of checkpoint usage to save intermediate results, beneficial for subsequent stages of seismic inversion.

### Challenges and Realizations

However, despite successful emulation, the translation of implemented features into FPGA design and efficient host-kernel interaction, particularly regarding checkpoints, proved problematic. This raised concerns about our software development methodology. Subsequent attempts to synthesize the design for FPGA failed, revealing flaws in our approach.

Professor Edson S. Gomi suggested transitioning our OpenCL kernel to OneAPI. However, transitioning proved challenging due to compiler issues on Intel Devcloud, our development environment. Initially attributing the failures to our code, it became apparent that the compiler was at fault. Confirmation came after failing to compile a vector addition example and seeking assistance on the Intel development forum.


### Transition to OneAPI

Upon resolving compiler issues, we successfully implemented a OneAPI kernel equivalent to the three-dimensional OpenCL kernel, utilizing functors and Unified Shared Memory (USM) for memory management.

## New Ambience

### Comparative Analysis

Intense efforts were invested over four days to collect comparative data between our optimized OneAPI FPGA kernel and adapted OneAPI GPU and CPU kernels sourced from an official OneAPI acoustic wave propagation example. Automation scripts were developed for job scheduling and execution on Intel Devcloud, facilitating compilation and runtime measurement for various kernel configurations on FPGA (Arria 10), CPU (i9 and Xeon Gold), and GPU (UHD and Data Center Max).

### Realization and Further Analysis

Results revealed a significant oversight regarding FPGA design behavior. Expectations of performance improvements with increased iterations, eventually matching GPU and CPU performance, were not met, prompting further investigation into the discrepancies.

### Error Analysis

Upon encountering errors, initial conjectures centered around algorithm expansion issues in three dimensions. However, further investigation and study revealed otherwise, prompting a shift in focus.

### Synthesis Report Analysis

To gain deeper insights, synthesis reports of the implemented architecture were meticulously examined, revealing discrepancies in initial assumptions. This pivotal realization marked the commencement of our journey into RTL development.

## Transition Challenges

### Compiler Compatibility

Efforts to convey our intentions to the icpx compiler proved challenging, leading us to seek alternative strategies.

### Hybrid Approach

Following Professor Edson S. Gomi's suggestion, we opted to segregate the algorithm, developing some parts in HLS (SYCL) and core components in RTL (SystemVerilog). While SYCL serves to simplify and mitigate the need for RTL, the inevitability of HDLs persists for the time being.

## New Environment

### Mintrop

While the Intel DevCloud served its purpose thus far, the evolution of our ideas involving RTL necessitates a more controlled development environment to facilitate our next steps. Hence, the introduction of Mintrop, a cluster hosted by NDF-USP (Núcleo de Dinâmica do Fluidos), boasting two Altera Arria 10 PAC FPGAs.

### Infrastructure Challenges

Upon transitioning to the new environment, we realized that our time was predominantly consumed by configuring infrastructure rather than developing the architecture itself. This realization prompted us to halt any further readings on algorithms or hardware engineering, redirecting our focus entirely towards infrastructure. Despite facing more challenges than anticipated, diligent effort eventually led to the successful configuration and documentation of our environment.

### RTL, Finally

The next step in our journey entails delving into RTL development. However, understanding how Quartus behaves with accelerators is imperative. It's evident that HLS offers more abstraction and requires less effort and understanding of complete synthesis. (However, it's important to note that it's not without its own challenges; it's simply less daunting than RTL.)

### OPEA, AFU, AF, ASE

Navigating through a sea of acronyms, we discovered our solution: a comprehensive development kit for PAC accelerators featuring a Xeon CPU, known as OPEA. And here we are, embarking on a journey of experimentation, study, and testing. While we have yet to implement a solid architecture, we have proof of concept for our key points. Stay tuned for updates.
