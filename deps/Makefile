# Makefile for dependencies.
#
# For Linux OS distribution packages add dependencies to linux_pkg_deps.conf.
# See that file for details on how to use.
#
# For dependencies that need to be built locally do the following.
# (1) Add a subdirectory for the local build and add it to PROJECT_DEPS.
# (2) Add scripts to subdirectory. At a minimum you should create:
#     - build.sh: This is run under your credentials so no sudo please.
#       This script should sheckout code and build locally. Exit 1 on failure.
#     - install.sh: This is run as root. Please do not create files in the
#       build tree with this script. Exit 1 on failure.
#     - check.sh: This is run under your credentials. It should exit 0 is 
#       the dependency is installed or exit 1 if not installed.
# (3) Optionally add common.sh script for common environment settings across
#     build, install, and check.
#
# The scripts (build.sh,check.sh,common,sh,install.sh) are included into
# the local dependency handler build-tool.sh. These scripts should not be 
# executable or have a shabang at the file header. The following environment
# variables are defined in all scripts:
# (1) SCRIPTPATH: absolute path to directory containing scripts.
# (2) ROOTPATH: absolute path to this directory. 
#

ifeq "$(findstring Darwin,$(shell python -mplatform))" "Darwin"
LINUX=0
ifeq "$(findstring brew,$(shell which brew))" "brew"
OSXBREW=1
OSXPORTS=0
else
OSXBREW=0
ifeq "$(findstring port,$(shell which port))" "port"
OSXPORTS=1
else
OSXPORTS=0
$(error Cannot find OSX package manager brew|ports)
endif
endif
else
$(info Linux install for all users)
LINUX=1
OSXPORTS=0
OSXBREW=0
endif

# Any dependencies that need to be built locally should be added to this list.
ifeq ($(LINUX),1)
PROJECT_DEPS := java protocbuf_py protocbuf_java grpc
else
PROJECT_DEPS := protocbuf_py protocbuf_java grpc
endif

INSTALL_TEMPLATE = @if ./build-tool.sh $(1) check; then \
		echo "$(1) Already installed"; else \
		./build-tool.sh $(1) install; \
		ldconfig; \
		if ./build-tool.sh $(1) check; then \
			echo "$(1) install successful"; else \
			echo "$(1) install failed"; fi; \
		fi

BUILD_TEMPLATE = @if ./build-tool.sh $(1) check; then \
		echo "$(1) Already installed"; else \
		./build-tool.sh $(1) build; fi

CLEAN_TEMPLATE = ./build-tool.sh $(1) clean

FORCE_BUILD_TEMPLATE = ./build-tool.sh $(1) build

.PHONY: all install-deps install build install force-build clean

CLEAN_TARGETS=$(addprefix clean-, $(PROJECT_DEPS))
BUILD_TARGETS=$(addprefix build-, $(PROJECT_DEPS))
FORCE_BUILD_TARGETS=$(addprefix force-build-, $(PROJECT_DEPS))
INSTALL_TARGETS=$(addprefix install-, $(PROJECT_DEPS))

all:
	$(call BUILD_TEMPLATE, protoc)

$(INSTALL_TARGETS):
	@echo "--$@-begin--"
	$(call INSTALL_TEMPLATE, $(patsubst install-%,%,$@))
	@echo "--$@-end--"
	@echo

$(CLEAN_TARGETS):
	@echo "--$@-begin--"
	$(call CLEAN_TEMPLATE, $(patsubst clean-%,%,$@))
	@echo "--$@-end--"
	@echo

$(BUILD_TARGETS):
	@echo "--$@-begin--"
	$(call BUILD_TEMPLATE, $(patsubst build-%,%,$@))
	@echo "--$@-end--"
	@echo

$(FORCE_BUILD_TARGETS):
	@echo "--$@-begin--"
	$(call FORCE_BUILD_TEMPLATE, $(patsubst build-%,%,$@))
	@echo "--$@-end--"
	@echo

install:
	$(call INSTALL_TEMPLATE, protoc)

install-deps: $(INSTALL_TARGETS)

clean: $(CLEAN_TARGETS)

build: $(BUILD_TARGETS)

force-build: $(FORCE_BUILD_TARGETS)


