# Improvement Ideas

## Introduction

In this section, we outline various improvement ideas aimed at enhancing our project's efficiency, performance, and development process. These ideas range from optimizing execution time measurement to exploring memory movement and leveraging CI/CD practices.

### Key Areas of Focus

1. **Standardized Execution Time Measurement**:
   Implement a standardized method for measuring kernel execution time, such as writing SYCL programs separately and ensuring their entry points return execution information. This approach will facilitate large-scale performance testing for different kernel parameterizations.

2. **Memory Movement Testing**:
   Conduct basic memory movement tests, such as performing trivial operations on matrices and analyzing the oneAPI report to identify fundamental performance limitations.

3. **SYCL Stencil Code Analysis**:
   Search for open-source SYCL code implementing stencils, analyze its performance, and gather inspiration to improve the performance of our shift register architecture.

4. **Continuous Integration/Continuous Deployment (CI/CD)**:
   Develop a compilation script that automatically checks if the propagation results are correct using a base (e.g., simwave). This ensures that code changes do not inadvertently break expected functionality, providing confidence for future modifications.

5. **CPU Compression Before FPGA Transfer**:
   Investigate the feasibility of compressing data on the CPU before transferring it to the FPGA. Determine the optimal point where the compression time outweighs the transport time, considering the grid size as a factor.

6. **Precompute dt²/delta**:
   Explore the precomputation of dt²/delta for dx = dy = dz to optimize performance.

## Next Steps

Feel free to explore these improvement ideas and contribute your insights and experiences to further enhance our project!

