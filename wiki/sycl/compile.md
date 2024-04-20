# FPGA Compilation Commands

Below are the commands for compiling FPGA code and generating reports:

## FPGA Image (Takes hours)
```bash
icpx -fsycl -fintelfpga -Xshardware -Xstarget=intel_a10gx_pac:pac_a10 main.cpp -o fpga_compile.fpga
```

## FPGA Emulator (Takes around 1 minute)
```bash
icpx -fsycl -fintelfpga main.cpp -o fpga_emu
```

## FPGA Reports (Takes a few minutes)
```bash
icpx -fsycl -fintelfpga -Xshardware -Xsauto-pipeline -fsycl-link=early -Xstarget=Arria10 main.cpp -o fpga_design_report.a
```

[References and Other Flags](https://www.intel.com/content/www/us/en/docs/oneapi/programming-guide/2023-0/fpga-compilation-flags.html)