dnl Process this file with autoconf to produce a configure script.

AC_PREREQ(2.57)
AC_INIT(libbnxtre, m4_esyscmd(echo -n `grep "LIBBNXT_RE_BUILD_VERSION" \
        src/bnxt_re_version.h | head -1 | awk '{print $3}'`))
AC_CONFIG_SRCDIR([src/bnxt_re_main.h])
AC_CONFIG_AUX_DIR(config)
AM_CONFIG_HEADER(config.h)
AM_INIT_AUTOMAKE(subdir-objects)
AM_PROG_LIBTOOL

AC_ARG_ENABLE(libcheck, [ --disable-libcheck    do not test for the presence of ib libraries],
[	if test x$enableval = xno ; then
		disable_libcheck=yes
	fi
])

dnl Checks for programs
AC_PROG_CC
AC_CHECK_SIZEOF(long)

dnl Checks for libraries
if test "$disable_libcheck" != "yes"
then
AC_CHECK_LIB(ibverbs, ibv_get_device_list, [],
    AC_MSG_ERROR([ibv_get_device_list() not found.  libbnxtre requires libibverbs.]))
fi

dnl Checks for header files.
AC_CHECK_HEADERS(sysfs/libsysfs.h)

if test "$disable_libcheck" != "yes"
then
AC_CHECK_HEADER(infiniband/driver.h, [],
    AC_MSG_ERROR([<infiniband/driver.h> not found.  Is libibverbs installed?]))
AC_HEADER_STDC
fi

dnl Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST

dnl Checks for library functions
AC_CHECK_FUNCS(ibv_read_sysfs_file ibv_register_driver)

dnl Check for libibverbs device library extension
dummy=if$$
cat <<IBV_VERSION > $dummy.c
#include <infiniband/driver.h>
IBV_DEVICE_LIBRARY_EXTENSION
IBV_VERSION
IBV_DEVICE_LIBRARY_EXTENSION=`$CC $CPPFLAGS -E $dummy.c 2> /dev/null | tail -1`
rm -f $dummy.c
if test $IBV_DEVICE_LIBRARY_EXTENSION = IBV_DEVICE_LIBRARY_EXTENSION; then
    AC_MSG_ERROR([IBV_DEVICE_LIBRARY_EXTENSION not defined.  Is libibverbs new enough?])
fi
AC_SUBST(IBV_DEVICE_LIBRARY_EXTENSION)

AC_MSG_CHECKING([for new definition of struct ibv_send_wr])
dummy=if$$
cat << WR_MW_BIND > $dummy.c
#include <infiniband/verbs.h>
struct ibv_send_wr wr = {
        .bind_mw.rkey = 0
        };
WR_MW_BIND
BIND_RESULT=`$CC $CPPFLAGS -c $dummy.c 2> /dev/null`
if test -f $dummy.o; then
    AC_MSG_RESULT(yes)
    AC_DEFINE([HAVE_WR_BIND_MW],[1],[ibv_send_wr has bind_mw member])
else
    AC_MSG_RESULT(no)
fi
rm -f $dummy.*

AC_MSG_CHECKING([for compile time macro definitions])
AC_MSG_RESULT(yes)
AC_DEFINE([HAVE_J8916_ENABLED],[1],[have eng-8916 definition])

AC_CACHE_CHECK(whether ld accepts --version-script, ac_cv_version_script,
    if test -n "`$LD --help < /dev/null 2>/dev/null | grep version-script`"; then
        ac_cv_version_script=yes
    else
        ac_cv_version_script=no
    fi)

AM_CONDITIONAL(HAVE_LD_VERSION_SCRIPT, test "$ac_cv_version_script" = "yes")
AM_CONDITIONAL(FMODEL, test "$FMODEL" = "1")

AC_CONFIG_FILES([Makefile libbnxtre.spec])
AC_OUTPUT
