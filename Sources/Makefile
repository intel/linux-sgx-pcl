#
# Copyright (C) 2011-2017 Intel Corporation. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
#   * Redistributions of source code must retain the above copyright
#     notice, this list of conditions and the following disclaimer.
#   * Redistributions in binary form must reproduce the above copyright
#     notice, this list of conditions and the following disclaimer in
#     the documentation and/or other materials provided with the
#     distribution.
#   * Neither the name of Intel Corporation nor the names of its
#     contributors may be used to endorse or promote products derived
#     from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR
# A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT
# OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL,
# SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
# LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE,
# DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY
# THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#
#

# PCL library makefile

ifndef $(VERBOSE)
	VERBOSE	:= @
endif

######## Verify environment: ########

ifeq ($(PCL_DIR),)
$(error PCL_DIR environment variable must be set!!!) 
endif

ifeq ($(SGX_SDK_SRCS),)
$(error SGX_SDK_SRCS environment variable must be set to home directory of SGX SDK source files!!!) 
endif

######## SGX SDK Settings ########

# location of SDK include, bin, lib folders
# SGX_SDK defined - build with SDK install (default: /opt/intel/sgxsdk)
ifeq ($(SGX_SDK),)
$(error SGX_SDK environment variable must be set to SGX SDK root directory (e.g. /opt/intel/sgxsdk) !!!) 
endif

# build configuration default settings HW, x64, debug
# SGX_MODE ?= HW default required for clean
SGX_MODE ?= HW
SGX_ARCH ?= x64
DEBUG ?= 0

# 32 bit build currently not supported on linux
ifeq ($(shell getconf LONG_BIT), 32)
	$(error x86 build is not supported, only x64!!)
else ifeq ($(findstring -m32, $(CXXFLAGS)), -m32)
	$(error x86 build is not supported, only x64!!)
endif

ifeq ($(SGX_ARCH), x86)
	$(error x86 build is not supported, only x64!!)
else
	SGX_COMMON_CFLAGS := -m64
endif

ifeq ($(DEBUG), 1)
        SGX_COMMON_CFLAGS += -O0 -g
else
        SGX_COMMON_CFLAGS += -O2
endif

######## Enclave Settings ########

# output dir for simulation build objects
SIM_DIR				:= simulation/

# library names
ifneq ($(SGX_MODE), HW)
SIM					:= sim
OBJ_DIR				:= $(SIM_DIR)
endif

PCL_SIM_LIB_NAME	:= sgx_pclsim
PCL_LIB_NAME		:= sgx_pcl$(SIM)
PCL_SIM_LIB			:= $(PCL_DIR)/lib64/lib$(PCL_SIM_LIB_NAME).a
PCL_LIB				:= $(PCL_DIR)/lib64/lib$(PCL_LIB_NAME).a

# source files
PCL_ASM_FILES		:=    \
    crypto/pcl_vpaes-x86_64.s \
    crypto/pcl_ghash-x86_64.s

PCL_CPP_FILES		:=            \
    pcl_entry.cpp                 \
    pcl_mem.cpp                   \
    crypto/pcl_crypto.cpp         \
    unseal/pcl_tSeal.cpp          \
    unseal/pcl_sgx_get_key.cpp    \
    unseal/pcl_tSeal_util.cpp     \
    unseal/pcl_tSeal_internal.cpp

PCL_C_FILES		:= \
    crypto/pcl_sha256.c    \
    crypto/pcl_gcm128.c 

# simulation mode only
PCL_SIM_C_FILES		:= crypto/pcl_cmac.c
PCL_SIM_CPP_FILES	:= unseal/sim/pcl_deriv.cpp unseal/sim/pcl_t_instructions.cpp

ifneq ($(SGX_MODE), HW)
	PCL_C_FILES	+= $(PCL_SIM_C_FILES)
	PCL_CPP_FILES	+= $(PCL_SIM_CPP_FILES)
endif 

# object files
PCL_CPP_OBJECTS			:= $(PCL_CPP_FILES:%.cpp=$(OBJ_DIR)%.o)
PCL_C_OBJECTS			:= $(PCL_C_FILES:%.c=$(OBJ_DIR)%.o)
PCL_ASM_OBJECTS			:= $(PCL_ASM_FILES:%.s=$(OBJ_DIR)%.o)

# simulation objects required for clean
PCL_SIM_CPP_OBJECTS		:= $(PCL_SIM_CPP_FILES:%.cpp=$(OBJ_DIR)%.o)
PCL_SIM_C_OBJECTS		:= $(PCL_SIM_C_FILES:%.c=$(OBJ_DIR)%.o)

# build flags
PCL_INCLUDE_PATH	:= -I$(PCL_DIR)/include \
			-I$(PCL_DIR)/common \
			-I$(PCL_DIR)/Sources/crypto \
			-I$(PCL_DIR)/Sources/unseal \
			-I$(SGX_SDK)/include \
			-I$(SGX_SDK)/include/tlibc \
			-I/usr/include/x86_64-linux-gnu \
			-I/usr/include \
			-I./

PCL_LIB_C_FLAGS := $(SGX_COMMON_CFLAGS) -nostdinc -fvisibility=hidden -fpie -fstack-protector $(PCL_INCLUDE_PATH) -I$(SGX_SDK_SRCS)/common/inc/internal
ifneq ($(SGX_MODE), HW)
	PCL_LIB_C_FLAGS += -DSE_SIM=1 -I$(SGX_SDK_SRCS)/sdk/simulation/tinst -I$(SGX_SDK_SRCS)/common/inc/internal
endif

PCL_LIB_CPP_FLAGS := $(PCL_LIB_C_FLAGS) -std=c++03 -nostdinc++

# targets
.PHONY: all

all:
	$(VERBOSE)$(MAKE) SGX_MODE=HW build
	$(VERBOSE)$(MAKE) SGX_MODE=SIM build

build: $(PCL_LIB)

######## PCL Lib ################

OBJCOPY := objcopy
NIPX := .nipx
NIPD := .nipd
NIPRODT := .niprod

$(OBJ_DIR)%.o: %.s
	$(VERBOSE)mkdir -p $(@D)
	$(VERBOSE)$(CXX) $(PCL_LIB_C_FLAGS) -c $< -o $@ 
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .text=$(NIPX)
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .data=$(NIPD)
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .rodata=$(NIPRODT)
	@echo "CXX  <=  $<"

$(OBJ_DIR)%.o: %.c
	$(VERBOSE)mkdir -p $(@D)
	$(VERBOSE)$(CXX) $(PCL_LIB_C_FLAGS) -c $< -o $@
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .text=$(NIPX)
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .data=$(NIPD)
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .rodata=$(NIPRODT)
	@echo "CXX  <=  $<"

$(OBJ_DIR)%.o: %.cpp
	$(VERBOSE)mkdir -p $(@D)
	$(VERBOSE)$(CXX) $(PCL_LIB_CPP_FLAGS) -c $< -o $@
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .text=$(NIPX)
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .data=$(NIPD)
	$(VERBOSE)$(OBJCOPY) $@ --rename-section .rodata=$(NIPRODT)
	@echo "CXX  <=  $<"

$(PCL_LIB): $(PCL_CPP_OBJECTS) $(PCL_ASM_OBJECTS) $(PCL_C_OBJECTS)
	@echo "library build with DEBUG =" $(DEBUG)
	$(VERBOSE)ar -rc $@ $^
	@echo "AR   <=  $@"

.PHONY: clean

clean:
	$(VERBOSE)rm -f $(PCL_LIB) $(PCL_SIM_LIB) $(PCL_CPP_OBJECTS) $(PCL_ASM_OBJECTS) $(PCL_C_OBJECTS) $(PCL_SIM_CPP_OBJECTS) $(PCL_SIM_C_OBJECTS)
	$(VERBOSE)rm -rf  $(SIM_DIR)
