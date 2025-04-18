PORTNAME=	ccl
DISTVERSIONPREFIX=	v
DISTVERSION=	1.13
PORTREVISION=	1
CATEGORIES=	lang lisp
MASTER_SITES+=	https://github.com/${GH_ACCOUNT}/${GH_PROJECT}/releases/download/v${PORTVERSION}/:bootstrap
DISTFILES=	freebsdx86.tar.gz:bootstrap
DIST_SUBDIR=	${PORTNAME}/${DISTVERSIONPREFIX}${PORTVERSION}
EXTRACT_ONLY=	${DISTNAME}${EXTRACT_SUFX}

MAINTAINER=	olgeni@FreeBSD.org
COMMENT=	Clozure CL is a free Common Lisp implementation
WWW=		https://www.clozure.com/ccl

LICENSE=	APACHE20

ONLY_FOR_ARCHS=	amd64

RUN_DEPENDS=	cl-freebsd-asdf-init>=0:devel/cl-freebsd-asdf-init

USE_GITHUB=	yes
GH_ACCOUNT=	Clozure

CCL_DIRECTORY=	${PREFIX}/lib/ccl
# the following files will not be installed
EXCLUDE=	cocoa-ide lisp-kernel scripts fx86cl fx86cl.image

USES=		cl

CONFLICTS_INSTALL=	cclive # bin/ccl

BUILD_DEPENDS+=	as:devel/binutils

LISP_ARCH=	x8664
FX86CL=		fx86cl64

SUB_LIST+=	CCL_DIRECTORY="${CCL_DIRECTORY}" \
		FX86CL="${FX86CL}"
SUB_FILES=	ccl.sh

post-extract:
	@cd ${WRKSRC} && ${EXTRACT_CMD} ${EXTRACT_BEFORE_ARGS} ${_DISTDIR}/freebsdx86.tar.gz

post-patch:
	@${SED}	-i '' -e 's|%%LOCALBASE%%|${LOCALBASE}|' ${WRKSRC}/tools/asdf.lisp
	@${SED}	-i '' -e 's|%%TAG%%|v${PORTVERSION}|' ${WRKSRC}/lisp-kernel/freebsdx86*/Makefile
	@${FIND} ${WRKSRC} -name .svn -or -name .cvsignore -or -name "*.orig" \
		| ${XARGS} ${RM} -r

do-build:
	@${RM} ${WRKSRC}/fx86cl ${WRKSRC}/fx86cl64
	@cd ${WRKSRC}/lisp-kernel/freebsd${LISP_ARCH} && ${MAKE} && ${RM} *.o
	@cd ${WRKSRC} && ${ECHO_CMD} | ${SETENV} -u CCL_DEFAULT_DIRECTORY ./${FX86CL} --no-init --batch --quiet \
		--eval "(ccl:rebuild-ccl :full t)" \
		--eval "(quit)"

do-install:
	@${MKDIR} ${STAGEDIR}${PREFIX}/lib/ccl
	${CP} -r ${WRKSRC}/* ${STAGEDIR}${CCL_DIRECTORY}
	@for i in ${EXCLUDE}; do \
		${RM} -r ${STAGEDIR}${CCL_DIRECTORY}/$${i}; \
	done
	${INSTALL_SCRIPT} ${WRKDIR}/ccl.sh ${STAGEDIR}${PREFIX}/bin/ccl

.include <bsd.port.mk>
