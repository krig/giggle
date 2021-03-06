dnl Process this file with autoconf to produce a configure script.
AC_PREREQ(2.61)

AC_INIT([giggle],[0.4.91],
        [http://bugzilla.gnome.org/enter_bug.cgi?product=giggle])

AC_SUBST([LIBGIGGLE_ABI],     [0:0:0])
AC_SUBST([LIBGIGGLE_GIT_ABI], [0:0:0])

AC_CONFIG_SRCDIR([src/giggle-main.c])
AC_CONFIG_MACRO_DIR([m4])

AM_INIT_AUTOMAKE([-Wall -Werror])
AC_CONFIG_HEADERS([config.h])

AC_DISABLE_STATIC

CXX=no
F77=no

LT_PREREQ([2.2])
LT_INIT

AC_ISC_POSIX

AC_PROG_CC
AC_HEADER_STDC

AM_PATH_GLIB_2_0

dnl Defined in acinclude.m4
IMENDIO_COMPILE_WARNINGS

dnl  ------------------
dnl | Pkgconfig checks |---------------------------------------
dnl  ------------------

PKG_CHECK_MODULES(GIGGLE, [
        gio-2.0           >= 2.18
        glib-2.0          >= 2.18
        gthread-2.0       >= 2.16
        gtk+-2.0          >= 2.12
        gtksourceview-2.0 >  2.0
        libglade-2.0      >  2.4
])

dnl  -----------------------
dnl | Evolution Data Server |-----------------------------------
dnl  -----------------------

AC_ARG_ENABLE([evolution-data-server],
              [AS_HELP_STRING([--disable-evolution-data-server],
                              [disable features using Evolution Data Server])],,
              [enable_evolution_data_server=yes])

if test "$enable_evolution_data_server" = "yes"
then
  PKG_CHECK_MODULES(EBOOK, [libebook-1.2])
  AC_DEFINE(ENABLE_EDS, 1, [Use features from libebook])
fi

AM_CONDITIONAL([ENABLE_EDS], [test "$enable_evolution_data_server" = "yes"])

dnl  ------------------
dnl | terminal support |-----------------------------------
dnl  ------------------

AC_ARG_ENABLE([terminal],
              [AS_HELP_STRING([--disable-terminal],
                              [disable features using vte terminal widget])],,
              [enable_terminal=yes])

if test "$enable_terminal" = "yes"
then
  PKG_CHECK_MODULES(VTE, [vte >= 0.16])
  AC_DEFINE(ENABLE_VTE, 1, [Use features from vte])
fi

AM_CONDITIONAL([ENABLE_VTE], [test "$enable_terminal" = "yes"])

dnl  ----------------------
dnl | library requirements |-----------------------------------
dnl  ----------------------

AC_DEFINE(_GNU_SOURCE, 1, [Enable GNU Extensions])

dnl Make sure that strptime can be used (read: has gnu extensions)
AC_MSG_CHECKING([for GNU extensions of strptime()])
AC_RUN_IFELSE(
  AC_LANG_PROGRAM(
    [[#include <time.h>]],
    [[const char* date = "1168635922 +0100"; struct tm tm;
      char const* returned = strptime(date, "%s %z", &tm);
      return (!returned || *returned ? 1 : 0);]]),
    [AC_MSG_RESULT(available)
     AC_DEFINE([STRPTIME_HAS_GNU], [1],
               [strptime() has GNU extensions %s and %z])],
    [AC_MSG_RESULT(not available)])

dnl Figure out which format string to use for time_t
AC_MSG_CHECKING([size of time_t type])
AC_RUN_IFELSE(
  AC_LANG_PROGRAM(
    [[#include <time.h>]],
    [[return (sizeof (time_t) == sizeof (long) ? 0 : 1);]]),
  [AC_MSG_RESULT(long)
   AC_DEFINE([GIGGLE_FORMAT_TIME_T], ["ld"], [format string for time_t])],
  [AC_MSG_RESULT(int)
   AC_DEFINE([GIGGLE_FORMAT_TIME_T], ["d"], [format string for time_t])])

dnl  ------------------
dnl | Language Support |---------------------------------------
dnl  ------------------
GETTEXT_PACKAGE=giggle
AC_SUBST(GETTEXT_PACKAGE)
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE,"$GETTEXT_PACKAGE",[Gettext package name])
AM_GLIB_DEFINE_LOCALEDIR(LOCALEDIR)

AM_GLIB_GNU_GETTEXT
IT_PROG_INTLTOOL([0.35.0])

AC_SUBST([CONFIG_STATUS_DEPENDENCIES],['$(top_srcdir)/po/LINGUAS'])


dnl  -----------------------
dnl | GTK+ platform check   |----------------------------------
dnl  -----------------------
IGE_PLATFORM_CHECK
if test "x$IGE_PLATFORM" = xx11; then
   PLATFORM_CFLAGS=
   PLATFORM_LDFLAGS=
else
   PLATFORM_CFLAGS=
   PLATFORM_LDFLAGS="-framework Carbon"
fi
AC_SUBST(PLATFORM_CFLAGS)
AC_SUBST(PLATFORM_LDFLAGS)


dnl  -----------------------
dnl | GIT command detection |----------------------------------
dnl  -----------------------
AC_ARG_WITH([git-command],
            [AS_HELP_STRING([--with-git-command=PATH], [path to git command])])
GIT_COMMAND=$with_git_command

if test x"$GIT_COMMAND" = x; then
  AC_PATH_PROG(GIT_COMMAND, git, [no])
else
  if ! test -x "$GIT_COMMAND"; then
    GIT_COMMAND=no
  fi
fi

if test "$GIT_COMMAND" = no; then
  AC_MSG_ERROR([git command not found in path])
else
  AC_SUBST(GIT_COMMAND)
  AC_DEFINE_UNQUOTED(GIT_COMMAND,"$GIT_COMMAND",[GIT command path])
fi

dnl -----------------------------------------------------------

dnl This will cause the automake generated makefiles to pass the
dnl correct flags to aclocal.
ACLOCAL_AMFLAGS="\${ACLOCAL_FLAGS}"
AC_SUBST(ACLOCAL_AMFLAGS)

SHAVE_INIT([.])

AC_CONFIG_FILES([
        shave
        shave-libtool
        Makefile
        data/Makefile
        libgiggle/Makefile
        libgiggle-git/Makefile
        plugins/Makefile
        src/Makefile
        pixmaps/Makefile
        po/Makefile.in
        test/Makefile
])

AC_OUTPUT

echo
echo "Prefix:                    $prefix"
echo "GIT command path:          $GIT_COMMAND"
echo "Use Evolution Data Server: $enable_evolution_data_server"
echo "Use VTE terminal widget:   $enable_terminal"
echo
