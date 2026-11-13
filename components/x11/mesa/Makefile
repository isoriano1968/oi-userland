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
# Mesa 26 needs Python >= 3.10 (mako, PyYAML, packaging) for its generators.
PYTHON_VERSION = 3.14
include ../../../make-rules/shared-macros.mk

COMPONENT_NAME= mesa
COMPONENT_VERSION= 26.2.6
COMPONENT_SUMMARY= The Mesa 3-D Graphics Library
COMPONENT_SRC= mesa-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE= mesa-$(COMPONENT_VERSION).tar.xz
COMPONENT_ARCHIVE_HASH= sha256:b9436b3e1a8be76fbd4823dc884f90596ab7c6ac5e1867a305e66ffccfb31a92
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

CONFIGURE_OPTIONS += --libdir='lib/mesa/amd64'


# Mesa 26: i965 is gone (crocus drives Haswell and older Intel), osmesa,
# xvmc and xa were removed upstream.  radeonsi (GCN: Tahiti in the Mac Pro
# 2013 up to Navi) needs LLVM with the AMDGPU target: OpenIndiana's LLVM 21.
# crocus needs mesa-clc (SPIRV-LLVM-Translator, not yet in OI): first build without it.
CONFIGURE_OPTIONS += -Dgallium-drivers='softpipe,llvmpipe,i915,r600,radeonsi'
CONFIGURE_OPTIONS += -Dvulkan-drivers=''
CONFIGURE_OPTIONS += -Dgbm=enabled
CONFIGURE_OPTIONS += -Dglvnd=disabled
# Wayland currently requires linux specific socket options and epoll for events.
CONFIGURE_OPTIONS += -Dplatforms='x11'
CONFIGURE_OPTIONS += -Dllvm=enabled
CONFIGURE_OPTIONS += -Dshared-llvm=enabled
CONFIGURE_OPTIONS += -Dglx=dri
CONFIGURE_OPTIONS += -Degl=enabled
CONFIGURE_OPTIONS += -Dgles1=enabled
CONFIGURE_OPTIONS += -Dgles2=enabled
CONFIGURE_OPTIONS += -Dshader-cache=enabled
CONFIGURE_OPTIONS += -Dgallium-va=disabled
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
LDFLAGS += -Wl,-R/usr/clang/21/lib
LDFLAGS += -Wl,-z,rescan-now
CFLAGS += -Wstack-protector -fdata-sections -fstack-protector-all
LD_OPTIONS += -z ignore -lssp -lsocket -lnsl
#LD_OPTIONS += -z discard-unused=dependencies
#LD_OPTIONS += -M $(COMPONENT_DIR)/mapfile.externs
CONFIGURE_ENV += PYTHON=$(PYTHON)
CONFIGURE_ENV += CPPFLAGS="$(CPPFLAGS)"

# Add RPATH to xorg lib directory where is libdrm.
# meson install drops the libdrm runpath it got from pkg-config, so set the
# runpath on every shared object Mesa installs: its own libraries, libdrm,
# the GCC runtime and LLVM (libgallium links libLLVM.so.21.1).
MESA_RUNPATH = /usr/lib/mesa/$(MACH64):/usr/lib/xorg/$(MACH64):$(GCC_LIBDIR):/usr/clang/21/lib

COMPONENT_POST_INSTALL_ACTION.64 += \
	for f in `find $(PROTOUSRLIBDIR)/mesa/$(MACH64) \
	    $(PROTOUSRLIBDIR)/xorg/modules/dri/$(MACH64) \
	    -type f -name "*.so*"` ; do \
		elfedit -e "dyn:runpath $(MESA_RUNPATH)" $$f || exit 1 ; \
	done ; \
	mv $(PROTOUSRDIR)/include/GL $(PROTOUSRDIR)/include/mesa

# Manually added build dependencies
REQUIRED_PACKAGES += x11/header/x11-protocols
REQUIRED_PACKAGES += x11/library/libpthread-stubs
REQUIRED_PACKAGES += system/header/header-drm
REQUIRED_PACKAGES += library/graphics/libvdpau

# Auto-generated dependencies
REQUIRED_PACKAGES += $(GCC_RUNTIME_PKG)
REQUIRED_PACKAGES += $(GXX_RUNTIME_PKG)
REQUIRED_PACKAGES += library/expat
REQUIRED_PACKAGES += compress/zstd
REQUIRED_PACKAGES += library/zlib
REQUIRED_PACKAGES += service/opengl/ogl-select
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += system/library/math
REQUIRED_PACKAGES += x11/header/x11-protocols
REQUIRED_PACKAGES += x11/library/glu
REQUIRED_PACKAGES += x11/library/libdrm
REQUIRED_PACKAGES += developer/clang-21
REQUIRED_PACKAGES += runtime/clang-21
REQUIRED_PACKAGES += library/python/mako-314
REQUIRED_PACKAGES += library/python/packaging-314
REQUIRED_PACKAGES += library/python/pyyaml-314
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxcb
REQUIRED_PACKAGES += x11/library/libxext
REQUIRED_PACKAGES += x11/library/libxfixes
REQUIRED_PACKAGES += x11/library/libxshmfence
REQUIRED_PACKAGES += x11/library/libxv
REQUIRED_PACKAGES += x11/library/libxvmc
REQUIRED_PACKAGES += x11/library/libxxf86vm
