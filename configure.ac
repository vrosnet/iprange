#
# Copyright (C) 2015 Alon Bar-Lev <alon.barlev@gmail.com>
#
AC_PREREQ(2.60)

define([VERSION_MAJOR], [1])
define([VERSION_MINOR], [0])
define([VERSION_FIX], [4])
define([VERSION_NUMBER], VERSION_MAJOR[.]VERSION_MINOR[.]VERSION_FIX)
define([VERSION_SUFFIX], [_master])

dnl Set to "1" for a first RPM release of a new version
PACKAGE_RPM_RELEASE="0.0.$(echo VERSION_SUFFIX | sed s/^_//)"

AC_INIT([iprange], VERSION_NUMBER[]VERSION_SUFFIX)

AC_ARG_ENABLE([man],
              [AS_HELP_STRING([--disable-man], [disable manpage installation @<:@enabled@:>@])],
              ,
              [enable_man="yes"])
AM_CONDITIONAL([ENABLE_MAN], [test "${enable_man}" = "yes"])

AM_MAINTAINER_MODE([disable])
if test x"$USE_MAINTAINER_MODE" = xyes; then
AC_MSG_NOTICE(***************** MAINTAINER MODE *****************)
PACKAGE_BUILT_DATE=$(date '+%d %b %Y')

AX_NEED_PROG([HELP2MAN],[help2man],[])
else
  if test ! -f iprange.1; then
    if test x"$enable_man" = xyes; then
      AC_MSG_ERROR([docs not built, use '--disable-man' or --enable-maintainer-mode])
    fi
  fi
fi

PACKAGE_RPM_VERSION="VERSION_NUMBER"
AC_SUBST([PACKAGE_RPM_VERSION])
AC_SUBST([PACKAGE_RPM_RELEASE])

AC_CONFIG_AUX_DIR([.])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_SRCDIR([iprange.c])
AM_INIT_AUTOMAKE
AC_CANONICAL_HOST
AC_PROG_CC
AC_PROG_INSTALL
AC_ARG_ENABLE(
	[pedantic],
	[AS_HELP_STRING([--enable-pedantic], [enable pedantic compiler warnings])],
	,
	[enable_pedantic="no"]
)
AC_ARG_WITH(
	[compare-with-common],
	[AS_HELP_STRING([--without-compare-with-common], [if set, use MODE_COMMON to compare files this is 20 times faster than MODE COMBINE])],
	,
	[with_compare_with_common="yes"]
)
AC_ARG_WITH(
	[system-ip2str],
	[AS_HELP_STRING([--with-system-ip2str], [if set, use slower inet_ntoa])],
	,
	[with_system_ip2str="no"]
)

ACX_PTHREAD(, [AC_MSG_ERROR([Cannot initialize pthread environment])])
LIBS="${PTHREAD_LIBS} ${LIBS}"
CFLAGS="${CFLAGS} ${PTHREAD_CFLAGS}"
CC="${PTHREAD_CC}"

AC_TYPE_UINT32_T
AC_C_INLINE

test "${with_compare_with_common}" = "yes" && AC_DEFINE([COMPARE_WITH_COMMON], [1], [compare settings])
test "${with_system_ip2str}" = "yes" && AC_DEFINE([SYSTEM_IP2STR], [1], [ip2str settings])

if test "${GCC}" = "yes"; then
	AC_DEFINE_UNQUOTED([likely(x)], [__builtin_expect(!!(x), 1)], [gcc branch optimization])
	AC_DEFINE_UNQUOTED([unlikely(x)], [__builtin_expect(!!(x), 0)], [gcc branch optimization])
else
	AC_DEFINE_UNQUOTED([likely(x)], [(x)], [gcc branch optimization])
	AC_DEFINE_UNQUOTED([unlikely(x)], [(x)], [gcc branch optimization])
fi

if test "${enable_pedantic}" = "yes"; then
	enable_strict="yes"
	CFLAGS="${CFLAGS} -pedantic -Wall -Wextra"
fi
AC_CONFIG_FILES([
	Makefile
	iprange.spec
])
AC_OUTPUT

test "${with_compare_with_common}" != "yes" && AC_MSG_WARN([compare-with-common is not enabled. You should enable it, as it is a lot faster.]) || :
