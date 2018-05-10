Intel(R) Software Guard Extensions (SGX) Protected Code Loader (PCL) for Linux\* OS
================================================

# linux-sgx-pcl

**Notice:** Starting Intel(R) SGX PSW and SDK branch 2.1.3 the Intel(R) SGX PCL is integrated into the SDK. Please refer to README.md file at  [SampleEnclavePCL](https://github.com/intel/linux-sgx/tree/master/SampleCode/SampleEnclavePCL/)

Introduction
------------
The Intel(R) Software Guard Extensions (Intel(R) SGX) Protected Code Loader (PCL) is intended to protect Intellectual Property (IP) within the code for Intel(R) SGX enclave applications running on the Linux* OS.

**Problem:** Intel(R) SGX provides integrity of code and confidentiality and integrity of data at run-time. However, it does NOT provide confidentiality of code offline as a binary file on disk. Adversaries can reverse engineer the binary enclave shared object.

**Solution:** The enclave shared object (.so) is encrypted at build time. It is decrypted at enclave load time. 

Intel(R) Software Guard Extensions Protected Code Loader for Linux\* OS (Intel(R) SGX PCL) provides: 
1. **sgx_encrypt:** A tool to encrypt the shared object at build time
2. **libsgx_pcl.a:** A library that is statically linked to the enclave and enables the decryption of the enclave at load time
3. Sample code which demonstrates how the tool and lib need to be integrated into an existing enclave project. 

---------

**Relation to Intel(R) SGX SDK, Intel(R) SGX PSW and Intel(R) SGX Driver:**

The Intel(R) SGX PCL is integrated into the [linux-sgx-sdk](https://github.com/01org/linux-sgx) project starting branch 2.1.3. 
For older branches of Intel(R) SGX PSW and SDK the project is an add-on to the Intel(R) SGX SDK and Intel(R) SGX PSW. 

The [linux-sgx-driver](https://github.com/01org/linux-sgx-driver) project hosts the out-of-tree driver for the Linux\* Intel(R) SGX software stack, which will be used until the driver upstreaming process is complete. 

---------

License
-------
See [License.txt](License.txt) for details.

Contributing
-------
See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

Documentation
-------------

See more elaborate documentation at [Intel(R) SGX Protected Code Loader for Linux User Guide](https://github.com/intel/linux-sgx-pcl/blob/master/Intel(R)%20SGX%20Protected%20Code%20Loader%20for%20Linux%20User%20Guide.pdf).

Prerequisites 
-------------

**Notice:** Starting Intel(R) SGX PSW and SDK branch 2.1.3 the Intel(R) SGX PCL is integrated into the SDK. These prerequesites are relevant when Intel(R) SGX PCL is used as a add-on to older branches of the Intel(R) SGX PSW and SDK.  

Build the Intel(R) SGX SDK and Intel(R) SGX PSW Package
-------------------------------------------------------
Follow the instructions in the [linux-sgx-sdk](https://github.com/01org/linux-sgx) project to build and install the Intel(R) SGX PSW and Intel(R) SGX SDK, branches sgx_2.0, sgx_2.1 or sgx_2.1.1.

**Note:** Current Intel(R) SGX PCL supports branches sgx_2.0, sgx_2.1 or sgx_2.1.1 of the Intel(R) SGX PSW and Intel(R) SGX SDK.  

**Note:** Non simulation build flavors require the platform to be Intel(R) SGX enabled (CPU and BIOS)

Build and Install the Intel(R) SGX Driver
-----------------------------------------
Follow the instructions in the [linux-sgx-driver](https://github.com/01org/linux-sgx-driver) project to build and install the Intel(R) SGX driver.

**Note:** Installing the driver require the platform to be Intel(R) SGX enabled (CPU and BIOS)

**Note:** Enclave writer must verify that the Intel(R) SGX SDK SampleCode/SampleEnclave successfully builds and runs on the enclave writer's platform in both simulation mode and HW mode (if HW supports Intel(R) SGX) before the modifications required for Intel(R) SGX PCL are applied. This will decrease the number of failures wrongly associated with Intel(R) SGX PCL.

OpenSSL1.1.0g
-------------

**Notice:** Starting Intel(R) SGX PSW and SDK branch 2.1.3 the Intel(R) SGX PCL is integrated into the SDK. This prerequesite is relevant when Intel(R) SGX PCL is used as a add-on to older branches of the Intel(R) SGX PSW and SDK. 

The build time encryption tool does not use the default OpenSSL version. It uses a newer version (1.1.0g), which must be downloaded and built.

Download OpenSSL1.1.0g from: https://www.openssl.org/source/

Build instructions: (https://wiki.openssl.org/index.php/Compilation_and_Installation)
  ```
    $ ./config
    $ make
  ```

**Note:** Intel® SGX PCL does not require installing OpenSSL 1.1.0g (which could possibly result in overriding the distro’s default). Intel® SGX PCL only uses the headers and generated shared objects.

git
---

**Notice:** Starting Intel(R) SGX PSW and SDK branch 2.1.3 the Intel(R) SGX PCL is integrated into the SDK. This prerequesite is relevant when Intel(R) SGX PCL is used as a add-on to older branches of the Intel(R) SGX PSW and SDK. 

Install git. git is used to apply a patch file to the Intel(R) SGX PSW and Intel(R) SGX SDK. 

Apply modifications to Intel(R) SGX PSW and Intel(R) SGX SDK
------------------------------------------------------------

**Notice:** Starting Intel(R) SGX PSW and SDK branch 2.1.3 the Intel(R) SGX PCL is integrated into the SDK. This step is relevant when Intel(R) SGX PCL is used as a add-on to older branches of the Intel(R) SGX PSW and SDK. 

Apply the required modifications to Intel(R) SGX PSW and Intel(R) SGX SDK source files using the supplied git patch. 
  ```
    $ cd <linux-sgx>
  ```
  where \<linux-sgx\> is the home directory of Intel(R) SGX PSW and Intel(R) SGX SDK.
  ```
    $ git apply <path_to_pcl_dir>/Tools/sgx.psw.sdk.2.1.git.diff
  ```
  where <path_to_pcl_dir> is path to Intel(R) SGX PCL base directory (either full or relative).


**Note:** A git patch file can only be applied on a specific branch. Enclave writer must verify the patch is applied on the correct branch. When using Intel(R) SGX PSW and Intel(R) SGX SDK branch sgx_2.0 use sgx.psw.sdk.2.0.git.diff. When using branch sgx_2.1 or sgx_2.1.1 use sgx.psw.sdk.2.1.git.diff. 

Rebuild and Reinstall Intel(R) SGX PSW and Intel(R) SGX SDK
-----------------------------------------------------------

**Notice:** Starting Intel(R) SGX PSW and SDK branch 2.1.3 the Intel(R) SGX PCL is integrated into the SDK. This step is relevant when Intel(R) SGX PCL is used as a add-on to older branches of the Intel(R) SGX PSW and SDK. 

Follow instructions at [linux-sgx](https://github.com/01org/linux-sgx) to uninstall and clean, then build and install the Intel(R) SGX PSW and Intel(R) SGX SDK.

Set environment variables
-------------------------

**Notice:** Starting Intel(R) SGX PSW and SDK branch 2.1.3 the Intel(R) SGX PCL is integrated into the SDK. Steps 1-3 below are relevant when Intel(R) SGX PCL is used as a add-on to older branches of the Intel(R) SGX PSW and SDK. 

1.	Set the Linux Intel(R) SGX PSW and Intel(R) SGX SDK home directory:
  ```
    $ export SGX_SDK_SRCS=< sgx_psw_sdk_sources_home_dir >
  ```
  where < sgx_psw_sdk_sources_home_dir > is the base directory of the Intel(R) SGX PSW and Intel(R) SGX SDK sources (that is, where the source files for Intel(R) SGX PSW and Intel(R) SGX SDK are located)

2.	Set the OpenSSL 1.1.0g shared object directory:
  ```
    $ export OPENSSL_ROOT=< openssl_crypto_libraries_dir >
  ```
  where < openssl_crypto_libraries_dir > is full path to the directory where OpenSSL 1.1.0g libcrypto.so (or libcrypto.so.1.1 etc.) is located. 

3.	When separately building the encryption tool, Intel(R) SGX PCL trusted runtime library or sample enclave, set the Intel(R) SGX PCL root folder. 
  ```
    $ export PCL_DIR=< path_to_pcl_dir >
  ```
  where < path_to_pcl_dir > is the full path to the main directory of Intel(R) SGX PCL (folder which includes the subfolder Include, Common, Tools etc.). 
 
Build the Intel(R) SGX PCL tool and static library 
--------------------------------------------------

**Notice:** Starting Intel(R) SGX PSW and SDK branch 2.1.3 the Intel(R) SGX PCL is integrated into the SDK. This step is relevant when Intel(R) SGX PCL is used as a add-on to older branches of the Intel(R) SGX PSW and SDK. 

The following steps describe how to build the Intel(R) SGX PCL build time encryption tool and static library. Enclave writer can build the project according to the enclave writer's requirements.  
- To build both Intel(R) SGX PCL encryption tool (sgx_encrypt) and Intel(R) SGX PCL statically linked library with default configuration, enter the following command:  
```
  $ make  
```  
  The tool is generated at `bin/x64` directory.  
  The static library is generated at `lib64` directory.
  
- To build Intel(R) SGX PCL with debug information, enter the following command:  
```
  $ make DEBUG=1
```
- To clean the files generated by previous `make` command, enter the following command:  
```
  $ make clean
```
**Note**: It is also possible to enter either the `Sources` or `Tools\Encryptip` folders and use the `make` command to separately build the Intel(R) SGX PCL static library or build time encryption tool, respectively.

Build and test the Intel(R) SGX PCL with the sample code
--------------------------------------------------------

- To compile and run the sample
```
  $ cd SampleCode/SampleEnclavePCL
  $ make
  $ ./app
```

- To compile and run the sample when using the PCL add on to older branches of the Intel(R) SGX PSW and SDK:
```
  $ cd SampleCode/SampleEnclave
  $ make
  $ ./app
```


**Note:** See [linux-sgx-sdk](https://github.com/01org/linux-sgx) for instructions on building with debug information and / or building in simulation mode. 

**Note:** Enclave writers are encoureged to compare SampleEnclave to SampleEnclavePCL as a demonstration of how the Intel(R) SGX PCL should be integrated into the enclave writer's project.  
