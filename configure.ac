dnl Process this file with autoconf to produce a configure script. -*-m4-*-

AC_INIT([speex],[1.2.1],[speex-dev@xiph.org])

AC_CONFIG_SRCDIR([libspeex/speex.c])
AC_CONFIG_MACRO_DIR([m4])

dnl enable silent rules on automake 1.11 and later
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])


SPEEX_MAJOR_VERSION=1
SPEEX_MINOR_VERSION=2
SPEEX_MICRO_VERSION=1
SPEEX_EXTRA_VERSION=

SPEEX_LT_CURRENT=6
SPEEX_LT_REVISION=2
SPEEX_LT_AGE=5


AC_SUBST(SPEEX_LT_CURRENT)
AC_SUBST(SPEEX_LT_REVISION)
AC_SUBST(SPEEX_LT_AGE)

AM_INIT_AUTOMAKE([foreign no-define])
AM_MAINTAINER_MODE([enable])

AC_CANONICAL_HOST
LT_INIT

AC_C_BIGENDIAN
AC_C_CONST
AC_C_INLINE
AC_C_RESTRICT


AC_MSG_CHECKING(for C99 variable-size arrays)
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[]], [[
int foo;
foo = 10;
int array[foo];
]])],[has_var_arrays=yes;AC_DEFINE([VAR_ARRAYS], [], [Use C99 variable-size arrays])
],[has_var_arrays=no
])
AC_MSG_RESULT($has_var_arrays)

AC_CHECK_HEADERS([alloca.h getopt.h])
AC_MSG_CHECKING(for alloca)
AC_COMPILE_IFELSE([AC_LANG_PROGRAM([[
#ifdef HAVE_ALLOCA_H
# include <alloca.h>
#endif
#include <stdlib.h>
]], [[
int foo=10;
int *array = alloca(foo);
]])],[
has_alloca=yes;
if test x$has_var_arrays = "xno" ; then
AC_DEFINE([USE_ALLOCA], [], [Make use of alloca])
fi
],[has_alloca=no
])
AC_MSG_RESULT($has_alloca)

AC_MSG_CHECKING(for SSE in current arch/CFLAGS)
AC_LINK_IFELSE([
AC_LANG_PROGRAM([[
#include <xmmintrin.h>
__m128 testfunc(float *a, float *b) {
  return _mm_add_ps(_mm_loadu_ps(a), _mm_loadu_ps(b));
}
]])],
[
has_sse=yes
],
[
has_sse=no
]
)
AC_MSG_RESULT($has_sse)

SAVE_CFLAGS="$CFLAGS"
CFLAGS="$CFLAGS -fvisibility=hidden"
AC_MSG_CHECKING(for ELF visibility)
AC_COMPILE_IFELSE([
AC_LANG_PROGRAM([[
#pragma GCC visibility push(hidden)
__attribute__((visibility("default")))
int var=10;
]])],
[
has_visibility=yes
AC_DEFINE([EXPORT], [__attribute__((visibility("default")))], [Symbol visibility prefix])
],
[
has_visibility=no
AC_DEFINE([EXPORT], [], [Symbol visibility prefix])
CFLAGS="$SAVE_CFLAGS"
]
)
AC_MSG_RESULT($has_visibility)

AC_CHECK_HEADERS(sys/soundcard.h sys/audioio.h)

LT_LIB_M

# Check for getopt_long; if not found, use included source.
AC_CHECK_FUNCS([getopt_long],,
[# FreeBSD has a gnugetopt library.
  AC_CHECK_LIB([gnugetopt],[getopt_long],
[AC_DEFINE([HAVE_GETOPT_LONG])],
[# Use the GNU replacement.
AC_LIBOBJ(getopt)
AC_LIBOBJ(getopt1)])])

SPEEX_VERSION=$PACKAGE_VERSION

AC_SUBST(SPEEX_VERSION)

AC_DEFINE_UNQUOTED(SPEEX_VERSION, "${PACKAGE_VERSION}", [Complete version string])
AC_DEFINE_UNQUOTED(SPEEX_MAJOR_VERSION, ${SPEEX_MAJOR_VERSION}, [Version major])
AC_DEFINE_UNQUOTED(SPEEX_MINOR_VERSION, ${SPEEX_MINOR_VERSION}, [Version minor])
AC_DEFINE_UNQUOTED(SPEEX_MICRO_VERSION, ${SPEEX_MICRO_VERSION}, [Version micro])
AC_DEFINE_UNQUOTED(SPEEX_EXTRA_VERSION, "${SPEEX_EXTRA_VERSION}", [Version extra])

AC_ARG_ENABLE(valgrind, [  --enable-valgrind       Enable valgrind extra checks],
[if test "$enableval" = yes; then
  AC_DEFINE([ENABLE_VALGRIND], , [Enable valgrind extra checks])
fi])

AC_ARG_ENABLE(sse, [  --enable-sse            Enable SSE support], [
if test "x$enableval" != xno; then
has_sse=yes
CFLAGS="$CFLAGS -O3 -msse"
else
has_sse=no
fi
])


FFT=smallft

AC_ARG_ENABLE(fixed-point, [  --enable-fixed-point    Compile as fixed-point],
[if test "$enableval" = yes; then
  FFT=kiss
  has_sse=no
  AC_DEFINE([FIXED_POINT], , [Compile as fixed-point])
else
  AC_DEFINE([FLOATING_POINT], , [Compile as floating-point])
fi],
AC_DEFINE([FLOATING_POINT], , [Compile as floating-point]))

if test "$has_sse" = yes; then
  AC_DEFINE([_USE_SSE], , [Enable SSE support])
fi

AC_ARG_ENABLE(float-api, [  --disable-float-api     Disable the floating-point API],
[if test "$enableval" = no; then
  AC_DEFINE([DISABLE_FLOAT_API], , [Disable all parts of the API that are using floats])
fi])

AC_ARG_ENABLE(binaries, [  --disable-binaries   Do not build the encoder and decoder programs, only the library])
AS_IF([test "x$enable_binaries" != "xno"], [
  PKG_CHECK_MODULES([OGG], [ogg],
    AM_CONDITIONAL([BUILD_BINARIES], true),
    [AC_MSG_WARN([Ogg not found, not building example programs])
     AM_CONDITIONAL([BUILD_BINARIES], false)])],
  [AM_CONDITIONAL([BUILD_BINARIES], false)])

AC_ARG_ENABLE(vbr, [  --disable-vbr           Disable VBR and VAD from the codec],
[if test "$enableval" = no; then
  AC_DEFINE([DISABLE_VBR], , [Disable VBR and VAD from the codec])
fi])

AC_ARG_ENABLE(arm4-asm, [  --enable-arm4-asm       Make use of ARM4 assembly optimizations],
[if test "$enableval" = yes; then
  AC_DEFINE([ARM4_ASM], , [Make use of ARM4 assembly optimizations])
fi])

AC_ARG_ENABLE(arm5e-asm, [  --enable-arm5e-asm      Make use of ARM5E assembly optimizations],
[if test "$enableval" = yes; then
  AC_DEFINE([ARM5E_ASM], , [Make use of ARM5E assembly optimizations])
fi])

AC_ARG_ENABLE(blackfin-asm, [  --enable-blackfin-asm   Make use of Blackfin assembly optimizations],
[if test "$enableval" = yes; then
  AC_DEFINE([BFIN_ASM], , [Make use of Blackfin assembly optimizations])
fi])
WINMM_LIBS=""
case $host_os in
  uclinux) LDFLAGS="-Wl,-elf2flt=-s100000 $LDFLAGS";;
  *mingw*) WINMM_LIBS="-lwinmm";;
esac
AC_SUBST(WINMM_LIBS)

AC_ARG_ENABLE(fixed-point-debug, [  --enable-fixed-point-debug  Debug fixed-point implementation],
[if test "$enableval" = yes; then
  AC_DEFINE([FIXED_DEBUG], , [Debug fixed-point implementation])
fi])

AC_ARG_ENABLE(ti-c55x, [  --enable-ti-c55x        Enable support for TI C55X DSP],
[if test "$enableval" = yes; then
  has_char16=yes;
  AC_DEFINE([TI_C55X], , [Enable support for TI C55X DSP])
fi])

AC_ARG_ENABLE(vorbis-psy, [  --enable-vorbis-psy    Enable the Vorbis psy model],
[if test "$enableval" = yes; then
  vorbis_psy=yes;
  AC_DEFINE([VORBIS_PSYCHO], , [Enable support for the Vorbis psy model])
fi])

AC_ARG_WITH([fft], [AS_HELP_STRING([--with-fft=choice],[use an alternate FFT implementation. The available choices are
kiss (default fixed point), smallft (default floating point), gpl-fftw3 and proprietary-intel-mkl])],
[FFT=$withval]
)

FFT_PKGCONFIG=
AS_CASE([$FFT],
 [kiss], [
  AC_DEFINE([USE_KISS_FFT], [], [Use KISS Fast Fourier Transform])
 ],
 [smallft], [
  AC_DEFINE([USE_SMALLFT], [], [Use FFT from OggVorbis])
 ],
 [gpl-fftw3], [
  AC_DEFINE([USE_GPL_FFTW3], [], [Use FFTW3 for FFT])
  PKG_CHECK_MODULES([FFT], [fftw3f])
 ],
 [proprietary-intel-mkl], [
  AC_DEFINE([USE_INTEL_MKL], [], [Use Intel Math Kernel Library for FFT])
  AC_MSG_CHECKING(for valid MKL)
  AC_LINK_IFELSE([
   AC_LANG_PROGRAM([[
#include <mkl.h>
void func() {
  DFTI_DESCRIPTOR_HANDLE h;
  MKL_LONG result=DftiCreateDescriptor(&h, DFTI_SINGLE, DFTI_REAL, 0);
}]])],
   [AC_MSG_RESULT(yes)],
   [AC_MSG_FAILURE([Failed to compile MKL test program. Make sure you set CFLAGS to include the include directory and set LDFLAGS to include the library directory and all necessary libraries.])]
  )
 ],
 [AC_MSG_FAILURE([Unknown FFT $FFT specified for --with-fft])]
)
AM_CONDITIONAL(BUILD_KISS_FFT, [test "$FFT" = "kiss"])
AM_CONDITIONAL(BUILD_SMALLFT, [test "$FFT" = "smallft"])
AC_SUBST(FFT_PKGCONFIG)

AM_CONDITIONAL(BUILD_VORBIS_PSY, [test "x$vorbis_psy" = "xyes"])

PKG_CHECK_MODULES([SPEEXDSP], [speexdsp], [AC_DEFINE([USE_SPEEXDSP], [], [Use SpeexDSP library])], [speexdsp_failed=yes])


AC_CHECK_SIZEOF([int16_t])
AC_CHECK_SIZEOF([uint16_t])
AC_CHECK_SIZEOF([u_int16_t])
AC_CHECK_SIZEOF([int32_t])
AC_CHECK_SIZEOF([uint32_t])
AC_CHECK_SIZEOF([u_int32_t])
AC_CHECK_SIZEOF([short])
AC_CHECK_SIZEOF([int])
AC_CHECK_SIZEOF([long])

AS_IF([test "$has_char16" = "yes"],
  [
   SIZEOF16=1
   SIZEOF32=2
  ],[
   SIZEOF16=2
   SIZEOF32=4
  ])

case $SIZEOF16 in
    $ac_cv_sizeof_int16_t) SIZE16="int16_t";;
    $ac_cv_sizeof_short) SIZE16="short";;
    $ac_cv_sizeof_int) SIZE16="int";;
esac

case $SIZEOF16 in
    $ac_cv_sizeof_uint16_t) USIZE16="uint16_t";;
    $ac_cv_sizeof_u_int16_t) USIZE16="u_int16_t";;
    $ac_cv_sizeof_short) USIZE16="unsigned short";;
    $ac_cv_sizeof_int) USIZE16="unsigned int";;
esac

case $SIZEOF32 in
    $ac_cv_sizeof_int32_t) SIZE32="int32_t";;
    $ac_cv_sizeof_int) SIZE32="int";;
    $ac_cv_sizeof_long) SIZE32="long";;
    $ac_cv_sizeof_short) SIZE32="short";;
esac

case $SIZEOF32 in
    $ac_cv_sizeof_uint32_t) USIZE32="uint32_t";;
    $ac_cv_sizeof_u_int32_t) USIZE32="u_int32_t";;
    $ac_cv_sizeof_short) USIZE32="unsigned short";;
    $ac_cv_sizeof_int) USIZE32="unsigned int";;
    $ac_cv_sizeof_long) USIZE32="unsigned long";;
esac

XIPH_ADD_CFLAGS([-Wall])

AS_IF([test -z "$SIZE16"],[AC_MSG_ERROR([No 16 bit type found on this platform!])])
AS_IF([test -z "$SIZE32"],[AC_MSG_ERROR([No 32 bit type found on this platform!])])
AS_IF([test -z "$USIZE16"],[AC_MSG_ERROR([No unsigned 16 bit type found on this platform!])])
AS_IF([test -z "$USIZE32"],[AC_MSG_ERROR([No unsigned 32 bit type found on this platform!])])

AC_SUBST([SIZE16])
AC_SUBST([USIZE16])
AC_SUBST([SIZE32])
AC_SUBST([USIZE32])

AS_IF([test "$ac_cv_header_stdint_h" = "yes"],    [INCLUDE_STDINT="#include <stdint.h>"],
      [test "$ac_cv_header_inttypes_h" = "yes"],  [INCLUDE_STDINT="#include <inttypes.h>"],
      [test "$ac_cv_header_sys_types_h" = "yes"], [INCLUDE_STDINT="#include <sys/types.h>"])

AC_SUBST([INCLUDE_STDINT])


AC_CONFIG_FILES([
           Makefile libspeex/Makefile src/Makefile doc/Makefile Speex.spec
           include/Makefile include/speex/Makefile speex.pc
           win32/Makefile win32/libspeex/Makefile win32/speexenc/Makefile
           win32/speexdec/Makefile symbian/Makefile
           win32/VS2003/Makefile
           win32/VS2003/tests/Makefile
           win32/VS2003/libspeex/Makefile
           win32/VS2003/speexdec/Makefile
           win32/VS2003/speexenc/Makefile
           win32/VS2005/Makefile
           win32/VS2005/libspeex/Makefile
           win32/VS2005/speexdec/Makefile
           win32/VS2005/speexenc/Makefile
           win32/VS2005/tests/Makefile
           win32/VS2008/Makefile
           win32/VS2008/speexdec/Makefile
           win32/VS2008/tests/Makefile
           win32/VS2008/libspeex/Makefile
           win32/VS2008/speexenc/Makefile
           include/speex/speex_config_types.h ti/Makefile
	   ti/speex_C54_test/Makefile ti/speex_C55_test/Makefile
	   ti/speex_C64_test/Makefile
])

AC_CONFIG_HEADERS([config.h])

AC_OUTPUT

echo "Type \"make; make install\" to compile and install Speex";
