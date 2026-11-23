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
# Copyright 2026 Ignacio Soriano
#

# libva 2.24.1, the VA-API loader: DRM and X11 (DRI3) back ends, no GLX
# (deprecated upstream) and no Wayland.  Drivers are looked up in
# /usr/lib/amd64/dri, where Mesa's gallium VA frontend installs
# radeonsi_drv_video.so.  No source changes are needed on illumos.

BUILD_BITS=		64
BUILD_STYLE=		meson

ifeq ($(shell uname -p),sparc)
# OpenIndiana SPARC: gcc 13 is its newest.
GCC_VERSION = 13
endif
include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		libva
COMPONENT_VERSION=	2.24.1
COMPONENT_SUMMARY=	libva - Video Acceleration API (VA-API) loader
COMPONENT_PROJECT_URL=	https://github.com/intel/libva
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.bz2
COMPONENT_ARCHIVE_HASH=	\
	sha256:eec6050b52876f229bd35e9df17cd31a06785e18e6f7990c445b584628483d67
COMPONENT_ARCHIVE_URL=	$(COMPONENT_PROJECT_URL)/releases/download/$(COMPONENT_VERSION)/$(COMPONENT_ARCHIVE)
COMPONENT_FMRI=		library/graphics/libva
COMPONENT_CLASSIFICATION=	System/Multimedia Libraries
COMPONENT_LICENSE=	MIT
COMPONENT_LICENSE_FILE=	$(COMPONENT_NAME).license

TEST_TARGET=		$(NO_TESTS)

include $(WS_MAKE_RULES)/common.mk

CONFIGURE_OPTIONS +=	-Ddriverdir=$(USRLIBDIR64)/dri
CONFIGURE_OPTIONS +=	-Ddisable_drm=false
CONFIGURE_OPTIONS +=	-Dwith_x11=yes
CONFIGURE_OPTIONS +=	-Dwith_glx=no
CONFIGURE_OPTIONS +=	-Dwith_wayland=no
CONFIGURE_OPTIONS +=	-Denable_docs=false

# libdrm lives in /usr/lib/xorg/amd64.
LDFLAGS +=		-Wl,-R/usr/lib/xorg/$(MACH64)
# Auto-generated dependencies
REQUIRED_PACKAGES += system/library
REQUIRED_PACKAGES += x11/library/libdrm
REQUIRED_PACKAGES += x11/library/libx11
REQUIRED_PACKAGES += x11/library/libxcb
REQUIRED_PACKAGES += x11/library/libxext
REQUIRED_PACKAGES += x11/library/libxfixes
