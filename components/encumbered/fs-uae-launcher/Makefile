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
# emulators/fs-uae-launcher -- the FS-UAE Launcher (Python 3, PyQt5), next
# to oi-userland's encumbered/fs-uae (emulators/fs-uae), the emulator it
# drives.  A Solaris 11.4 port of the same version is kept in step with it.
#
# Pure Python.  Upstream's own Makefile does the work: "mo" builds the
# message catalogs, "install" runs setup.py (distutils) into
# $(prefix)/share/fs-uae-launcher and links $(prefix)/bin/fs-uae-launcher to
# it.  It calls python3, which is 3.9 on OpenIndiana -- the only Python with
# PyQt5 here (library/python/pyqt5-39), so the launcher is a 3.9 package.
# 3.2.35 prefers PyQt6 and falls back to PyQt5; it imports PyOpenGL at
# startup (packages/python/PyOpenGL, with an illumos libGL fix).  It no
# longer uses pkg_resources, so the 3.1.70 patch is gone.
#

BUILD_BITS =		NO_ARCH
BUILD_STYLE =		justmake

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME =	fs-uae-launcher
COMPONENT_VERSION =	3.2.35
ifeq ($(MACH),sparc)
# SPARC only: the first SPARC build (2026-11-23) had the build host's home
# path in its files; rebuilt with neutral paths as the next revision (x86 is
# unchanged)
COMPONENT_REVISION=	1
endif
COMPONENT_SUMMARY =	FS-UAE Launcher, the configuration front end of FS-UAE
COMPONENT_PROJECT_URL =	https://fs-uae.net/
COMPONENT_FMRI =	emulators/fs-uae-launcher
COMPONENT_CLASSIFICATION = Applications/System Utilities
COMPONENT_SRC =		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE =	$(COMPONENT_SRC).tar.xz
COMPONENT_ARCHIVE_URL =	https://github.com/FrodeSolheim/fs-uae-launcher/releases/download/v$(COMPONENT_VERSION)/$(COMPONENT_ARCHIVE)
COMPONENT_ARCHIVE_HASH = sha256:cdfd74cd99281931a904340d414c7ebc44ddbdd0d0d599b3eb9818d58105dc25
COMPONENT_LICENSE =	GPLv2
COMPONENT_LICENSE_FILE = fs-uae-launcher.license

include $(WS_MAKE_RULES)/encumbered.mk
include $(WS_MAKE_RULES)/common.mk

PATH =			$(PATH.gnu)

# A real copy of the source tree, not cloney's symlink farm: upstream's
# install-data does "cp -a share/*", which would deliver the symlinks.
CLONEY_MODE =		copy

# Upstream's Makefile: "mo" to build, "install" with DESTDIR and prefix.
COMPONENT_BUILD_TARGETS = mo
COMPONENT_INSTALL_TARGETS = install
COMPONENT_INSTALL_ARGS += prefix=$(USRDIR)

# setup.py writes "#!/usr/bin/python3" into the launcher script; pkgdepend
# refuses an executable Python script that names no version, and the
# package is a Python 3.9 one (PyQt5 exists only for 3.9 here).
COMPONENT_POST_INSTALL_ACTION += \
	$(GSED) -i '1s|^\#!/usr/bin/python3$$|\#!/usr/bin/python3.9|' \
		$(PROTO_DIR)$(USRSHAREDIR)/fs-uae-launcher/fs-uae-launcher

TEST_TARGET =		$(NO_TESTS)

# PyOpenGL, PyQt5 and requests are needed only at run time; the manifest
# names them with explicit depend actions (pyopengl-39 and requests-39 are
# not needed to build, so they are not listed here, where prep would install
# them).
REQUIRED_PACKAGES += library/python/pyqt5-39
REQUIRED_PACKAGES += runtime/python-39
REQUIRED_PACKAGES += text/gnu-gettext
