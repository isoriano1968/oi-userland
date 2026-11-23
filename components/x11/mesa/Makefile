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
# Copyright 2015 Alexander Pyhalov
# Copyright 2015 Ken Mays
#

BUILD_STYLE = meson
ifeq ($(shell uname -p),sparc)
# OpenIndiana SPARC: gcc 13 is its newest.
GCC_VERSION = 13
endif
# Mesa 26 needs Python >= 3.10 (mako, PyYAML, packaging) for its generators.
PYTHON_VERSION = 3.14
include ../../../make-rules/shared-macros.mk

COMPONENT_NAME= mesa
COMPONENT_VERSION= 26.2.7
COMPONENT_REVISION= 5
COMPONENT_SUMMARY= The Mesa 3-D Graphics Library
COMPONENT_SRC= mesa-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE= mesa-$(COMPONENT_VERSION).tar.xz
COMPONENT_ARCHIVE_HASH= sha256:04294f75d22e9f61cd1e050206bc48b3d39366e788f1803a3368e3d5a872ce18
COMPONENT_ARCHIVE_URL= https://archive.mesa3d.org/$(COMPONENT_ARCHIVE)
COMPONENT_PROJECT_URL = http://www.mesa3d.org/

TEST_TARGET= $(NO_TESTS)
include $(WS_MAKE_RULES)/common.mk

PATCH_LEVEL= 0

ARCHLIBSUBDIR.64= /$(MACH64)
ARCHLIBSUBDIR= $(ARCHLIBSUBDIR.$(BITS))

SERVERMOD_SUBDIR.64= /$(MACH64)
SERVERMOD_SUBDIR= $(SERVERMOD_SUBDIR.$(BITS))
X11_SERVERMODS_DIR= /usr/lib/xorg/modules
X11_SERVERLIBS_DIR= /usr/lib/xorg

# Identify source_file files in hard links for manifest generator.
#PKG_HARDLINKS +=	usr/lib/xorg/modules/dri/radeon_dri.so

CONFIGURE_OPTIONS += --libdir='lib/mesa/$(MACH64)'


# Mesa 26: i965 is gone (crocus drives Haswell and older Intel), osmesa,
# xvmc and xa were removed upstream.  radeonsi (GCN: Tahiti in the Mac Pro
# 2013 up to Navi) needs LLVM with the AMDGPU target: OpenIndiana's LLVM 21.
# crocus (Haswell and older Intel) and iris (Broadwell and later: Kaby Lake
# in the MacBook Pro 2017) build their GPU-side kernels with mesa-clc, which
# needs LLVM 21's SPIRV-LLVM-Translator and libclc (developer/llvm/*-21, in
# /usr/clang/21); libclc is linked in, so the drivers do not need it at run
# time.
#
# SPARC (Sun workstations): r300 (XVR-300, RV370) and r600 (XVR-?) on the
# radeon KMS driver of illumos-drm, softpipe; no LLVM there, so no llvmpipe,
# radeonsi or mesa-clc. Big-endian: Mesa 26.2 has the r300 fixes (MR 42218).
GALLIUM_DRIVERS.i386 = softpipe,llvmpipe,i915,crocus,iris,r600,radeonsi
GALLIUM_DRIVERS.sparc = softpipe,r300,r600
CONFIGURE_OPTIONS += -Dgallium-drivers='$(GALLIUM_DRIVERS.$(MACH))'
ifeq ($(MACH),i386)
CONFIGURE_OPTIONS += -Dmesa-clc=enabled
CONFIGURE_OPTIONS += -Dprecomp-compiler=enabled
CONFIGURE_OPTIONS += -Dstatic-libclc=all
PKG_CONFIG_PATH.prepend += /usr/clang/21/lib/pkgconfig /usr/clang/21/share/pkgconfig
endif
CONFIGURE_OPTIONS += -Dvulkan-drivers=''
CONFIGURE_OPTIONS += -Dgbm=enabled
CONFIGURE_OPTIONS += -Dglvnd=disabled
# Wayland currently requires linux specific socket options and epoll for events.
CONFIGURE_OPTIONS += -Dplatforms='x11'
ifeq ($(MACH),i386)
CONFIGURE_OPTIONS += -Dllvm=enabled
CONFIGURE_OPTIONS += -Dshared-llvm=enabled
else
CONFIGURE_OPTIONS += -Dllvm=disabled
endif
CONFIGURE_OPTIONS += -Dglx=dri
CONFIGURE_OPTIONS += -Degl=enabled
CONFIGURE_OPTIONS += -Dgles1=enabled
CONFIGURE_OPTIONS += -Dgles2=enabled
CONFIGURE_OPTIONS += -Dshader-cache=enabled
# VA-API (libva): radeonsi_drv_video.so in /usr/lib/amd64/dri, libva's
# driver directory, for UVD/VCE (Polaris: H.264, HEVC, VC-1 decode; H.264,
# HEVC encode) and the Tahiti UVD 3.1.  All codecs: the hardware decides.
ifeq ($(MACH),i386)
CONFIGURE_OPTIONS += -Dgallium-va=enabled
CONFIGURE_OPTIONS += -Dvideo-codecs=all
CONFIGURE_OPTIONS += -Dva-libs-path=$(USRLIBDIR64)/dri
else
CONFIGURE_OPTIONS += -Dgallium-va=disabled
endif
CONFIGURE_OPTIONS += -Dvalgrind=disabled
CONFIGURE_OPTIONS += -Dlibunwind=disabled
CONFIGURE_OPTIONS += -Dlmsensors=disabled
CONFIGURE_OPTIONS += -Dzstd=enabled
CONFIGURE_OPTIONS += -Ddri-drivers-path='$(X11_SERVERMODS_DIR)/dri$(SERVERMOD_SUBDIR)'

CFLAGS += $(XPG7MODE)
CFLAGS += -Wno-incompatible-pointer-types -D_POSIX_PTHREAD_SEMANTICS
CPPFLAGS += -D__STDC_CONSTANT_MACROS -D__STDC_LIMIT_MACROS -Drestrict=__restrict__ -DHAVE_NOATEXIT
#LDFLAGS += -Wl,-zgnu-version-script-compat
# This code has init cycles and multiple C++ libraries, and misbehaves
# when lazy loading is used. Since we're unable to restructure third
# party code at that level, we must instead disable lazy loading.
LDFLAGS += -Wl,-znolazyload
# libgallium (llvmpipe, radeonsi) links OpenIndiana LLVM 21, which lives in
# /usr/clang/21/lib: give it the runpath to find libLLVM.so.21.1.
ifeq ($(MACH),i386)
LDFLAGS += -Wl,-R/usr/clang/21/lib
endif
LDFLAGS += -Wl,-z,rescan-now
CFLAGS += -Wstack-protector -fdata-sections -fstack-protector-all
LD_OPTIONS += -z ignore -lssp -lsocket -lnsl
#LD_OPTIONS += -z discard-unused=dependencies
#LD_OPTIONS += -M $(COMPONENT_DIR)/mapfile.externs
CONFIGURE_ENV += PYTHON=$(PYTHON)
ifeq ($(MACH),sparc)
# Mesa's generators need Python >= 3.10 with mako and PyYAML, which
# OpenIndiana SPARC does not have yet for its python-314: a build machine
# names a bin directory with such a python3.14 in MESA_PYTHON_BINDIR.
PATH.prepend += $(MESA_PYTHON_BINDIR)
# Workaround for an illumos ld bug: GNU binutils linked on sparc have _end
# inside their own data, so the heap overwrites it and gar dies with SIGBUS
# on libmesa_util.a.  Drop this when binutils has been relinked.
CONFIGURE_ENV += AR=/usr/bin/ar
endif
CONFIGURE_ENV += CPPFLAGS="$(CPPFLAGS)"

# Add RPATH to xorg lib directory where is libdrm.
# meson install drops the libdrm runpath it got from pkg-config, so set the
# runpath on every shared object Mesa installs: its own libraries, libdrm,
# the GCC runtime and LLVM (libgallium links libLLVM.so.21.1).
MESA_RUNPATH.i386 = /usr/lib/mesa/$(MACH64):/usr/lib/xorg/$(MACH64):$(GCC_LIBDIR):/usr/clang/21/lib
MESA_RUNPATH.sparc = /usr/lib/mesa/$(MACH64):/usr/lib/xorg/$(MACH64):$(GCC_LIBDIR)
MESA_RUNPATH = $(MESA_RUNPATH.$(MACH))

MESA_VA_DIR.i386 = $(PROTOUSRLIBDIR)/$(MACH64)/dri
COMPONENT_POST_INSTALL_ACTION.64 += \
	for f in `find $(PROTOUSRLIBDIR)/mesa/$(MACH64) \
	    $(PROTOUSRLIBDIR)/xorg/modules/dri/$(MACH64) \
	    $(MESA_VA_DIR.$(MACH)) \
	    -type f -name "*.so*"` ; do \
		elfedit -e "dyn:runpath $(MESA_RUNPATH)" $$f || exit 1 ; \
	done ; \
	mv $(PROTOUSRDIR)/include/GL $(PROTOUSRDIR)/include/mesa

# Manually added build dependencies
REQUIRED_PACKAGES += x11/header/x11-protocols
REQUIRED_PACKAGES += x11/library/libpthread-stubs
REQUIRED_PACKAGES += system/header/header-drm
REQUIRED_PACKAGES += library/graphics/libvdpau
ifeq ($(MACH),i386)
REQUIRED_PACKAGES += library/graphics/libva
REQUIRED_PACKAGES += developer/llvm/spirv-llvm-translator-21
REQUIRED_PACKAGES += developer/llvm/libclc-21
endif
# Auto-generated dependencies
REQUIRED_PACKAGES += $(CLANG_RUNTIME_PKG)
REQUIRED_PACKAGES += $(GCC_RUNTIME_PKG)
REQUIRED_PACKAGES += $(GXX_RUNTIME_PKG)
REQUIRED_PACKAGES += compress/zstd
REQUIRED_PACKAGES += library/expat
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += service/opengl/ogl-select
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += x11/header/x11-protocols
REQUIRED_PACKAGES += x11/library/glu
REQUIRED_PACKAGES += x11/library/libdrm
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxcb
REQUIRED_PACKAGES += x11/library/libxext
REQUIRED_PACKAGES += x11/library/libxshmfence
REQUIRED_PACKAGES += x11/library/libxxf86vm
REQUIRED_PACKAGES += x11/library/spirv-tools
