# AF Development Flow (Mintrop)

## What is an AF? - Accelerator Function
Compiled Hardware Accelerator image implemented in FPGA logic that accelerates an application. An AFU and associated AFs may also be referred to as **GBS** (Green-Bits, Green BitStream). GBS's are important and are the main offload of this Flow.

## What is an AFU? - Accelerator Functional Unit
AFU is the Hardware Accelerator described in FPGA logic, which is not a GBS yet. Basically the respective RTL or HLS for the accelerator.

[See this guide for more information](https://www.intel.com/content/www/us/en/docs/programmable/683633/1-2-1/about-this-document.html#sun1522441132364)


## The Flow

### Introduction
First, we need to get in touch with our tools. [OPAE](https://opae.github.io/1.0.2/docs/fpga_tools/fpgainfo/fpgainfo.html) and [ASE](https://opae.github.io/1.0.2/docs/ase_userguide/ase_userguide.html). They both are abstractions that runs Quartus Pro behind it and manages the Host-Device communication and bus ports. ASE is important to simulate the AFU's, for this we need to use ModelSim (We have it on Mintrop!). 

*TODO: Explore ModelSim'*


To setup mintrop environment run the following commands:
```bash
module load intel/quartus_pro/19.2 #The version must be the 19.2, the 20.4 does not work
module load openssl-1.1.1n-oneapi-2022.0.2-sj7edwk #OpenSSL 
export OPAE_PLATFORM_ROOT="/opt/inteldevstack/a10_gx_pac_ias_1_2_1_pv" #init_env.sh does not work properly
```

### Structuring the AFU
Further then the description of the accelerator circuit, to interact with the bus is required to have the top level description of them. They are pre-build and we can find them at samples in OPAE root. Further on, we need a JSON file that will be used by OPAE following this template:

```json
{
   "version": 1,
   "afu-image": {
      "power": 0,
      "afu-top-interface":
         {
            "class": "ccip_std_afu"
         },
      "accelerator-clusters":
         [
            {
               "name": "hello_afu",
               "total-contexts": 1,
               "accelerator-type-uuid": "850adcc2-6ceb-4b22-9722-d43375b61c66"
            }
         ]
   }
}

```

accelerator-type-uuid must be unique.

These above are the hardware part that will be synthesized for the FPGA.
Now we need to have a software layer that runs in the Host and interacts with the device. A .c file that explicitly manages the memory addresses. Samples are useful to this as well.


### Compiling the bitstream

Before running OPEA, there is another step. Creating a source.txt file that contains the path of all RTL files needed to the simulation or synthesis. The path of the json can be added to this file as well.

Then in hw folder:

```bash
afu_sim_setup --source source.txt build_sim #For simulation
afu_synth_setup --source source.txt build_synth #For synthesis 
#build_sim and build_synth are placeholders for the directory that will be created
```

*TODO: Explore Simulation*

afu_synth_setup runs quartus behind it, so the module must be loaded before. 

After quartus performs all steps a .gbs file will be generated. This is the Green BitStream, the archive needed to partially reconfigure (PR) the chip. But this is not enough to load onto a FPGA.

### Runing Quartus

After running afu_synth_setup, it's time for Quartus Synthesis:

```bash
${OPAE_PLATFORM_ROOT}/bin/run.sh
```

### Signing the BitStream

The BitStreams loaded in PAC boards must be signed for a security purpose. [Check it here](https://www.intel.com/content/www/us/en/docs/programmable/683453/current/overview.html)

If is your first compilation, you may create a OpenSSL key pair for signing it.

Assure that OpenSSL module is loaded.

#### Creating SSL Keys

Create the root private key:
```bash
openssl ecparam -name secp256r1 -genkey -noout \
-out key_pr_root_private_key.pem
```

Create the root public key:
```bash
openssl ec -in key_pr_root_private_key.pem -pubout \
-out key_pr_root_public_key.pem
```

Create private CSK1:
```bash
openssl ecparam -name secp256r1 -genkey -noout \
-out key_pr_csk1_private_key.pem
```

Create public CSK1
```bash
openssl ec -in key_pr_csk1_private_key.pem -pubout \
-out key_pr_csk1_public_key.pem
```

#### Signing Images

The PACSign program can be used with OpenSSL keys, so the last step is imperative. If you do not have the key pair, go back and create it.

```bash
PACSign PR -t UPDATE -H openssl_manager \
-r key_pr_root_public_key.pem -k key_pr_csk1_public_key.pem -i hello_afu.gbs \
-o hello_afu_signed_ssl.gbs
```

*hello_afu is a placeholder*


#### Loading Images

2 different OPEA application can be used for this task, I don't know the differences properly yet - fpgasupdate or fpgaconf fp. Mintrop has two FPGA devices, which we can check information running:

```bash
fpgainfo fme #Id informations of each board
fpgainfo temp #Temperature
fpgainfo power #Enenrgy Consumption
```

In advance the two Id buses of the boards are:
 - 0000:86:00.0
 - 0000:D8:00.0

```bash
fpgasupdate hello_afu_signed_ssl.gbs 86:00.0
fpgaconf -B 0x86 hello_afu_signed_ssl.gbs
```

### Compiling the Software Layer

The MakeFile below has some paths for essential includes and was made merging and editing some examples that I found. Works.

<details>
  <summary>Click to expand MakeFile</summary>

```MakeFile
  ##
## Common sw build rules
##

COPT     ?= -g -O2
CPPFLAGS ?= -std=c++11
CXX      ?= g++
LDFLAGS  ?=

ifeq (,$(CFLAGS))
CFLAGS = $(COPT)
endif

ifneq (,$(ndebug))
else
CPPFLAGS += -DENABLE_DEBUG=1
endif
ifneq (,$(nassert))
else
CPPFLAGS += -DENABLE_ASSERT=1
endif

# stack execution protection
LDFLAGS +=-z noexecstack

# data relocation and projection
LDFLAGS +=-z relro -z now

# stack buffer overrun detection
# Note that CentOS 7 has gcc 4.8 by default.  When we switch
# to a system with gcc 4.9 or newer this should be changed to
# CFLAGS="-fstack-protector-strong"
CFLAGS +=-fstack-protector

# Position independent execution
CFLAGS +=-fPIE -fPIC
LDFLAGS +=-pie

# fortify source
CFLAGS +=-D_FORTIFY_SOURCE=2

# format string vulnerabilities
CFLAGS +=-Wformat -Wformat-security

ifeq (,$(DESTDIR))
ifneq (,$(prefix))
CFLAGS   += -I$(prefix)/include
CPPFLAGS += -I$(prefix)/include
LDFLAGS  += -L$(prefix)/lib -Wl,-rpath-link -Wl,$(prefix)/lib -Wl,-rpath -Wl,$(prefix)/lib \
            -L$(prefix)/lib64 -Wl,-rpath-link -Wl,$(prefix)/lib64 -Wl,-rpath -Wl,$(prefix)/lib64
endif
else
ifeq (,$(prefix))
prefix = /usr/local
endif
CFLAGS   += -I$(DESTDIR)$(prefix)/include
CPPFLAGS += -I$(DESTDIR)$(prefix)/include
LDFLAGS  += -L$(DESTDIR)$(prefix)/lib -Wl,-rpath-link -Wl,$(prefix)/lib -Wl,-rpath -Wl,$(DESTDIR)$(prefix)/lib \
            -L$(DESTDIR)$(prefix)/lib64 -Wl,-rpath-link -Wl,$(prefix)/lib64 -Wl,-rpath -Wl,$(DESTDIR)$(prefix)/lib64
endif

LDFLAGS += -luuid

FPGA_LIBS = -lopae-c
ASE_LIBS = -lopae-c-ase

##
## Placeholders below - Editing is required
##

# Primary test name
TEST = hello_afu #PlaceHolder

# Build directory
OBJDIR = obj
CFLAGS += -I./$(OBJDIR)
CPPFLAGS += -I./$(OBJDIR)

# Files and folders
SRCS = $(TEST).c
OBJS = $(addprefix $(OBJDIR)/,$(patsubst %.c,%.o,$(SRCS)))

all: $(TEST)

# AFU info from JSON file, including AFU UUID
AFU_JSON_INFO = $(OBJDIR)/afu_json_info.h #PlaceHolder
$(AFU_JSON_INFO): ../hw/$(TEST).json | objdir #PlaceHolder
	afu_json_mgr json-info --afu-json=$^ --c-hdr=$@
$(OBJS): $(AFU_JSON_INFO)

$(TEST): $(OBJS)
	$(CC) -o $@ $^ $(LDFLAGS) $(FPGA_LIBS) -lrt

$(OBJDIR)/%.o: %.c | objdir
	$(CC) $(CFLAGS) -c $< -o $@

clean:
	rm -rf $(TEST) $(OBJDIR)

objdir:
	@mkdir -p $(OBJDIR)

.PHONY: all clean

```

</details>


TODO: Explore more examples and continue documentation