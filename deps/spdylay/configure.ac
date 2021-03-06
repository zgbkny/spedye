dnl Spdylay - SPDY Library

dnl Copyright (c) 2012 Tatsuhiro Tsujikawa

dnl Permission is hereby granted, free of charge, to any person obtaining
dnl a copy of this software and associated documentation files (the
dnl "Software"), to deal in the Software without restriction, including
dnl without limitation the rights to use, copy, modify, merge, publish,
dnl distribute, sublicense, and/or sell copies of the Software, and to
dnl permit persons to whom the Software is furnished to do so, subject to
dnl the following conditions:

dnl The above copyright notice and this permission notice shall be
dnl included in all copies or substantial portions of the Software.

dnl THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND,
dnl EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
dnl MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND
dnl NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE
dnl LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION
dnl OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
dnl WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
AC_PREREQ(2.61)
AC_INIT([spdylay], [0.1.0], [t-tujikawa@users.sourceforge.net])
LT_PREREQ([2.2.6])
LT_INIT()
dnl See versioning rule:
dnl  http://www.gnu.org/software/libtool/manual/html_node/Updating-version-info.html
AC_SUBST(LT_CURRENT, 0)
AC_SUBST(LT_REVISION, 0)
AC_SUBST(LT_AGE, 0)

AC_CANONICAL_BUILD
AC_CANONICAL_HOST
AC_CANONICAL_TARGET

AC_CONFIG_MACRO_DIR([m4])

AM_INIT_AUTOMAKE()
AC_CONFIG_HEADERS([config.h])

dnl Checks for programs
AC_PROG_CC
AC_PROG_CXX
AC_PROG_INSTALL
AC_PROG_LN_S
AC_PROG_MAKE_SET
PKG_PROG_PKG_CONFIG([0.20])

AC_COMPILE_STDCXX_11
AM_CONDITIONAL([HAVE_STDCXX_11],
               [ test "x$ac_cv_cxx_compile_cxx11_cxx" = "xyes" ])

# Checks for libraries.

# zlib
PKG_CHECK_MODULES([ZLIB], [zlib >= 1.2.3])
LIBS="$ZLIB_LIBS $LIBS"
CFLAGS="$CFLAGS $ZLIB_CFLAGS"

# cunit
PKG_CHECK_MODULES([CUNIT], [cunit >= 2.1], [have_cunit=yes], [have_cunit=no])
# If pkg-config does not find cunit, check it using AC_CHECK_LIB.  We
# do this because Debian (Ubuntu) lacks pkg-config file for cunit.
if test "x${have_cunit}" = "xno"; then
  AC_MSG_WARN([${CUNIT_PKG_ERRORS}])
  AC_CHECK_LIB([cunit], [CU_initialize_registry],
               [have_cunit=yes], [have_cunit=no])
  if test "x${have_cunit}" = "xyes"; then
    CUNIT_LIBS="-lcunit"
    CUNIT_CFLAGS=""
    AC_SUBST([CUNIT_LIBS])
    AC_SUBST([CUNIT_CFLAGS])
  fi
fi
if test "x${have_cunit}" = "xyes"; then
  # cunit in Mac OS X requires ncurses. Note that in Mac OS X, test
  # program can be built without -lncurses, but it emits runtime
  # error.
  case "${build}" in
    *-apple-darwin*)
      CUNIT_LIBS="$CUNIT_LIBS -lncurses"
      AC_SUBST([CUNIT_LIBS])
      ;;
  esac
fi

AM_CONDITIONAL([HAVE_CUNIT], [ test "x${have_cunit}" = "xyes" ])

# openssl (for examples)
PKG_CHECK_MODULES([OPENSSL], [openssl >= 1.0.1])

# Checks for header files.
AC_CHECK_HEADERS([ \
  arpa/inet.h \
  netinet/in.h \
  stddef.h \
  stdint.h \
  stdlib.h \
  string.h \
  time.h \
  unistd.h \
])

# Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_SIZE_T
AC_TYPE_SSIZE_T
AC_TYPE_UINT8_T
AC_TYPE_UINT16_T
AC_TYPE_UINT32_T
AC_TYPE_UINT64_T
AC_CHECK_TYPES([ptrdiff_t])
AC_C_BIGENDIAN

# Checks for library functions.
if test "x$cross_compiling" != "xyes"; then
  AC_FUNC_MALLOC
fi
AC_CHECK_FUNCS([ \
  memmove \
  memset \
])

AX_HAVE_EPOLL([have_epoll=yes], [have_epoll=no])
if test "x${have_epoll}" = "xyes"; then
  AC_DEFINE([HAVE_EPOLL], [1], [Define to 1 if you have the `epoll`.])
fi
AM_CONDITIONAL([HAVE_EPOLL], [ test "x${have_epoll}" = "xyes" ])

AC_CHECK_FUNCS([kqueue], [have_kqueue=yes])
AM_CONDITIONAL([HAVE_KQUEUE], [test "x${have_kqueue}" = "xyes"])

AC_CONFIG_FILES([
  Makefile
  lib/Makefile
  lib/libspdylay.pc
  lib/includes/Makefile
  lib/includes/spdylay/spdylayver.h
  tests/Makefile
  examples/Makefile
  doc/Makefile
  doc/conf.py
])
AC_OUTPUT

AC_MSG_NOTICE([summary of build options:

    version:        ${VERSION} shared $LT_CURRENT:$LT_REVISION:$LT_AGE
    Host type:      ${host}
    Install prefix: ${prefix}
    C compiler:     ${CC}
    CFlags:         ${CFLAGS}
    Library types:  Shared=${enable_shared}, Static=${enable_static}
    CUnit:          ${have_cunit}
])
