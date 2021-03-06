dnl
dnl Configuring Kahua
dnl  process this file with "autoconf -I `gauche-config --ac`" to
dnl  generate 'configure'.
dnl
AC_PREREQ(2.59)
AC_INIT(kahua, m4_esyscmd([bash version.sh | tr -d '\n']), hayato.hashimoto@gmail.com)
AC_GAUCHE_INIT_EXT

AC_SUBST(PACKAGE_NAME)
AC_SUBST(PACKAGE_VERSION)

REQUIRED_GAUCHE_VERSION=0.8.13

dnl Sets the default value of INSTALL_TYPE macro.
AC_GAUCHE_INSTALL_TYPE(site)

dnl Get compiler parameters which Gauche has been compiled with.
AC_GAUCHE_CC
AC_GAUCHE_FLAGS

dnl Check for other programs.
AC_PROG_INSTALL
AC_CHECK_PROGS(MAKEINFO, makeinfo)
AC_CHECK_PROGS(GZIP_PROGRAM, gzip)

dnl Check Gauche version and threading support type.
dnl Kahua require threading support 'pthread' type,
dnl and if on Linux, Kahua require NPTL.
AC_MSG_CHECKING(check Gauche version and thread type)
gosh check.scm $REQUIRED_GAUCHE_VERSION
case "$?" in
  0);;
  1) AC_MSG_ERROR(
You must install Gauche $REQUIRED_GAUCHE_VERSION or later which was built with
                  --enable-threads=pthreads configure option.
, 1) ;;
  *) AC_MSG_ERROR("Unknown arguments", 70) ;;
esac
case "$target" in
  *-*-linux)
  if sh -c 'getconf GNU_LIBPTHREAD_VERSION|grep -vq NPTL'; then
      AC_MSG_ERROR(
NPTL(Native POSIX Thread Library) required.  We recommend Linux kernel 2.6.x.
, 1)
  fi
  ;;
esac
AC_MSG_RESULT(seem good)

dnl Sets some additional installation destination
KAHUA_CGIDIR=$libexecdir/kahua
AC_ARG_WITH(cgidir,
  AC_HELP_STRING([--with-cgidir=PATH],
                 [Specifies the directory where Kahua cgi scripts should be
installed.  If disabled (--with-cgidir=no or --without-cgidir), cgi scripts
are not installed automatically.  Default: $libexecdir/kahua]),
  [
    case "$with_cgidir" in
      yes|no) KAHUA_CGIDIR= ;;
      *)      KAHUA_CGIDIR=$with_cgidir ;;
    esac
  ])
AC_SUBST(KAHUA_CGIDIR)

KAHUA_REPODIR=/usr/local/share/kahua/repository
AC_ARG_WITH(repodir,
  AC_HELP_STRING([--with-repodir=PATH],
    [Specifies the directory where Kahua packages should be 
stored. Default: $prefix/share/kahua/repository]),
    [ KAHUA_REPODIR=$with_repodir ])
AC_SUBST(KAHUA_REPODIR)

KAHUA_SKELDIR=/usr/local/share/kahua/skel
AC_ARG_WITH(skeldir,
  AC_HELP_STRING([--with-skeldir=PATH],
    [Specifies the directory where Kahua package skelton be 
stored. Default: $prefix/share/kahua/skel]),
    [ KAHUA_SKELDIR=$with_skeldir ])
AC_SUBST(KAHUA_SKELDIR)

FASTCGI_EXT=fcg
AC_ARG_WITH(fastcgi-ext,
  AC_HELP_STRING([--with-fastcgi-ext=EXT],
		 [Specifies the extension of fastcgi script.  It's may be fcg or fcgi usually.  Default: fcg]),
  [
    case "$with_fastcgi_ext" in
      "") ;;
      *)  FASTCGI_EXT="$with_fastcgi_ext" ;;
    esac
  ])
AC_SUBST(FASTCGI_EXT)

AC_ARG_WITH(fastcgi,
  AC_HELP_STRING([--with-fastcgi=yes|no],
                 [If yes, kahua.cgi will be generated as a fastcgi script. Default: no]),
  [
    case "$with_fastcgi" in
      no) FASTCGI_SCRIPT=          ;;
      *)  FASTCGI_SCRIPT='kahua.$(FASTCGI_EXT)' ;;
    esac
  ])
AC_SUBST(FASTCGI_SCRIPT)

dnl Site bundle for examples. 
AC_ARG_WITH(site-bundle,
  AC_HELP_STRING([--with-site-bundle=/path/to/site],
                 [Examples installed into /path/to/bundle. Default: none]),[])

dnl Add more test

KAHUA_BUILD_REVISION=``
AC_SUBST(KAHUA_BUILD_REVISION)

dnl Set LDFLAGS to generate shared library.
AC_GAUCHE_FIX_LIBS

AC_CONFIG_SUBDIRS(examples)

dnl Output
echo $PACKAGE_VERSION > VERSION
AC_OUTPUT(Makefile 
          src/Makefile 
          doc/Modules/Makefile
          cgi/Makefile 
          repository/Makefile 
          emacs/Makefile
          plugins/Makefile
          test/Makefile)
