dnl
dnl  amsynth configure.ac
dnl
dnl  Copyright (C) 2001-2015 Nick Dowell
dnl
dnl  This program is free software; you can redistribute it and/or modify
dnl  it under the terms of the GNU General Public License as published by
dnl  the Free Software Foundation; either version 2 of the License, or
dnl  (at your option) any later version.
dnl
dnl  This program is distributed in the hope that it will be useful,
dnl  but WITHOUT ANY WARRANTY; without even the implied warranty of
dnl  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
dnl  GNU General Public License for more details.
dnl
dnl  You should have received a copy of the GNU General Public License
dnl  along with this program; if not, write to the Free Software
dnl  Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA 02111-1307, USA.
dnl

AC_COPYRIGHT([Copyright (C) 2001-2015 Nick Dowell])
AC_INIT([amsynth], [git], [https://github.com/amsynth/amsynth/issues], [amsynth], [https://amsynth.github.io])
AC_CONFIG_SRCDIR([src/main.cc])
AC_CONFIG_HEADERS([config.h])
AM_INIT_AUTOMAKE([subdir-objects])
AM_SILENT_RULES([yes])

# make CXXFLAGS inherit from CFLAGS unless CXXFLAGS were specified
AS_IF([test "x$CXXFLAGS" = "x"], [CXXFLAGS="$CFLAGS"])

# capture user-supplied compiler flags before AC_PROG_CC clobbers them
user_CFLAGS="$CFLAGS"
user_CXXFLAGS="$CXXFLAGS"

AC_PROG_CC
AM_PROG_CC_C_O
AC_PROG_CXX
AC_PROG_INSTALL
LT_INIT

AC_LANG(C++)

dnl ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
dnl
dnl Required libraries & packages
dnl

AC_CHECK_LIB(m, sin, , exit)
AC_CHECK_LIB(pthread, pthread_create, [], exit)

AS_IF([test "x$with_gui" != "xno"],[PKG_CHECK_MODULES([GTK], [gtk+-2.0 >= 2.20.0])])

dnl ## http://www.gtkmm.org/docs/gtkmm-2.4/docs/reference/html/newin2p6s.html
dnl ## we use the following APIs only available in gtkmm 2.6 and up:
dnl ## Gtk::MessageDialog::set_secondary_text()
dnl ## Gtk::AboutDialog
AS_IF([test "x$with_gui" != "xno"],[PKG_CHECK_MODULES([GTKMM], [gtkmm-2.4 >= 2.6.0])])

dnl ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
dnl
dnl Optional packages
dnl

AC_ARG_WITH([gui], [AS_HELP_STRING([--with-gui], [build gui - enabled by default])])
AS_IF([test "x$with_gui" != "xno"], [
       AC_DEFINE([WITH_GUI],, [Build GUI])
       with_gui="yes"], [
       with_gui="no"
       with_lv2="no"
       with_dssi_gui="no"])
AM_CONDITIONAL([BUILD_GUI], [test "x$with_gui" != "xno"])

AC_ARG_WITH([oss], [AS_HELP_STRING([--with-oss], [build support for Open Sound System])])
AS_IF([test "x$with_oss" != "xno"], [
    AC_CHECK_HEADERS([sys/soundcard.h], [
        AC_DEFINE([WITH_OSS],, [Use this optional package])
        with_oss="yes"], [with_oss="no"])])

AC_ARG_WITH([alsa], [AS_HELP_STRING([--with-alsa], [build support for ALSA])])
AS_IF([test "x$with_alsa" != "xno"], [
    PKG_CHECK_MODULES([ALSA], [alsa], [
        AC_DEFINE([WITH_ALSA],, [Use this optional package])
        with_alsa="yes"], [with_alsa="no"])])

AC_ARG_WITH([jack], [AS_HELP_STRING([--with-jack], [build support for Jack Audio Connection Kit])])
AS_IF([test "x$with_jack" != "xno"],
    [PKG_CHECK_MODULES([JACK], [jack], [
        AC_DEFINE([WITH_JACK],, [Use this optional package])
        AC_CHECK_HEADERS([jack/midiport.h jack/session.h])
        with_jack="yes"], [with_jack="no"])])

AC_ARG_WITH([lash], [AS_HELP_STRING([--with-lash], [build support for LASH])])
AS_IF([test "x$with_lash" != "xno"], [
    PKG_CHECK_MODULES([LASH], [lash-1.0], [
        AC_DEFINE([WITH_LASH],, [Use this optional package])
        with_lash="yes"], [with_lash="no"])])

AC_ARG_WITH([sndfile], [AS_HELP_STRING([--with-sndfile], [use libsndfile for recording audio])])
AS_IF([test "x$with_sndfile" != "xno"], [
    PKG_CHECK_MODULES([SNDFILE], [sndfile], [
        AC_DEFINE([WITH_SNDFILE],, [Use this optional package])
        with_sndfile="yes"], [with_sndfile="no"])])

dnl

AC_ARG_WITH([dssi], [AS_HELP_STRING([--with-dssi], [build DSSI plugin])])
AS_IF([test "x$with_dssi" != "xno"], [
    PKG_CHECK_MODULES([DSSI], [dssi], [
        AS_IF([test "x$with_gui" != "xno"], [with_dssi_gui="yes"])
        PKG_CHECK_MODULES([LIBLO], [liblo], [], [with_dssi_gui="no"])
    with_dssi="yes"], [with_dssi="no"])])
AM_CONDITIONAL([BUILD_DSSI], [test "x$with_dssi" != "xno"])
AM_CONDITIONAL([BUILD_DSSI_GUI], [test "x$with_dssi_gui" == "xyes"])

AC_ARG_WITH([lv2], [AS_HELP_STRING([--with-lv2], [build support for LV2])])
AS_IF([test "x$with_lv2" != "xno"], [
    PKG_CHECK_MODULES([LV2], [[gtk+-2.0 >= 2.20.0]], [with_lv2="yes"], [with_lv2="no"])])
AM_CONDITIONAL([BUILD_LV2], [test "x$with_lv2" != "xno"])

AC_ARG_WITH([vst], [AS_HELP_STRING([--with-vst], [build VST plug-in] [default=yes])], [], [with_vst=yes])
AM_CONDITIONAL([BUILD_VST], [test "x$with_vst" != "xno"])

dnl
dnl
dnl
AC_ARG_ENABLE([realtime], [AS_HELP_STRING([--enable-realtime],
               [use the Linux SCHED_FIFO scheduler to improve performance,
                requires binary to be installed as root with suid bit enabled
                (default is no)])],
              [AC_DEFINE([ENABLE_REALTIME], [],
               [Use the Linux SCHED_FIFO scheduler to improve performance.])]
              )
AM_CONDITIONAL([ENABLE_REALTIME], [test x$enable_realtime != x])

AM_CONDITIONAL([DARWIN], [test "$(uname -s)" = "Darwin"])

AC_OUTPUT([
    Makefile
    data/amsynth.desktop
])

dnl
dnl
dnl	Output summary message..
dnl
dnl

echo
echo -------------------------------------------------------- $PACKAGE $VERSION
echo \|
AS_IF([test "x$with_gui" != "xno"], [
echo \| GTK version........................................... : `pkg-config --modversion gtk+-2.0`
echo \| GTK-- version......................................... : `pkg-config --modversion gtkmm-2.4`
])
echo \| Build with GUI support................................ : $with_gui
echo \|
echo \| Build with OSS support................................ : $with_oss
echo \| Build with ALSA support............................... : $with_alsa
echo \| Build with JACK support............................... : $with_jack
AS_IF([test "x$with_jack" != "xno"], [
echo \| Build with JACK MIDI support.......................... : $ac_cv_header_jack_midiport_h
echo \| Build with JACK session support....................... : $ac_cv_header_jack_session_h
])
echo \| Build with LASH support............................... : $with_lash
echo \| Build DSSI plugin..................................... : $with_dssi
AS_IF([test "x$with_dssi" != "xno"], [
echo \| Build DSSI GUI........................................ : $with_dssi_gui
])
echo \| Build LV2 plugin...................................... : $with_lv2
echo \| Build VST plugin...................................... : $with_vst
echo \|
echo \| Use libsndfile for .wav output support................ : $with_sndfile
echo
echo configure complete. now type \'make\' to build $PACKAGE
echo and then, as root, \'make install\' to install
echo
