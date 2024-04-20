# Compilation Job Creation in Devcloud

To create a compilation job from a script in the current directory:

```bash
qsub -l nodes=1:fpga_runtime:arria10:ppn=2 -d . <script_name>
```

If using a Stratix, change the arria10 attribute in the above command to stratix10. If your code uses USM, use the command aocl initialize acl0 pac_s10_usm.

## Monitoring Active Jobs

```bash
watch -n 1 qstat -n -1
```