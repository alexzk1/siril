dnl Process this file with autoconf to produce a configure script.
m4_define([siril_major_version], [0])
m4_define([siril_minor_version], [9])
m4_define([siril_micro_version], [4])
m4_define([siril_prerelease_version], [beta.3])
m4_define([siril_version],
          [siril_major_version.siril_minor_version.siril_micro_version])
m4_define([siril_version_full],
          [siril_major_version().siril_minor_version().siril_micro_version()m4_bpatsubst(siril_prerelease_version(), [^], [-])])

dnl change siril_version_full by siril_version for stable release
dnl AC_INIT([siril], [siril_version_full],
AC_INIT([siril], [siril_version],
        [https://free-astro.org/bugs/view_all_bug_page.php],
        [siril],
        [https://free-astro.org/index.php/Siril/fr]) 
        
AM_INIT_AUTOMAKE([subdir-objects])
AC_CONFIG_HEADERS(config.h)
AC_CONFIG_SRCDIR([src/main.c])
AC_CONFIG_MACRO_DIR([m4])

dnl ================================================================
dnl Gettext stuff.
dnl ================================================================
IT_PROG_INTLTOOL([0.50])
AC_SUBST([GETTEXT_PACKAGE], [siril])

remember_set_CFLAGS="$CFLAGS"

AC_PROG_CC
AC_HEADER_STDC
AC_OPENMP

AC_LANG([C])
AC_LANG([C++])

dnl remove -g flag from default CFLAGS
if test "x$remember_set_CFLAGS" = "x"; then
	if test "$CFLAGS" = "-g -O2"; then
		CFLAGS="-O2"
	elif test "$CFLAGS" = "-g"; then
		CFLAGS=""
	fi
fi

CFLAGS="$CFLAGS $OPENMP_CFLAGS -DNDEBUG"
CXXFLAGS="$CFLAGS"

if test `uname` != "Darwin"; then
	LDFLAGS="$LDFLAGS -rdynamic"
fi


dnl Set PACKAGE_SOURCE_DIR in config.h.
packagesrcdir=`cd $srcdir && pwd`
AC_DEFINE_UNQUOTED(PACKAGE_SOURCE_DIR, "${packagesrcdir}", [Package source dir])

dnl Set PACKAGE_DOC_DIR in config.h.
dnl AC_DEFINE_UNQUOTED(PACKAGE_DOC_DIR, "${ac_default_prefix}/share/doc/${PACKAGE}", "package doc dir")

dnl ================ MANDATORY DEPENDECIES ================ 
dnl check math lib
AC_CHECK_LIB(m, log, LIBS="$LIBS -lm")

dnl check GTK
PKG_CHECK_MODULES(GTK, gtk+-3.0 >= 3.6.0)

dnl check fftw3
PKG_CHECK_MODULES(FFTW, [fftw3])

dnl check GNU Scientific Library
PKG_CHECK_MODULES(GSL, [gsl < 2],
		  [AC_DEFINE([HAVE_GSL_1], [1], [Use GSL 1])],
		  [PKG_CHECK_MODULES(GSL, [gsl >= 2],
				     [AC_DEFINE([HAVE_GSL_2], [1], [Use GSL 2])
				     ])
		  ])

dnl check cfitsio
PKG_CHECK_MODULES(CFITSIO, [cfitsio])

dnl check libconfig
PKG_CHECK_MODULES([LIBCONFIGXX], [libconfig++ >= 1.4],,
	AC_MSG_ERROR([libconfig++ 1.4 or newer not found.]))

dnl ================ OPTIONAL DEPENDECIES ================ 
dnl check raw lib
PKG_CHECK_MODULES(LIBRAW, [libraw],
 		  [AC_DEFINE([HAVE_LIBRAW], [1], [Using RAW images])],
 		  AC_MSG_WARN([libraw not found. Not using RAW importer.]))

dnl check tiff lib
PKG_CHECK_MODULES(LIBTIFF, [libtiff-4],
		  [AC_DEFINE([HAVE_LIBTIFF], [1], [Using TIFF images])],
		  AC_MSG_WARN([libtiff not found. Not using TIFF importer and exporter.]))

dnl check jpeg lib
dnl PKG_CHECK_MODULES(JPEG, [libjpeg])
AC_CHECK_LIB(jpeg, jpeg_mem_src, [],
	     AC_MSG_WARN([libjpeg not found. Not using JPEG importer and exporter.]))

dnl check png lib
PKG_CHECK_MODULES(LIBPNG, [libpng],
		  [AC_DEFINE([HAVE_LIBPNG], [1], [Using PNG images])],
		  AC_MSG_WARN([libpng not found. Not using PNG importer and exporter.]))
		  
dnl check gif lib
AC_CHECK_LIB(gif, EGifGetGifVersion, [])
AS_IF([test "$ac_cv_lib_gif_EGifGetGifVersion" != yes],
      AC_MSG_WARN([giflib not found or too old. Not using GIF exporter.]))

dnl check ffms2
PKG_CHECK_MODULES(FFMS2, [ffms2 < 2.21],
		  [AC_DEFINE([HAVE_FFMS2_1], [1], [Use FFMS2 < 2.21])],
		  [PKG_CHECK_MODULES(FFMS2, [ffms2 >= 2.21],
		  [AC_DEFINE([HAVE_FFMS2_2], [1], [Use FFMS2 > 2.21])],
		  AC_MSG_WARN([libffms2 not found. Not using AVI importer.]))
		  ])

dnl check opencv
AC_PROG_CXX
if test -n "$CXX" && ( test "X$CXX" != "Xno" &&
		      ( (test "X$CXX" = "Xg++" && `g++ -v >/dev/null 2>&1` ) ||
		      (test "X$CXX" != "Xg++"))) ; then
	AC_PROG_CXXCPP
	
	dnl check for C++11 support
	m4_include([m4/ax_cxx_compile_stdcxx.m4])
	AX_CXX_COMPILE_STDCXX(11, noext, mandatory)
	
	dnl AC_MSG_WARN(CXX is "$CXX")
	dnl http://nerdland.net/2009/07/detecting-c-libraries-with-autotools/ solution 2
	PKG_CHECK_MODULES([OPENCV], [opencv >= 2.0],
			  [AC_DEFINE([HAVE_OPENCV], [1], [Using OpenCV])]
			  [OPENCV_LIBS="-lopencv_core -lopencv_imgproc"],
			  AC_MSG_WARN([opencv not found. Not using some image processing.]))

else
	AC_MSG_ERROR([no C++ compiler found])
fi

AM_CONDITIONAL(HAVE_OPENCV, [test "x$OPENCV_LIBS" != "x"])

AC_CONFIG_FILES([Makefile src/Makefile po/Makefile.in])
AC_OUTPUT

