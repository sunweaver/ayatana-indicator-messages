CLEANFILES=
check_LTLIBRARIES = libgtest.la
check_PROGRAMS = test-gactionmuxer

TESTS = $(check_PROGRAMS)

AM_CPPFLAGS = $(GTEST_CPPFLAGS) \
	-I${top_srcdir}/src

######################################
# Google Test
######################################

nodist_libgtest_la_SOURCES = \
	$(GTEST_SOURCE)/src/gtest-all.cc \
	$(GTEST_SOURCE)/src/gtest_main.cc
libgtest_la_CPPFLAGS = \
	$(GTEST_CPPFLAGS) -w \
	$(AM_CPPFLAGS)
libgtest_la_CXXFLAGS = \
	$(AM_CXXFLAGS)

######################################
# GActionMixer
######################################

test_gactionmuxer_SOURCES = \
	test-gactionmuxer.cpp

test_gactionmuxer_CPPFLAGS = \
	$(APPLET_CFLAGS) \
	$(AM_CPPFLAGS)

test_gactionmuxer_LDADD = \
	$(APPLET_LIBS) \
	libindicator-messages-service.la \
	libgtest.la

######################################
# Indicator Test
######################################

SCHEMA_COMPILED_DIR="$(abs_builddir)/gsettings-schemas-compiled/"

indicator_test_SOURCES = \
	indicator-test.cpp

indicator_test_CPPFLAGS = \
	-DINDICATOR_MESSAGES_SERVICE_BINARY="\"$(abs_top_builddir)/src/ayatana-indicator-messages-service\"" \
	-DSCHEMA_DIR="\"$(SCHEMA_COMPILED_DIR)\"" \
	-DXDG_DATA_DIRS="\"$(abs_srcdir)/\"" \
	-I$(top_srcdir)/libmessaging-menu \
	-std=c++11 \
	$(APPLET_CFLAGS) \
	$(DBUSTEST_CFLAGS) \
	$(AM_CPPFLAGS)

indicator_test_LDADD = \
	$(APPLET_LIBS) \
	$(DBUSTEST_LIBS) \
	$(top_builddir)/libmessaging-menu/libmessaging-menu.la \
	libgtest.la \
	-lc -lpthread

indicator-test.cpp: schemas-compiled.stamp

schemas-compiled.stamp: $(top_srcdir)/data/*gschema.xml
	@rm -rf $(SCHEMA_COMPILED_DIR)
	@mkdir -p $(SCHEMA_COMPILED_DIR)
	@cp -f $(top_srcdir)/data/*gschema.xml $(SCHEMA_COMPILED_DIR)
	@`pkg-config gio-2.0 --variable glib_compile_schemas` $(SCHEMA_COMPILED_DIR)
	@touch schemas-compiled.stamp

CLEANFILES += schemas-compiled.stamp
TESTS += indicator-test
check_PROGRAMS += indicator-test

######################################
# Lib containing code under test
######################################

noinst_LTLIBRARIES = \
	libindicator-messages-service.la

libindicator_messages_service_la_SOURCES = \
	$(top_builddir)/common/indicator-messages-service.c \
	$(top_builddir)/common/indicator-messages-service.h \
	$(top_srcdir)/src/gactionmuxer.c \
	$(top_srcdir)/src/gactionmuxer.h \
	$(top_srcdir)/src/dbus-data.h

libindicator_messages_service_ladir = \
	$(includedir)/libayatana-indicator-messages-service/

libindicator_messages_service_la_CFLAGS = \
	$(APPLET_CFLAGS) \
	$(COVERAGE_CFLAGS) \
	-I$(top_builddir)/src \
	-I$(top_builddir)/common \
	-Wall \
	-Wl,-Bsymbolic-functions \
	-Wl,-z,defs \
	-Wl,--as-needed \
	-Werror -Wno-error=deprecated-declarations \
	-DG_LOG_DOMAIN=\"Ayatana-Indicator-Messages\"

libindicator_messages_service_la_LIBADD = \
	$(APPLET_LIBS)

libindicator_messages_service_la_LDFLAGS = \
	$(COVERAGE_LDFLAGS)

######################################
# Test client with dbusmock
######################################

TESTS_ENVIRONMENT = \
	export LD_LIBRARY_PATH=$(top_builddir)/libmessaging-menu/.libs; \
	export GI_TYPELIB_PATH=$(top_builddir)/libmessaging-menu; \
	export XDG_DATA_DIRS=$(abs_srcdir);

TESTS += test-client.py
