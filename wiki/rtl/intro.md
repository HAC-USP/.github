# OPAE - Open Programmable Accelerator Environment

OPAE is an abstraction layer built on top of PAC FPGA drivers, facilitating communication between host devices and FPGA accelerators.

## Usage on Mintrop

- **No Sudo Required**: On Mintrop, running the `fpgainfo` tool can identify the PR Interface ID (FIM) and BMC firmware currently loaded.

## Installation and Directories

- OPAE is installed in `/usr/src/opae` on Mintrop, serving as our host device communication tool.
- The SDK for OPAE is located at `/opt/inteldevstack/a10_gx_pac_ias_1_2_1_pv/`.

## Components

- **AFU (Accelerator Functional Unit)**: Modules acting as accelerators.
- **ASE (AFU Simulation Environment)**: Used for AFU production workflow. Example tutorials are available [here](https://github.com/OFS/examples-afu/tree/main/tutorial).
  - `env_check.sh` script: Location - `/opt/inteldevstack/a10_gx_pac_ias_1_2_1_pv/sw/opae-1.1.2-2/ase/scripts`

## ModelSim_DE

ModelSim_DE is an RTL simulator available for use. It can be accessed by loading the ModelSim module and used with Quartus under "Tools > Run Simulation Tool > RTL Simulation".

## CCI-P

- **Core Cache Interface Protocol (CCI-P)**: One of the protocols managing PCIe barrier operations.
