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

ifndef $(VERBOSE)
	VERBOSE	:= @
endif

######## Verify environment variables ########

ifeq ($(PCL_DIR),)
$(error PCL_DIR environment variable must be set!!!) 
endif

ifeq ($(OPENSSL_ROOT),)
$(error OPENSSL_ROOT environment variable must be set to root directory of OpenSSL 1.1.0g !!!) 
endif

######## SGX SDK Settings ########

# location of SDK include, bin, lib folders
# SGX_SDK defined - build with SDK install (default: /opt/intel/sgxsdk)
ifeq ($(SGX_SDK),)
$(error SGX_SDK environment variable must be set to SGX SDK root directory (e.g. /opt/intel/sgxsdk) !!!) 
endif

SGX_LIBRARY_PATH 	:= $(SGX_SDK)/lib64
SERVICE_LIBRARY_NAME 	:= sgx_tservice

######## App Settings ########

APP_INCLUDE_PATH	:=  -I$(OPENSSL_ROOT)/include -I$(PCL_DIR)/include -I$(PCL_DIR)/Tools/Encryptip -I$(PCL_DIR)/common -I$(SGX_SDK)/include
APP_C_FLAGS 		:= $(APP_INCLUDE_PATH) -fPIC -Wno-attributes -g -mrdrnd

SSL_CRYPTO_LIB_NAME := :libcrypto.a

APP_LINK_FLAGS := -L$(OPENSSL_ROOT) -l$(SSL_CRYPTO_LIB_NAME) -L$(SGX_LIBRARY_PATH) -l$(SERVICE_LIBRARY_NAME) -ldl -lpthread

######## PCL Tool ###########

PCL_ENCRYPTION_CPP_FILES := encryptip.cpp
PCL_ENCRYPTION_OBJECTS := $(PCL_ENCRYPTION_CPP_FILES:.cpp=.o)
PCL_ENCRYPTION_TOOL := $(PCL_DIR)/bin/x64/sgx_encrypt

.PHONY: all

all: $(PCL_ENCRYPTION_TOOL)

$(PCL_ENCRYPTION_OBJECTS): $(PCL_ENCRYPTION_CPP_FILES)
	$(VERBOSE)$(CXX) -c $^ -o $@ $(APP_C_FLAGS)
	@echo "CXX  =>  $@"

$(PCL_ENCRYPTION_TOOL): $(PCL_ENCRYPTION_OBJECTS)
	$(VERBOSE)$(CXX) $^ -o $@ $(APP_LINK_FLAGS)
	@echo "LINK =>  $@"


.PHONY: clean

clean:
	$(VERBOSE)rm -f $(PCL_ENCRYPTION_TOOL) $(PCL_ENCRYPTION_OBJECTS)

