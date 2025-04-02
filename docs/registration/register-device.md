# CSMP Agent Build and Run Guide

This guide describes the steps to build and run the CSMP Agent sample from the [CiscoDevNet/csmp-agent-lib](https://github.com/CiscoDevNet/csmp-agent-lib) repository. Follow the instructions below to set up your environment, compile the sample, and run the agent.

## 1. Clone the Repository

Clone the CSMP Agent repository from GitHub:

```bash
git clone https://github.com/CiscoDevNet/csmp-agent-lib.git
```

Change your default directory to the root of the CSMP Agent directory structure:

```bash
cd csmp-agent-lib
```

## 2. Install Build Tools

Ensure that you have the required build tools installed on your system. On Ubuntu, install the build-essential package (which includes the gcc compiler, make, etc.) by running:

```bash
sudo apt-get update
sudo apt-get install build-essential
```

For more detailed instructions, refer to the [Installing Compilers on Ubuntu](https://help.ubuntu.com/community/InstallingCompilers) guide.

## 3. Confirm Build Target Platform

If you are building for a different target platform, modify the `Makefile` accordingly. Locate the following line in the `Makefile`:

```make
CC=gcc
```

Change it to use the appropriate compiler for your target platform if necessary.

## 4. Build the CSMP Agent

Before building, ensure that the build script has executable permissions:

```bash
chmod 777 build.sh
```

Then, run the build script:

```bash
./build.sh
```

If the build is successful, you will find the `CsmpAgentLib_sample` executable in the `sample` directory.

## 5. Clean Build Files (Optional)

To clean the build files before a subsequent build, run:

```bash
./build.sh clean
```

## 6. Enable Debug Output (Optional)

To enable additional debug output, open the `Makefile` and add the following line:

```make
CFLAGS += -DPRINTDEBUG
```

This will include debug output during the build.

## 7. Running the CSMP Agent Sample

Once the build is complete, you can run the CSMP Agent sample. There are two ways to run the sample:

### 7.1. Basic Run

If your environment is properly set up, you can start the agent in debug mode by running:

```bash
./CsmpAgentLib_sample -d
```

### 7.2. Full Command-Line Parameters

To configure the FND server’s IPv6 address, agent registration intervals, and the EUI of the agent, use the full command-line parameter set. For example:

```bash
./CsmpAgentLib_sample -d 2020::2020 -min 10 -max 100 -eid <EID_FROM_THE_DEVICES_ON_THE_GUI>
```

**NOTE:**  
- Replace `2020::2020` with a valid FND IPv6 address.
- Replace `<EID_FROM_THE_DEVICES_ON_THE_GUI>` with the appropriate 16-character hexadecimal EID.
- The `-min` and `-max` options set the minimum and maximum registration intervals (in seconds).

Once the CSMP Agent sample is started, it will begin registration attempts with the FND server.

## Summary

This guide covers:
- Cloning the repository
- Installing the necessary build tools
- Confirming the build target platform
- Building and cleaning the build
- Enabling debug output (if needed)
- Running the CSMP Agent sample with proper parameters

For more information or troubleshooting tips, please refer to the repository documentation or contact support.