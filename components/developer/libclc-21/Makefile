#
# This file and its contents are supplied under the terms of the
# Common Development and Distribution License ("CDDL"), version 1.0.
# You may only use this file in accordance with the terms of version
# 1.0 of the CDDL.
#
# A full copy of the text of the CDDL should have accompanied this
# source.  A copy of the CDDL is also available via the Internet at
# http://www.illumos.org/license/CDDL.
#

#
# Copyright 2026 isoriano1968
#

# libclc, LLVM's OpenCL C library, for OpenIndiana's LLVM 21
# (developer/clang-21, /usr/clang/21): only its SPIR-V targets
# spirv-mesa3d- and spirv64-mesa3d-, which Mesa 26's mesa-clc links into
# the GPU-side kernels of crocus and iris. Built with llvm-spirv from
# developer/llvm/spirv-llvm-translator-21 and installed next to that LLVM.

BUILD_BITS=		64
BUILD_STYLE=		cmake
include ../../../make-rules/shared-macros.mk

LLVM_MAJOR=		21
LLVM_PREFIX=		/usr/clang/$(LLVM_MAJOR)

COMPONENT_NAME=		libclc
COMPONENT_VERSION=	21.1.8
COMPONENT_SUMMARY=	OpenCL C library (SPIR-V for Mesa) for LLVM $(LLVM_MAJOR)
COMPONENT_PROJECT_URL=	https://libclc.llvm.org/
COMPONENT_SRC=		libclc-$(COMPONENT_VERSION).src
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.xz
COMPONENT_ARCHIVE_URL=	https://github.com/llvm/llvm-project/releases/download/llvmorg-$(COMPONENT_VERSION)/$(COMPONENT_ARCHIVE)
COMPONENT_ARCHIVE_HASH=	sha256:6c2677362a53531c35edf482bdc9171ea0471ca0a1e9138ac9b5a1782925616f
COMPONENT_FMRI=		developer/llvm/libclc-$(LLVM_MAJOR)
COMPONENT_CLASSIFICATION= Development/Other Languages
COMPONENT_LICENSE=	Apache-2.0 WITH LLVM-exception, NCSA OR MIT (legacy)
COMPONENT_LICENSE_FILE=	LICENSE.TXT

include $(WS_MAKE_RULES)/common.mk

CMAKE_OPTIONS += -DCMAKE_INSTALL_PREFIX=$(LLVM_PREFIX)
CMAKE_OPTIONS += -DCMAKE_BUILD_TYPE=Release
CMAKE_OPTIONS += -DLLVM_CMAKE_DIR=$(LLVM_PREFIX)/lib/cmake/llvm
CMAKE_OPTIONS += -DLLVM_SPIRV=$(LLVM_PREFIX)/bin/llvm-spirv
CMAKE_OPTIONS += "-DLIBCLC_TARGETS_TO_BUILD=spirv-mesa3d-;spirv64-mesa3d-"

# Build dependencies
REQUIRED_PACKAGES += developer/clang-$(LLVM_MAJOR)
REQUIRED_PACKAGES += developer/llvm/spirv-llvm-translator-$(LLVM_MAJOR)
# Auto-generated dependencies
