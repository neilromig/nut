# Network UPS Tools: common

AM_CFLAGS = -I$(top_srcdir)/include
AM_LDFLAGS = -no-undefined
EXTRA_DIST =

noinst_LTLIBRARIES = libparseconf.la libcommon.la libcommonclient.la
libparseconf_la_SOURCES = parseconf.c

# do not hard depend on '../include/nut_version.h', since it blocks
# 'dist', and is only required for actual build, in which case
# BUILT_SOURCES (in ../include) will ensure nut_version.h will
# be built before anything else... but do depend on its build area:
if BUILDING_IN_TREE
# No need for symlink hack
common.c: $(top_builddir)/include/nut_version.h
else
# Surprisingly, for some "make" implementations this dependency means
# that the "common.c" required for builds below will be seeked in the
# current directory. So for out-of-tree builds like distcheck, we have
# to symlink the "real" source to build area:
common.c: $(top_builddir)/include/nut_version.h $(srcdir)/common.c
	test -s "$@" || ln -s -f "$(top_srcdir)/common/common.c" "$@"
endif

$(top_builddir)/include/nut_version.h:
	@cd $(@D) && $(MAKE) $(AM_MAKEFLAGS) $(@F)

libcommon_la_SOURCES = state.c str.c upsconf.c
libcommonclient_la_SOURCES = state.c str.c
if BUILDING_IN_TREE
libcommon_la_SOURCES += common.c
libcommonclient_la_SOURCES += common.c
else
nodist_libcommon_la_SOURCES = common.c
nodist_libcommonclient_la_SOURCES = common.c
CLEANFILES = $(top_builddir)/common/common.c
BUILT_SOURCES = common.c
endif

if HAVE_STRPTIME
EXTRA_DIST += strptime.c
else
# fall back to NetBSD implem
libcommon_la_SOURCES += strptime.c
libcommonclient_la_SOURCES += strptime.c
endif

if HAVE_STRNLEN
EXTRA_DIST += strnlen.c
else
# fall back to FreeBSD implem
libcommon_la_SOURCES += strnlen.c
libcommonclient_la_SOURCES += strnlen.c
endif

if HAVE_STRSEP
EXTRA_DIST += strsep.c
else
# fall back to simple implem
libcommon_la_SOURCES += strsep.c
libcommonclient_la_SOURCES += strsep.c
endif

# TODO: libnutwincompat.la?
if HAVE_WINDOWS
libcommon_la_SOURCES += wincompat.c $(top_srcdir)/include/wincompat.h
libcommonclient_la_SOURCES += wincompat.c $(top_srcdir)/include/wincompat.h
endif

# ensure inclusion of local implementation of missing systems functions
# using LTLIBOBJS. Refer to configure.in/.ac -> AC_REPLACE_FUNCS
libcommon_la_LIBADD = libparseconf.la @LTLIBOBJS@ @NETLIBS@
libcommonclient_la_LIBADD = libparseconf.la @LTLIBOBJS@ @NETLIBS@

libcommon_la_CFLAGS = $(AM_CFLAGS)
libcommonclient_la_CFLAGS = $(AM_CFLAGS)

# Did the user request, and build env support, tighter integration with
# libsystemd methods such as sd_notify()?
if WITH_LIBSYSTEMD
  libcommon_la_CFLAGS += $(LIBSYSTEMD_CFLAGS)
  libcommon_la_LIBADD += $(LIBSYSTEMD_LIBS)

# A typical client should not need this,
# but just in case (and to simplify linking)...
#  libcommonclient_la_CFLAGS += $(LIBSYSTEMD_CFLAGS)
#  libcommonclient_la_LIBADD += $(LIBSYSTEMD_LIBS)
  libcommonclient_la_CFLAGS += -DWITHOUT_LIBSYSTEMD=1
endif

MAINTAINERCLEANFILES = Makefile.in .dirstamp

# NOTE: Do not clean ".deps" in SUBDIRS of the main project,
# the root Makefile.am takes care of that!
#clean-local:
#	$(AM_V_at)rm -rf $(builddir)/.deps
