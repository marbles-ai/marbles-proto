Installing Dependencies
=======================
 
## Overview

Proto files depend on the version of the proto compiler. This must be built first by running:
```
make
make install
```

After which all other dependences can be built by running:
```
make build
make install-deps
```
If you intend to modify dependencies please ensure no files owned by root end up
in the build tree.

## Adding New Dependences Built from Source

For dependencies that need to be built locally do the following.

1. Add a subdirectory for the local build and add it to the `PROJECT_DEPS` 
   macro.  
2. Add scripts to subdirectory. At a minimum you should create:
    - `build.sh`: This is run under your credentials so no sudo please.
      This script should checkout code and build locally. Exit 1 on failure.
    - `install.sh`: This is run as root. Please do not create files in the
      build tree with this script. Exit 1 on failure.
    - `check.sh`: This is run under your credentials. It should exit 0 if 
      the dependency is installed or exit 1 if not installed.
    - `clean.sh`: Clean the build.
3. Optionally add `common.sh` script for common environment settings across build,
   install, clean, and check.

The scripts (`build.sh`,`check.sh`,`common.sh`,`clean.sh`,`install.sh`) are included into the local
dependency handler `build-tool.sh`. These scripts should not be executable or have
a shabang at the file header. The following environment
variables are defined in all scripts:

1. `SCRIPTPATH`: absolute path to directory containing scripts.
2. `ROOTPATH`: absolute path to this directory. 


