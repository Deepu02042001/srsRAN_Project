# Uncovering Security Weaknesses in srsRAN with CodeQL: A Static Analysis Approach for Next-Gen RAN Systems
==============

[![Build Status](https://github.com/srsran/srsRAN_Project/actions/workflows/ccpp.yml/badge.svg?branch=main)](https://github.com/srsran/srsRAN_Project/actions/workflows/ccpp.yml)
[![OpenSSF Best Practices](https://www.bestpractices.dev/projects/7868/badge)](https://www.bestpractices.dev/projects/7868)

# Project Description

Uncovering Security Weaknesses in srsRAN with CodeQL is a static analysis project that examines security vulnerabilities in the srsRAN open-source 5G RAN using CodeQL. The project analyzes the C/C++ codebase to identify issues such as memory safety bugs, unsafe API usage, cryptographic weaknesses, hardcoded secrets, and improper input validation, highlighting potential risks like denial-of-service, privilege escalation, and data leakage.


The srsRAN Project is a complete 5G RAN solution, featuring an ORAN-native CU/DU developed by [SRS](http://www.srs.io).

See the [srsRAN Project](https://www.srsran.com/) for information, guides and project news.

Build instructions and user guides - [srsRAN Project documentation](https://docs.srsran.com/projects/project).

Community announcements and support - [Discussion board](https://www.github.com/srsran/srsran_project/discussions).

Features and roadmap - [Features](https://docs.srsran.com/projects/project/en/latest/general/source/2_features_and_roadmap.html).


## Project Overview

Modern 5G Radio Access Networks increasingly rely on open-source, software-defined RAN implementations such as **srsRAN**. Due to the size, complexity, and performance-critical nature of RAN software written in **C/C++**, latent security vulnerabilities can remain undetected and pose serious risks to **network stability**, **availability**, and **user privacy**.

This project performs a **read-only static security analysis** of the **srsRAN codebase** using **CodeQL**, focusing on systematically uncovering source-level security weaknesses **without changing runtime behavior**.

This analysis framework is capable of:

- ✅ **Detecting memory safety vulnerabilities** that may cause crashes or denial-of-service  
- ✅ **Identifying unsafe and insecure API usage** in system-level code  
- ✅ **Flagging hardcoded credentials/secrets** and **weak randomness**  
- ✅ **Discovering cryptographic misuse** and **timing-attack risks**  
- ✅ **Highlighting improper input validation** and **insecure network communication**  
- ✅ **Exposing over-privileged access patterns** and **missing authorization checks**  

The system uses:

- **Custom CodeQL queries** written for **C/C++** RAN software  
- **Semantic data-flow and control-flow analysis** provided by CodeQL  
- **Query-driven detection** of security anti-patterns and **CWE classes**  
- **Reproducible execution pipeline** with **SARIF** result outputs  

---

## Where the CodeQL queries are

📁 **Queries Folder**
- `codeql-custom-queries-cpp/`

🔗 **Direct Link**
- https://github.com/Deepu02042001/srsRAN_Project/tree/main/codeql-custom-queries-cpp

---


Build Preparation
-----------------

### Dependencies

* Build tools:
  * cmake:               <https://cmake.org/>
  
* Mandatory requirements:
  * libfftw:             <https://www.fftw.org/>
  * libsctp:             <https://github.com/sctp/lksctp-tools>
  * yaml-cpp:            <https://github.com/jbeder/yaml-cpp>
  * mbedTLS:             <https://www.trustedfirmware.org/projects/mbed-tls/>
  * googletest:          <https://github.com/google/googletest/>

You can install the build tools and mandatory requirements for some example distributions with the commands below:

<details open>
<summary><strong>Ubuntu 22.04</strong></summary>

```bash
sudo apt-get install cmake make gcc g++ pkg-config libfftw3-dev libmbedtls-dev libsctp-dev libyaml-cpp-dev libgtest-dev
```

</details>
<details>
<summary><strong>Fedora</strong></summary>

```bash
sudo yum install cmake make gcc gcc-c++ fftw-devel lksctp-tools-devel yaml-cpp-devel mbedtls-devel gtest-devel
```

</details>
<details>
<summary><strong>Arch Linux</strong></summary>

```bash
sudo pacman -S cmake make base-devel fftw mbedtls yaml-cpp lksctp-tools gtest
```

</details>

#### Split-8

For Split-8 configurations, either UHD or ZMQ is required for the fronthaul interface. Both drivers are linked below, please see their respective documentation for installation instructions.

* UHD:                 <https://github.com/EttusResearch/uhd>
* ZMQ:                 <https://zeromq.org/>

#### Split-7.2

For Split-7.2 configurations no extra 3rd-party dependencies are required, only those listed above.

Optionally, DPDK can be installed for high-bandwidth low-latency scenarios. For more information on this, please see [this tutorial](https://docs.srsran.com/projects/project/en/latest/tutorials/source/dpdk/source/index.html#).

Build Instructions
------------------

Download and build srsRAN:

<details open>
<summary><strong>Vanilla Installation</strong></summary>

First, clone the srsRAN Project repository:

```bash
    git clone https://github.com/srsRAN/srsRAN_Project.git
```

Then build the code-base:

```bash

    cd srsRAN_Project
    mkdir build
    cd build
    cmake ../ 
    make -j $(nproc)
    make test -j $(nproc)
```

You can now run the gNB from ``srsRAN_Project/build/apps/gnb/``. If you wish to install the srsRAN Project gNB, you can use the following command:

```bash
    sudo make install
```

</details>

<details>
<summary><strong>ZMQ Enabled Installation</strong></summary>

Once ZMQ has been installed you will need build of srsRAN Project with the correct flags to enable the use of ZMQ.

The following commands can be used to clone and build srsRAN Project from source. The relevant flags are added to the ``cmake`` command to enable the use of ZMQ:

```bash
git clone https://github.com/srsran/srsRAN_Project.git
cd srsRAN_Project
mkdir build
cd build
cmake ../ -DENABLE_EXPORT=ON -DENABLE_ZEROMQ=ON
make -j $(nproc)
make test -j $(nproc)
```

Pay extra attention to the cmake console output. Make sure you read the following line to ensure ZMQ has been correctly detected by srsRAN:

```bash
...
-- FINDING ZEROMQ.
-- Checking for module 'ZeroMQ'
--   No package 'ZeroMQ' found
-- Found libZEROMQ: /usr/local/include, /usr/local/lib/libzmq.so
...
```

</details>

<details>
<summary><strong>DPDK Enabled Installation</strong></summary>

Once DPDK has been installed and configured you will need to create a clean build of srsRAN Project to enable the use of DPDK.

If you have not done so already, download the code-base with the following command:

```bash
git clone https://github.com/srsRAN/srsRAN_Project.git
```

Then build the code-base, making sure to include the correct flags when running cmake:

```bash
cd srsRAN_Project
mkdir build
cd build
cmake ../ -DENABLE_DPDK=True -DASSERT_LEVEL=MINIMAL
make -j $(nproc)
make test -j $(nproc)
```

</details>

### PHY Tests

PHY layer tests use binary test vectors and are not built by default. To enable, see the [docs](https://docs.srsran.com/projects/project/en/latest/user_manuals/source/installation.html).

Deploying srsRAN Project
------------------------

srsRAN Project can be run in two ways:

* As a monolithic gNB (combined CU & DU)
* With a split CU and DU

For exact details on running srsRAN Project in any configuration, see [the documentation](https://docs.srsran.com/projects/project/en/latest/user_manuals/source/running.html).

For information on configuring and running srsRAN for various different use cases,  check our [tutorials](https://docs.srsran.com/projects/project/en/latest/tutorials/source/index.html).
