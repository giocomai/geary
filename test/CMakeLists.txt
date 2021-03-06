# Geary build script
# Copyright 2016 Software Freedom Conservancy Inc.
# Copyright 2016 Michael Gratton <mike@vee.net>

set(TEST_SRC
  main.vala
  testcase.vala # Taken as-is from libgee, courtesy Julien Peeters

  engine/rfc822-mailbox-address-test.vala
  engine/rfc822-message-test.vala
  engine/rfc822-message-data-test.vala
  engine/rfc822-utils-test.vala
  engine/util-html-test.vala

  client/application/geary-configuration-test.vala
)

# Vala
find_package(Vala REQUIRED)
include(ValaVersion)
include(ValaPrecompile)

pkg_check_modules(DEPS REQUIRED
  gee-0.8
  gio-2.0
  glib-2.0
  gmime-2.6
  gthread-2.0
  gtk+-3.0
  libsoup-2.4
  webkitgtk-3.0
)

set(TEST_PACKAGES
  geary-client
  geary-engine
  gee-0.8
  gio-2.0
  glib-2.0
  gmime-2.6
  gtk+-3.0
  libsoup-2.4
  webkitgtk-3.0
)

set(CFLAGS
    ${DEPS_CFLAGS}
    ${DEPS_CFLAGS_OTHER}
    -D_BUILD_ROOT_DIR=\"${CMAKE_BINARY_DIR}\"
    -D_SOURCE_ROOT_DIR=\"${CMAKE_SOURCE_DIR}\"
)

include_directories(${CMAKE_BINARY_DIR}/src)

set(LIB_PATHS ${DEPS_LIBRARY_DIRS})
link_directories(${LIB_PATHS})
add_definitions(${CFLAGS})

set(VALAC_OPTIONS
    --vapidir=${CMAKE_BINARY_DIR}/src
    --vapidir=${CMAKE_SOURCE_DIR}/bindings/vapi
    --metadatadir=${CMAKE_SOURCE_DIR}/bindings/metadata
    --target-glib=${TARGET_GLIB}
    --thread
    --debug
    --enable-checking
    --enable-deprecated
    --fatal-warnings
    ${EXTRA_VALA_OPTIONS}
)

vala_precompile(TEST_VALA_C geary-test
    ${TEST_SRC}
PACKAGES
    ${TEST_PACKAGES}
OPTIONS
    ${VALAC_OPTIONS}
)

add_executable(geary-test ${TEST_VALA_C})
target_link_libraries(geary-test ${DEPS_LIBRARIES} geary-client geary-engine)
