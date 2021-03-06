AC_INIT([wforce], [1.2.1])
AM_INIT_AUTOMAKE([foreign dist-bzip2 parallel-tests 1.11 subdir-objects])
AM_SILENT_RULES([yes])
AC_CONFIG_MACRO_DIR([m4])
AC_CONFIG_HEADERS([config.h])
AC_PROG_CC
AC_PROG_CXX
AC_LANG([C++])
# Check dependencies with pkgconfig
PKG_PROG_PKG_CONFIG
PKG_CHECK_MODULES([GEOIP], [geoip], [AC_DEFINE([HAVE_GEOIP], [1], [Define to 1 if you have geoip])], [true])
PKG_CHECK_MODULES([GETDNS], [getdns], [AC_DEFINE([HAVE_GETDNS], [1], [Define to 1 if you have getdns])])
PKG_CHECK_MODULES([libsodium], [libsodium], [AC_DEFINE([HAVE_LIBSODIUM], [1], [Define to 1 if you have libsodium])])
PKG_CHECK_MODULES([LIBSYSTEMD], [libsystemd], [AC_DEFINE([HAVE_LIBSYSTEMD], [1], [Define to 1 if you have libsystemd-dev])], [true])
PKG_CHECK_MODULES([LIBHIREDIS], [libhiredis], [AC_DEFINE([HAVE_LIBHIREDIS], [1], [Define to 1 if you have libhiredis-dev])], [true])
AS_IF([test "x$LIBHIREDIS_LIBS" == "x"], [
  PKG_CHECK_MODULES([LIBHIREDIS], [hiredis], [AC_DEFINE([HAVE_LIBHIREDIS], [1], [Define to 1 if you have hiredis])])
])
AS_IF([test "x$LIBHIREDIS_LIBS" == "x"], [
  AC_MSG_ERROR([libhiredis not found, libhiredis support is required])
])
PDNS_CHECK_YAMLCPP([AC_DEFINE([HAVE_YAMLCPP], [1], [Define to 1 if you have yaml-cpp])], [true])
AS_IF([test "x$YAMLCPP_LIBS" == "x"], [
  AC_MSG_ERROR([yaml-cpp not found, yaml-cpp support is required])
])
AM_CONDITIONAL([LIBSYSTEMD],[test "$HAVE_LIBSYSTEMD" = "1"])
AC_PROG_LIBTOOL
# Boost-specific checks
BOOST_REQUIRE([1.42])
BOOST_DATE_TIME
BOOST_REGEX
BOOST_FOREACH
BOOST_SYSTEM
BOOST_FILESYSTEM
# Check for dependent programs
AC_CHECK_PROG(PERL, perl, perl)
AC_CHECK_PROG(HAVE_WGET, wget, wget)
AS_IF([test "x$HAVE_WGET" == "x"], [
  AC_MSG_ERROR([wget not found, wget needed for downloading regexes.yaml and is required])
])
AC_CHECK_PROG(DOCKER, docker-compose, docker-compose)
AS_IF([test "x$HAVE_WGET" == "x"], [
  AC_MSG_ERROR([docker-compose not found, and is required])
])
AX_PROG_PERL_MODULES( Swagger2::Markdown, , AC_MSG_WARN(Need to install Perl Swagger2::Markdown))
# Look for protobuf
PDNS_WITH_PROTOBUF
AS_IF([test "x$PROTOBUF_LIBS" == "x" -o "x$PROTOC" == "x"], [
  AC_MSG_ERROR([Protobuf not found, protobuf support is required])
])
# Setup variables for locally built libraries
AC_SUBST([YAHTTP_CFLAGS], ['-I$(top_srcdir)/ext/yahttp'])
AC_SUBST([YAHTTP_LIBS], ['-L$(abs_top_builddir)/ext/yahttp/yahttp -lyahttp'])
AC_SUBST([JSON11_CFLAGS], ['-I$(top_srcdir)/ext/json11'])
AC_SUBST([JSON11_LIBS], ['-L$(abs_top_builddir)/ext/json11 -ljson11'])
AC_SUBST([EXT_CFLAGS], ['-I$(top_srcdir)/ext'])
AC_SUBST([EXT_LIBS], ['$(abs_top_builddir)/ext/ext/libext.la'])
AC_SUBST([WFORCE_CFLAGS], ['-I$(top_srcdir)'/common])
AC_SUBST([WFORCE_LIBS], ['$(abs_top_builddir)/common/libweakforce.la'])
# We need libcrypto for hash functions
PDNS_CHECK_LIBCRYPTO
# Check for LuaJIT first then Lua
PDNS_WITH_LUAJIT
AS_IF([test "x$with_luajit" = "xno"], [
  PDNS_WITH_LUA
])
AS_IF([test "x$LUAPC" = "x" -a "x$LUAJITPC" = "x"], [
  AC_MSG_ERROR([Neither Lua nor LuaJIT found, Lua support is required])
])
PDNS_CHECK_LUA_HPP
# Need pandoc to build documentation
PDNS_CHECK_PANDOC
# Check for C++ 2011 support
AX_CXX_COMPILE_STDCXX_11(ext,mandatory)
AM_CONDITIONAL([CXX2011],[test "$HAVE_CXX11" = "1"])
# Check for systemd
AX_AVAILABLE_SYSTEMD
AM_CONDITIONAL([HAVE_SYSTEMD], [ test x"$systemd" = "xy" ])
# We need a working libcurl package
LIBCURL_CHECK_CONFIG
AS_IF([test "x$LIBCURL" = "x"], [
  AC_MSG_ERROR([Libcurl not found, libcurl support is required])
])
# Do we want the docker subdirectories to be built?
AC_ARG_ENABLE([docker],
[  --enable-docker         Build and run docker images],
[case "${enableval}" in
  yes) docker=true ;;
  no)  docker=false ;;
  *) AC_MSG_ERROR([bad value ${enableval} for --enable-docker]) ;;
esac],[docker=false])
AM_CONDITIONAL([WITH_DOCKER], [test x$docker = xtrue])
AC_CONFIG_FILES([Makefile
	ext/Makefile
	ext/ext/Makefile
	ext/json11/Makefile
	ext/yahttp/Makefile
	ext/yahttp/yahttp/Makefile
	common/Makefile
	docs/Makefile
	docker/Makefile
	trackalert/Makefile])
	
AC_OUTPUT
