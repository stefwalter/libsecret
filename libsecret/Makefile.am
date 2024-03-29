include $(top_srcdir)/Makefile.decl

SUBDIRS = . tests

AM_CPPFLAGS = \
	-I$(top_srcdir) \
	-DSECRET_COMPILATION \
	$(NULL)

lib_LTLIBRARIES = libsecret-@SECRET_MAJOR@.la


incdir = $(includedir)/libsecret-@SECRET_MAJOR@/libsecret

HEADER_FILES = \
	secret.h \
	secret-attributes.h \
	secret-collection.h \
	secret-item.h \
	secret-password.h \
	secret-paths.h \
	secret-prompt.h \
	secret-schema.h \
	secret-schemas.h \
	secret-service.h \
	secret-types.h \
	secret-value.h \
	$(NULL)

inc_HEADERS = \
	$(HEADER_FILES) \
	secret-enum-types.h \
	$(NULL)

BUILT_SOURCES = \
	secret-dbus-generated.c secret-dbus-generated.h \
	secret-enum-types.c secret-enum-types.h \
	$(NULL)

PUBLIC_FILES = \
	secret-attributes.h secret-attributes.c \
	secret-collection.h secret-collection.c \
	secret-item.h secret-item.c \
	secret-methods.c \
	secret-password.h secret-password.c \
	secret-prompt.h secret-prompt.c \
	secret-schema.h secret-schema.c \
	secret-schemas.h secret-schemas.c \
	secret-service.h secret-service.c \
	secret-types.h \
	secret-value.h secret-value.c \
	secret-paths.h secret-paths.c \
	$(NULL)

PRIVATE_FILES = \
	secret-private.h \
	secret-session.c \
	secret-util.c \
	$(NULL)

libsecret_@SECRET_MAJOR@_la_SOURCES = \
	$(PUBLIC_FILES) \
	$(PRIVATE_FILES) \
	$(BUILT_SOURCES) \
	$(NULL)

libsecret_@SECRET_MAJOR@_la_CFLAGS = \
	$(LIBGCRYPT_CFLAGS)

libsecret_@SECRET_MAJOR@_la_LDFLAGS = \
	-version-info $(SECRET_LT_RELEASE) \
	-no-undefined \
	-export-symbols-regex '^secret_|^SECRET_|^SECMEM_.*'

libsecret_@SECRET_MAJOR@_la_LIBADD = \
	$(top_builddir)/egg/libegg.la \
	$(LIBGCRYPT_LIBS) \
	$(LIBS)

noinst_LTLIBRARIES = libsecret-testable.la
libsecret_testable_la_SOURCES =
libsecret_testable_la_LIBADD = $(libsecret_@SECRET_MAJOR@_la_OBJECTS) \
	$(libsecret_@SECRET_MAJOR@_la_LIBADD)
libsecret_testable_la_DEPENDENCIES = $(libsecret_@SECRET_MAJOR@_la_OBJECTS)
libsecret_testable_la_LDFLAGS = -rpath /force/shared

DBUS_XML_DEFINITIONS = \
	$(srcdir)/org.freedesktop.Secrets.xml

temp-dbus-generated.c: $(DBUS_XML_DEFINITIONS) Makefile.am
	$(AM_V_GEN) gdbus-codegen --interface-prefix org.freedesktop.Secret. \
		--generate-c-code temp-dbus-generated --c-namespace SecretGen \
		$(DBUS_XML_DEFINITIONS)
secret-dbus-generated.c: temp-dbus-generated.c Makefile.am
	$(AM_V_GEN) sed -e '1i\
	#define GLIB_DISABLE_DEPRECATION_WARNINGS' \
		-e 's/secret_gen_/_secret_gen_/g' -e 's/type-/type/g' \
		-e 's/temp-dbus-generated.h/secret-dbus-generated.h/g' \
		temp-dbus-generated.c > secret-dbus-generated.c
secret-dbus-generated.h: temp-dbus-generated.c Makefile.am
	$(AM_V_GEN) sed -e 's/secret_gen_/_secret_gen_/g' -e 's/type-/type/g' \
		temp-dbus-generated.h > secret-dbus-generated.h

secret-enum-types.h: secret-enum-types.h.template $(HEADER_FILES)
	$(AM_V_GEN) $(GLIB_MKENUMS) --template $^ > $@

secret-enum-types.c: secret-enum-types.c.template $(HEADER_FILES)
	$(AM_V_GEN) $(GLIB_MKENUMS) --template $^ > $@

CLEANFILES =

# ------------------------------------------------------------------
# INTROSPECTION

if HAVE_INTROSPECTION

include $(INTROSPECTION_MAKEFILE)

INTROSPECTION_GIRS = Secret-@SECRET_MAJOR@.gir
INTROSPECTION_SCANNER_ARGS = $(INTROSPECTION_FLAGS) --warn-all --add-include-path=$(srcdir) --add-include-path=.
INTROSPECTION_COMPILER_ARGS = --includedir=$(srcdir) --includedir=.

Secret-@SECRET_MAJOR@.gir: libsecret-@SECRET_MAJOR@.la

Secret_@SECRET_MAJOR@_gir_NAMESPACE = Secret
Secret_@SECRET_MAJOR@_gir_EXPORT_PACKAGES = libsecret-@SECRET_MAJOR@
Secret_@SECRET_MAJOR@_gir_INCLUDES = GObject-2.0 Gio-2.0
Secret_@SECRET_MAJOR@_gir_LIBS = libsecret-@SECRET_MAJOR@.la
Secret_@SECRET_MAJOR@_gir_CFLAGS = -I$(top_srcdir) -I$(top_builddir) -DSECRET_COMPILATION
Secret_@SECRET_MAJOR@_gir_FILES = $(PUBLIC_FILES)
Secret_@SECRET_MAJOR@_gir_SCANNERFLAGS = --c-include "libsecret/secret.h"

girdir = $(datadir)/gir-1.0
gir_DATA = Secret-@SECRET_MAJOR@.gir

typelibsdir = $(libdir)/girepository-1.0
typelibs_DATA = $(INTROSPECTION_GIRS:.gir=.typelib)

if ENABLE_VAPIGEN
include $(VAPIGEN_MAKEFILE)

libsecret-@SECRET_MAJOR@.vapi: Secret-@SECRET_MAJOR@.gir Secret-@SECRET_MAJOR@.metadata libsecret-@SECRET_MAJOR@.deps

VAPIGEN_VAPIS = \
	libsecret-@SECRET_MAJOR@.vapi

libsecret_@SECRET_MAJOR@_vapi_DEPS = glib-2.0 gio-2.0
libsecret_@SECRET_MAJOR@_vapi_METADATADIRS = $(srcdir)
libsecret_@SECRET_MAJOR@_vapi_FILES = Secret-@SECRET_MAJOR@.gir

VAPI_DEPS = $(VAPIGEN_VAPIS:.vapi=.deps)

libsecret-$(SECRET_MAJOR).deps: Makefile.am
	$(AM_V_GEN) echo $(libsecret_@SECRET_MAJOR@_vapi_DEPS) | tr ' ' '\n' > $@

vapidir = $(datadir)/vala/vapi
vapi_DATA = \
	$(VAPIGEN_VAPIS) \
	$(VAPI_DEPS)

CLEANFILES += \
	$(VAPIGEN_VAPIS) \
	$(VAPI_DEPS)

endif # ENABLE_VAPIGEN
endif # HAVE_INTROSPECTION

# ------------------------------------------------------------------
# PKG CONFIG

libsecret-$(SECRET_MAJOR).pc: libsecret.pc
	cp libsecret.pc libsecret-$(SECRET_MAJOR).pc

pkgconfigdir = $(libdir)/pkgconfig
pkgconfig_DATA = \
	libsecret-$(SECRET_MAJOR).pc \
	libsecret-unstable.pc

# ------------------------------------------------------------------


EXTRA_DIST = \
	secret-enum-types.h.template \
	secret-enum-types.c.template \
	org.freedesktop.Secrets.xml \
	Secret-@SECRET_MAJOR@.metadata \
	$(NULL)

CLEANFILES += \
	$(pkgconfig_DATA) \
	$(gir_DATA) \
	$(typelibs_DATA) \
	$(noinst_DATA) \
	temp-dbus-generated.c \
	temp-dbus-generated.h \
	$(NULL)

DISTCLEANFILES = \
	secret-dbus-generated.c \
	secret-dbus-generated.h \
	$(NULL)

check-memory:
	make -C tests check-memory
