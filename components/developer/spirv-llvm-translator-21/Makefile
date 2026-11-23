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

# Khronos' SPIR-V <-> LLVM IR translator (libLLVMSPIRVLib) for OpenIndiana's
# LLVM 21 (developer/clang-21, /usr/clang/21).  Mesa 26 needs it for
# mesa-clc, with which crocus (and iris) build their GPU-side kernels.
# Installed next to the LLVM it is built for, like that LLVM itself.
#
# The translator pins an exact SPIRV-Headers commit (spirv-headers-tag.conf)
# and would fetch it with git during the build; it is the second pristine
# archive here instead (headers only, build time only), so the build needs
# no network and OpenIndiana's x11/library/spirv-headers stays as it is.

BUILD_BITS=		64
BUILD_STYLE=		cmake
include ../../../make-rules/shared-macros.mk

LLVM_MAJOR=		21
LLVM_PREFIX=		/usr/clang/$(LLVM_MAJOR)

COMPONENT_NAME=		spirv-llvm-translator
COMPONENT_VERSION=	21.1.13
COMPONENT_SUMMARY=	SPIR-V to LLVM IR translator library for LLVM $(LLVM_MAJOR)
COMPONENT_PROJECT_URL=	https://github.com/KhronosGroup/SPIRV-LLVM-Translator
COMPONENT_SRC=		SPIRV-LLVM-Translator-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_URL=	$(COMPONENT_PROJECT_URL)/archive/refs/tags/v$(COMPONENT_VERSION).tar.gz
COMPONENT_ARCHIVE_HASH=	sha256:f80d32d266df54eeb75e41b87ed4ea4bfdbe8f0eaea510de1d7cc3f088ebbd9d

# the SPIRV-Headers commit of spirv-headers-tag.conf in 21.1.13
SPIRV_HEADERS_COMMIT=	cb42dec3830d3ac67fa449ecdc0c0f73d5e74498
COMPONENT_SRC_1=	SPIRV-Headers-$(SPIRV_HEADERS_COMMIT)
COMPONENT_ARCHIVE_1=	$(COMPONENT_SRC_1).tar.gz
COMPONENT_ARCHIVE_URL_1= https://github.com/KhronosGroup/SPIRV-Headers/archive/$(SPIRV_HEADERS_COMMIT).tar.gz
COMPONENT_ARCHIVE_HASH_1= sha256:117010e525fe36b1ce006e0e58a6e0a3b415aabc9179d912b92737f41f3f994f

COMPONENT_FMRI=		developer/llvm/spirv-llvm-translator-$(LLVM_MAJOR)
COMPONENT_CLASSIFICATION= Development/Other Languages
COMPONENT_LICENSE=	NCSA
COMPONENT_LICENSE_FILE=	LICENSE.TXT

include $(WS_MAKE_RULES)/common.mk

CMAKE_OPTIONS += -DCMAKE_INSTALL_PREFIX=$(LLVM_PREFIX)
CMAKE_OPTIONS += -DCMAKE_INSTALL_LIBDIR=lib
CMAKE_OPTIONS += -DCMAKE_BUILD_TYPE=Release
CMAKE_OPTIONS += -DBUILD_SHARED_LIBS=ON
CMAKE_OPTIONS += -DLLVM_DIR=$(LLVM_PREFIX)/lib/cmake/llvm
CMAKE_OPTIONS += -DLLVM_EXTERNAL_SPIRV_HEADERS_SOURCE_DIR=$(COMPONENT_DIR)/$(COMPONENT_SRC_1)
CMAKE_OPTIONS += -DLLVM_SPIRV_INCLUDE_TESTS=OFF
CMAKE_OPTIONS += -DCMAKE_INSTALL_RPATH=$(LLVM_PREFIX)/lib

# Build dependencies
REQUIRED_PACKAGES += developer/clang-$(LLVM_MAJOR)

# Auto-generated dependencies
REQUIRED_PACKAGES += $(CLANG_RUNTIME_PKG)
REQUIRED_PACKAGES += $(GCC_RUNTIME_PKG)
REQUIRED_PACKAGES += $(GXX_RUNTIME_PKG)
REQUIRED_PACKAGES += system/library
