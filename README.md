# DeepSpeed Windows Patcher

A graphical tool to simplify building and installing DeepSpeed on Windows systems. This tool automates the build process, manages dependencies, and provides clear guidance for CUDA setup.

# Table of Contents
- [Important Version Compatibility Information](#important-version-compatibility-information)
  - [Version Compatibility Example](#version-compatibility-example)
  - [Common Compatibility Issues](#common-compatibility-issues)
- [Purpose](#purpose)
- [System Requirements](#system-requirements)
  - [Mandatory Prerequisites](#mandatory-prerequisites)
  - [Python Package Dependencies](#python-package-dependencies)
- [Download and Usage](#download-and-usage)
  - [Getting the Tool](#getting-the-tool)
  - [Before Running the Tool](#before-running-the-tool)
  - [Running the Tool](#running-the-tool)
  - [Important Notes](#important-notes)
  - [Verification After Building](#verification-after-building)
  - [Common Setup Mistakes to Avoid](#common-setup-mistakes-to-avoid)
  - [Quick Environment Check](#quick-environment-check)
- [Startup Checks](#startup-checks)
- [Usage Guide](#usage-guide)
  - [Building DeepSpeed](#building-deepspeed)
  - [Build Management](#build-management)
- [Configuration File](#configuration-file-deepspeed_configjson)
  - [Adding New Versions](#adding-new-versions)
- [Additional Notes](#additional-notes)
- [Troubleshooting](#troubleshooting)
- [Notes for Developers](#notes-for-developers)
- [Support](#support)

---

## Important Version Compatibility Information

DeepSpeed wheels are environment-specific and must be built for your exact configuration. A built wheel is tied to:

1. **Python Major Version**
   - Must match exactly (e.g., 3.10.x, 3.11.x)
   - A wheel built for Python 3.10 won't work on Python 3.11 or 3.9

2. **CUDA Versions** (There are TWO different CUDA versions to consider):
   
   a) **PyTorch CUDA Version**
   - This is the CUDA version that PyTorch was built with
   - Can be checked with `torch.version.cuda`
   - This is determined when you install PyTorch
   - Example: PyTorch 2.1.0+cu121 uses CUDA 12.1

   b) **NVIDIA CUDA Toolkit Version**
   - This is the full CUDA development toolkit installed on your system
   - Used for compiling DeepSpeed's CUDA extensions
   - Must be installed separately from PyTorch
   - Should be compatible with (same or newer than) your PyTorch CUDA version

   For example:
   - If PyTorch uses CUDA 11.8 → NVIDIA CUDA Toolkit should be 11.8 or higher
   - If PyTorch uses CUDA 12.1 → NVIDIA CUDA Toolkit should be 12.1 or higher

### Version Compatibility Example
```
Environment A (where wheel was built):
- Python 3.11.5
- PyTorch 2.1.0+cu121 (CUDA 12.1)
- NVIDIA CUDA Toolkit 12.1

This wheel will ONLY work in environments with:
- Python 3.11.x (any minor version of 3.11)
- PyTorch built with CUDA 12.1
- NVIDIA CUDA Toolkit 12.1 or higher
```

### Common Compatibility Issues
- Installing a wheel built with Python 3.10 in a Python 3.11 environment
- Using a wheel built against CUDA 11.8 with PyTorch built for CUDA 12.1
- Having mismatched PyTorch CUDA and NVIDIA CUDA Toolkit versions

## Purpose

Building DeepSpeed on Windows can be challenging due to specific requirements and environment setup needs. This tool:
- Automates the build process
- Verifies system prerequisites
- Manages build environments
- Archives built wheel files
- Provides CUDA setup guidance
- Offers both build-only and build-with-install options

## System Requirements

### Mandatory Prerequisites
1. **NVIDIA GPU with CUDA Support**
   - Compatible NVIDIA GPU
   - NVIDIA Display Driver installed

2. **NVIDIA CUDA Toolkit**
   - Version 11.0 or later required
   - Must include:
     - CUDA Development Compiler (nvcc)
     - CUDA Development Libraries (CUBLAS)
     - CUDA Runtime Libraries
   - Download from: [NVIDIA CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive)

3. **Visual Studio with C++ Build Tools**
   - Visual Studio 2019 or 2022 (Community Edition or BuildTools)
   - Must include "Desktop development with C++"
   - Download from: [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/)

4. **Python Environment**
   - Python 3.8 or later
   - PyTorch installed with CUDA support
   - Admin rights for installation

### Python Package Dependencies
- PyTorch (must be installed manually)
- ninja (auto-installed if missing)
- psutil (auto-installed if missing)

## Download and Usage

### Getting the Tool

1. Clone the repository:
`git clone https://github.com/erew123/deepspeedpatcher`

move into the folder
`cd deepspeedpatcher`

### Before Running the Tool

⚠️ **IMPORTANT**: Complete these steps in order:

1. **Install Visual Studio or Visual Studio Build Tools**
   - Install Visual Studio 2019/2022 Community or Build Tools
   - During installation, select "Desktop development with C++"
   - This must be done before proceeding

2. **Install NVIDIA CUDA Toolkit**
   - Install the appropriate version based on your PyTorch's CUDA version
   - Example: For PyTorch with CUDA 12.1, install CUDA Toolkit 12.1
   - Must include development components (nvcc compiler)

3. **Set Up Python Environment**
   - Create and activate your Python environment (venv, conda, etc.)
   - Install PyTorch with the desired CUDA version
   - Verify PyTorch CUDA is working:
     ```python
     python -c "import torch; print(f'PyTorch CUDA available: {torch.cuda.is_available()}, Version: {torch.version.cuda}')"
     ```

### Running the Tool

1. **Activate Your Target Environment**
   ```bash
   # If using venv
   .\venv\Scripts\activate

   # If using conda
   conda activate your_environment_name
   ```

2. **Launch the Tool**
   ```bash
   python builddeepspeed.py
   ```
   - The tool will request administrative privileges
   - It will perform system checks automatically

### Important Notes

- **Environment Matching**: The DeepSpeed wheel will be built for the Python environment that launches the tool
- **Admin Rights**: The tool requires administrative privileges to build DeepSpeed
- **Build Location**: By default, builds occur in a `deepspeed` subdirectory where the tool is run
- **Archived Wheels**: Completed builds are stored in `deepspeed_wheels` with version information

### Verification After Building

After building and installing, verify DeepSpeed:
```bash
python -c "import deepspeed; deepspeed.show_env()"
```

### Common Setup Mistakes to Avoid

1. **Wrong Environment Active**
   - Building in one environment but trying to use in another
   - Not activating the target environment before running the tool

2. **Incorrect Order of Installation**
   - Installing CUDA Toolkit after PyTorch
   - Not having Visual Studio/Build Tools installed first

3. **Version Mismatches**
   - PyTorch CUDA version doesn't match CUDA Toolkit
   - Using wrong Python version for your needs

### Quick Environment Check

Run these commands before starting to verify your setup:
```bash
# Check Python version
python --version

# Check PyTorch and its CUDA version
python -c "import torch; print(f'PyTorch {torch.__version__}, CUDA {torch.version.cuda if torch.cuda.is_available() else "Not Available"}')"

# Check CUDA Toolkit
nvcc --version
```

All these components must be correctly installed and compatible before running the tool.


## Startup Checks

The tool performs several verification steps on launch:

1. **Administrative Rights Check**
   - Verifies admin privileges
   - Offers to restart with elevated privileges if needed

2. **Visual Studio Detection**
   - Checks for VS2019/VS2022 installations
   - Verifies presence of C++ build tools

3. **CUDA Toolkit Verification**
   - Scans for installed CUDA versions
   - Verifies nvcc compiler availability
   - Checks CUBLAS presence

4. **Python Environment Check**
   - Verifies Python version
   - Checks PyTorch installation and CUDA availability
   - Auto-installs missing dependencies (except PyTorch)

## Usage Guide

### Building DeepSpeed

1. **Launch the Application**
   - Run as administrator
   - The tool will perform initial system checks

2. **Configure Build Settings**
   - Select DeepSpeed version
   - Choose CUDA version
   - Set installation directory
   - Configure build options (typically left unchecked for Windows)

3. **Build Options**
   - **Build Only**: Creates wheel file without installation
   - **Install Built Wheel**: Installs previously built wheel
   - **Build and Install**: Performs both operations
   - **CUDA_HOME Setup Guide**: Shows CUDA_HOME configuration instructions

### Build Management

The tool manages builds in an organized way:

1. **Work Directory Structure**
   ```
   root/
   ├── deepspeed/          # Temporary build directory
   └── deepspeed_wheels/   # Archive directory
       └── deepspeed_[version]_cuda[version]_py[version]/
           └── wheelfile.whl
   ```

2. **Cleanup Process**
   - Automatically cleans build directory before each build
   - Preserves built wheels in version-specific archives
   - Maintains separate directories for different configurations

## Configuration File (deepspeed_config.json)

The tool uses a JSON configuration file to manage available versions:

```json
{
    "versions": {
        "0.15.0": {
            "url": "https://github.com/microsoft/DeepSpeed/archive/refs/tags/v0.15.0.zip",
            "cuda_min": "11.0"
        },
        "0.15.1": {
            "url": "https://github.com/microsoft/DeepSpeed/archive/refs/tags/v0.15.1.zip",
            "cuda_min": "11.0"
        }
    }
}
```

### Adding New Versions

To add support for new DeepSpeed versions:

1. Add a new entry to the "versions" object
2. Specify the GitHub release URL
3. Set minimum CUDA version requirement

Note: This tool supports DeepSpeed 0.15.0 and later. Earlier versions may have different build requirements and are not supported.

## Additional Notes

1. **CUDA_HOME Environment**
   - The tool provides guidance for setting CUDA_HOME
   - Different options for system-wide, conda, and venv setups
   - Verification steps included

2. **Build Artifacts**
   - Wheels are archived with version information
   - Each build creates a clean environment
   - Previous builds are preserved in the archive directory

3. **Error Handling**
   - Detailed error messages in the log
   - Suggestions for common issues
   - Build process can be retried if needed

4. **Log File**
   - All operations are logged to 'deepspeed_build.log'
   - Includes timestamps and detailed progress information
   - Useful for troubleshooting

## Troubleshooting

1. **Build Failures**
   - Verify CUDA installation completeness
   - Check Visual Studio installation
   - Ensure PyTorch is installed with CUDA support
   - Review log file for specific errors

2. **CUDA Issues**
   - Confirm CUDA_HOME is set correctly
   - Verify nvcc.exe is accessible
   - Check CUDA version compatibility with PyTorch

3. **Installation Issues**
   - Run as administrator
   - Check Python environment isolation
   - Verify all prerequisites are met

## Notes for Developers

- The tool is designed for Windows 10/11 environments
- Build options are minimized for Windows compatibility
- The configuration file can be extended for future versions
- Error handling prioritizes user feedback

## Support

For DeepSpeed-specific issues, refer to:
- [DeepSpeed GitHub](https://github.com/microsoft/DeepSpeed)
- [DeepSpeed Documentation](https://www.deepspeed.ai/)

For tool-specific issues:
- Check the log file
- Verify system requirements
- Follow the CUDA setup guide
