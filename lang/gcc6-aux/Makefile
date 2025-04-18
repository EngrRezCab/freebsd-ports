PORTNAME=	gcc6-aux
PORTVERSION=	${SNAPSHOT}
PORTREVISION=	${MAIN_PR}
PORTEPOCH=	1
CATEGORIES=	lang
MASTER_SITES=	http://downloads.dragonlace.net/src/:boot \
		LOCAL/marino:boot LOCAL/ericbsd:boot GCC/${MS_SUBDIR}
DISTFILES=	${IDENTIFICATION}.tar.xz

MAINTAINER=	fluffy@FreeBSD.org
COMMENT=	Version of GCC ${GCC_BRANCH} with full Ada support
WWW=		http://www.dragonlace.net/

LICENSE=	GPLv3 GPLv3RLE
LICENSE_COMB=	multi

.include "${.CURDIR}/Makefile.common"

LANGS=			c c++ ada
APPLY_DIFFS=		core cxx ada
INTENDED_COMPILER=	NATIVE
GARCH=			${ARCH:S/amd64/x86_64/}
BOOTSTRAP_COMPILER=	gnat-bootstrap.${GARCH}.${OPSYS:tl}.tar.bz2
BLD_TARGET=		${GARCH}-aux-${OPSYS:tl}${OSREL}
FULL_GNATGCC=		NOT_SET
OS_LABEL4VERS=		[${OPSYS}${GARCH:S/amd//:S/x86_//:S/aarch/\/ARM/:S/i386/32/}]
NO_MTREE=		yes
CPPFLAGS+=		-I${LOCALBASE}/include
LDFLAGS+=		-L${LOCALBASE}/lib

PIE_UNSAFE=		yes

OPTIONS_GROUP=		STANDARD BOOTSTRAP
OPTIONS_GROUP_STANDARD=	FORTRAN OBJC NLS TESTSUITE ALLSTAGES STATIC
OPTIONS_GROUP_BOOTSTRAP=BOOTSTRAP
OPTIONS_DEFAULT=	FORTRAN OBJC
NO_OPTIONS_SORT=	yes

TESTSUITE_DESC=		Activate test support
ALLSTAGES_DESC=		Build compiler with itself (twice, brings in libcc1)
STATIC_DESC=		Link compilers and drivers statically
BOOTSTRAP_DESC=		Create bootstrap compiler (overrides other options)

# The BOOTSTRAP option overrides all the others.  "make stage" will build a
# static compiler that supports C, C++, and Ada languages and then create
# a new distfile in $WRKDIR/newbsd with the name following the pattern:
# ada-bootstrap.$ARCH.$OPSYS.XY.tar.bz2.  There is no need to "make install"

.include <bsd.port.options.mk>

.if ${OPSYS} == FreeBSD
ONLY_FOR_ARCHS=		aarch64 amd64 i386
MODERN_BINUTILS=	yes
BOOTSTRAP_COMPILER=	ada-bootstrap.${GARCH}.freebsd.120.tar.bz2
.endif
.if ${OPSYS} == DragonFly
ONLY_FOR_ARCHS=		x86_64
BOOTSTRAP_COMPILER=	ada-bootstrap.x86_64.dragonfly.41.tar.bz2
.endif

.if defined(MODERN_BINUTILS)
# Apparently gcc 4.9 needs binutils 2.23 or greater to link Ada correctly
USE_BINUTILS=	yes
RUN_DEPENDS+=	${LOCALBASE}/bin/as:devel/binutils
EXTRA_CONFIG+=	--with-ld=${LOCALBASE}/bin/ld
EXTRA_CONFIG+=	--with-as=${LOCALBASE}/bin/as
.else
EXTRA_CONFIG+=	--with-ld=/usr/bin/ld
EXTRA_CONFIG+=	--with-as=/usr/bin/as
.endif

WRKSRC=			${WRKDIR}/${IDENTIFICATION}
BUILDDIR=		${WRKDIR}/build
NEWBSDIR=		${WRKDIR}/newbs/bootstrap
CFG_SCRIPT=		${WRKSRC}/configure
REVFILE=		${WRKSRC}/gcc/REVISION
PHASEFILE=		${WRKSRC}/gcc/DEV-PHASE
BOOTSTRAP_PREFIX=	${WRKDIR}/bootstrap
PKG_PREFIX=		${PREFIX}/${PORTNAME}
MANDIRS=		${PREFIX}/${PORTNAME}/share
BSFILENAME=		ada-bootstrap.${GARCH}.${OPSYS:tl}.${OSREL:S/.//}.tar.bz2

# If we find gnatmake and friends in a standard location, then we'll use this
# compiler instead of downloading the bootstrap.
# Add ${LOCALBASE}/bin for gmake
.if exists (${LOCALBASE}/${PORTNAME}/bin/ada)
.if exists (${LOCALBASE}/${PORTNAME}/bin/gnatbind)
.if exists (${LOCALBASE}/${PORTNAME}/bin/gnatlink)
.if exists (${LOCALBASE}/${PORTNAME}/bin/gnatmake)
FULL_GNATGCC=${LOCALBASE}/${PORTNAME}/bin/ada
FULL_GNATGXX=${LOCALBASE}/${PORTNAME}/bin/g++
FULL_PATH=/sbin:/bin:/usr/sbin:/usr/bin:${PREFIX}/${PORTNAME}/bin:${PREFIX}/bin:${LOCALBASE}/bin
.endif
.endif
.endif
.endif

# If FULL_GNATGCC is not set, we'll request the bootstrap compiler
# Add ${LOCALBASE}/bin for gmake
.if ${FULL_GNATGCC} == NOT_SET
INTENDED_COMPILER=BOOTSTRAP
FULL_GNATGCC=${BOOTSTRAP_PREFIX}/bin/gcc
FULL_GNATGXX=${BOOTSTRAP_PREFIX}/bin/g++
FULL_PATH=${BOOTSTRAP_PREFIX}/bin:/sbin:/bin:/usr/sbin:/usr/bin:${PREFIX}/bin:${LOCALBASE}/bin
DISTFILES+=	${BOOTSTRAP_COMPILER}:boot
.endif

# for port maintenance, invoke "make makesum PLUS_BOOTSTRAPS=1"
.if defined(PLUS_BOOTSTRAPS)
DISTFILES=	\
   ${IDENTIFICATION}.tar.xz \
   ada-bootstrap.i386.freebsd.100B.tar.bz2:boot \
   ada-bootstrap.i386.freebsd.120.tar.bz2:boot \
   ada-bootstrap.x86_64.dragonfly.41.tar.bz2:boot \
   ada-bootstrap.x86_64.freebsd.100B.tar.bz2:boot \
   ada-bootstrap.x86_64.freebsd.120.tar.bz2:boot \
   ada-bootstrap.aarch64.freebsd.120.tar.bz2:boot
.endif

.if ${PORT_OPTIONS:MFORTRAN} && !${PORT_OPTIONS:MBOOTSTRAP}
LANGS+=		fortran
EXTRA_CONFIG+=	--enable-libquadmath
.else
EXTRA_CONFIG+=	--disable-libquadmath
.endif

.if ${PORT_OPTIONS:MOBJC} && !${PORT_OPTIONS:MBOOTSTRAP}
LANGS+=		objc
.endif

.if ${PORT_OPTIONS:MTESTSUITE} && !${PORT_OPTIONS:MBOOTSTRAP}
BUILD_DEPENDS+=	dejagnu>=1.4:misc/dejagnu
APPLY_DIFFS+=	ada-testsuite
APPLY_DIFFS+=	gcc-testsuite
.endif

.if ${PORT_OPTIONS:MNLS} && !${PORT_OPTIONS:MBOOTSTRAP}
USES+=		gettext
EXTRA_CONFIG+=	--enable-nls
.else
EXTRA_CONFIG+=	--disable-nls
.endif

.if ${PORT_OPTIONS:MSTATIC} && !${PORT_OPTIONS:MBOOTSTRAP}
EXTRA_CONFIG+=		--with-stage1-ldflags=-static
.endif

.if ${PORT_OPTIONS:MBOOTSTRAP}
EXTRA_CONFIG+=		--disable-shared --disable-lto
EXTRA_CONFIG+=		--with-boot-ldflags=-static
.else
.  if !${PORT_OPTIONS:MALLSTAGES}
#  For reasons unknown, build fails at libcc1 when the compiler is not
#  not bootstrapping.  It appears to be an upstream error.  LibCC1 is used
#  for gdb 7.9 exclusively, so it's not really needed.  Let's disable it
#  by default, but let people that want it suffer a 3-stage build.
EXTRA_CONFIG+=		--disable-bootstrap --disable-libcc1
.  endif
USES+=			iconv
.endif

# Establish Ada-aware compiler for use
ADA_CONFIGURE_ENV+=	CC=${FULL_GNATGCC}
ADA_CONFIGURE_ENV+=	CXX=${FULL_GNATGXX}
ADA_CONFIGURE_ENV+=	CFLAGS="${CFLAGS}"
ADA_CONFIGURE_ENV+=	CPPFLAGS="${CPPFLAGS}"
ADA_CONFIGURE_ENV+=	CXXFLAGS="${CXXFLAGS}"
ADA_CONFIGURE_ENV+=	LDFLAGS="${LDFLAGS}"
ADA_CONFIGURE_ENV+=	build_configargs="CPPFLAGS=\"${CPPFLAGS}\""
ADA_CONFIGURE_ENV+=	host_configargs="CPPFLAGS=\"${CPPFLAGS}\""
ADA_CONFIGURE_ENV+=	PATH=${FULL_PATH}
ADA_MAKE_ENV+=		PATH=${FULL_PATH}

# The standard configuration options
ADA_CONFIG_ARGS=	--enable-languages=${LANGS:Q}
ADA_CONFIG_ARGS+=	--build=${BLD_TARGET}
ADA_CONFIG_ARGS+=	--prefix=${PKG_PREFIX:Q}
ADA_CONFIG_ARGS+=	--with-system-zlib
ADA_CONFIG_ARGS+=	--with-gmp=${PREFIX}
ADA_CONFIG_ARGS+=	--with-mpfr=${PREFIX}
ADA_CONFIG_ARGS+=	--with-mpc=${PREFIX}
ADA_CONFIG_ARGS+=	${ICONV_CONFIGURE_ARG}
ADA_CONFIG_ARGS+=	--enable-shared
ADA_CONFIG_ARGS+=	--enable-threads=posix
ADA_CONFIG_ARGS+=	--enable-checking=release
ADA_CONFIG_ARGS+=	--disable-libmudflap
ADA_CONFIG_ARGS+=	--disable-libgomp
ADA_CONFIG_ARGS+=	--disable-libssp
ADA_CONFIG_ARGS+=	${EXTRA_CONFIG}

post-extract:
	# Personalize GNAT for each different machine
	@${ECHO} "-=> GNAT AUX ${OS_LABEL4VERS}" > ${REVFILE}
	@${ECHO} ${PHASE} > ${PHASEFILE}

	# Apply required composite diff files
.for suffix in ${APPLY_DIFFS}
	@${ECHO} "Applying composite patch diff-${suffix}"
	@${PATCH} -d ${WRKSRC} -s -E < ${FILESDIR}/diff-${suffix}
.endfor
.if ${PORT_OPTIONS:MSTATIC} || ${PORT_OPTIONS:MBOOTSTRAP}
	# Ensure GNAT tools are built statically
	${REINPLACE_CMD} -e 's/^GCC_LINK_FLAGS=.*/GCC_LINK_FLAGS=-static/' \
		${WRKSRC}/gcc/ada/gcc-interface/Makefile.in
.endif
	# Update LINK_SPEC to add gcc-aux lib runpath in every binary
	@${ECHO} "Configuring LINK_SPEC runpath"
	@${REINPLACE_CMD} -e 's;\@PREFIX\@;${PREFIX};' \
		-e 's;\@GCCAUX\@;${PORTNAME};' \
		${WRKSRC}/gcc/config/dragonfly.h \
		${WRKSRC}/gcc/config/i386/freebsd64.h \
		${WRKSRC}/gcc/config/i386/freebsd.h \
		${WRKSRC}/gcc/config/aarch64/aarch64-freebsd.h
	@${REINPLACE_CMD} -e 's|static:crtbeginT.o%s;||' \
		${WRKSRC}/gcc/config/freebsd-spec.h
.if defined(MODERN_BINUTILS)
	# Use dwarf4-compatible addr2line (affects FreeBSD only)
	${REINPLACE_CMD} -e 's|/usr/bin/addr2line|${PREFIX}/bin/addr2line|g' \
	  ${WRKSRC}/gcc/ada/traceback_symbolic.c
.endif

do-configure:
	${MKDIR} ${BUILDDIR}
	cd ${BUILDDIR} && ${SETENV} ${ADA_CONFIGURE_ENV} \
		${CFG_SCRIPT} ${ADA_CONFIG_ARGS}

do-build:
	cd ${BUILDDIR} && ${SETENV} ${ADA_MAKE_ENV} \
		${MAKE_CMD} -j${MAKE_JOBS_NUMBER} ${MAKE_ARGS} all

.if ${PORT_OPTIONS:MTESTSUITE} && !${PORT_OPTIONS:MBOOTSTRAP}
test: build test-ada test-fortran test-objc test-cxx test-c

test-ada:
	cd ${BUILDDIR} && ${SETENV} ${ADA_MAKE_ENV} \
		${MAKE_CMD} -sk check-ada

test-cxx:
	# libstdc++ testsuite will break every time, for TRUE to continue
	cd ${BUILDDIR} && ${SETENV} ${ADA_MAKE_ENV} \
		${MAKE_CMD} -j${MAKE_JOBS_NUMBER} -sk check-c++ || true

test-c:
	cd ${BUILDDIR} && ${SETENV} ${ADA_MAKE_ENV} \
		${MAKE_CMD} -j${MAKE_JOBS_NUMBER} -sk check-c

test-fortran:
.  if ${PORT_OPTIONS:MFORTRAN}
	cd ${BUILDDIR} && ${SETENV} ${ADA_MAKE_ENV} \
		${MAKE_CMD} -j${MAKE_JOBS_NUMBER} -sk check-fortran
.  endif

test-objc:
.  if ${PORT_OPTIONS:MOBJC}
	cd ${BUILDDIR} && ${SETENV} ${ADA_MAKE_ENV} \
		${MAKE_CMD} -j${MAKE_JOBS_NUMBER} -sk check-objc
.  endif
.endif

do-install:
	cd ${BUILDDIR} && ${SETENV} ${ADA_MAKE_ENV} \
		${MAKE_CMD} install-strip ${MAKE_ARGS}
	${MV} ${STAGEDIR}${PKG_PREFIX}/share ${WRKDIR}/moved_share
	${MKDIR} ${STAGEDIR}${PREFIX}/share/dejagnu/baseboards \
		${STAGEDIR}${PREFIX}/share/dejagnu/config
	${INSTALL_DATA} ${FILESDIR}/android6.exp \
		${STAGEDIR}${PREFIX}/share/dejagnu/config
	${INSTALL_DATA} ${FILESDIR}/gnatdroid6.exp \
		${STAGEDIR}${PREFIX}/share/dejagnu/baseboards

post-stage:
	${RM} -r ${STAGEDIR}${PKG_PREFIX}/lib/gcc/${BLD_TARGET}/${GCC_VERSION}/include-fixed/
	(cd ${STAGEDIR}${PKG_PREFIX}; ${FIND} * -type d -empty | \
		${SORT} -dr | ${XARGS} ${RMDIR})
	(cd ${STAGEDIR}${PREFIX}; ${FIND} ${PORTNAME} share \
		\( -type f -or -type l \) | ${SORT} | ${SED} \
		-e 's/\/man\/man[1578]\/.*[1578]$$/&.gz/' >> ${TMPPLIST})
.if ${PORT_OPTIONS:MBOOTSTRAP}
	${MKDIR} ${NEWBSDIR}
	${CP} -a ${STAGEDIR}${PKG_PREFIX}/bin ${NEWBSDIR}/
	${CP} -a ${STAGEDIR}${PKG_PREFIX}/lib ${NEWBSDIR}/
	${CP} -a ${STAGEDIR}${PKG_PREFIX}/include ${NEWBSDIR}/
	${CP} -a ${STAGEDIR}${PKG_PREFIX}/libexec ${NEWBSDIR}/
	${RM} ${NEWBSDIR}/bin/${GARCH}*
	(cd ${NEWBSDIR}/.. ; ${TAR} -cyf ${BSFILENAME} bootstrap)
.endif

.include <bsd.port.mk>
