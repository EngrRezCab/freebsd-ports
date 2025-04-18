PORTNAME=	sympow
PORTVERSION=	2.023.7
PORTREVISION=	1
DISTVERSIONPREFIX=	v
CATEGORIES=	math

MAINTAINER=	thierry@FreeBSD.org
COMMENT=	Mathematical program for SYMmetric POWer elliptic curve L-functions
WWW=		https://gitlab.com/rezozer/forks/sympow

LICENSE=	BSD3CLAUSE
LICENSE_FILE=	${WRKSRC}/COPYING

BUILD_DEPENDS=	help2man:misc/help2man	\
		bash:shells/bash
LIB_DEPENDS=	libpari.so:math/pari

USES=		compiler:c++11-lang gmake shebangfix
SHEBANG_FILES=	${CONFIGURE_SCRIPT}

USE_GITLAB=	yes
GL_ACCOUNT=	rezozer/forks

HAS_CONFIGURE=	yes
CONFIGURE_SCRIPT=Configure
CONFIGURE_ENV=	CFLAGS="${CFLAGS}"

pre-configure:
	${REINPLACE_CMD} -e 's|-O3 ||' ${WRKSRC}/${CONFIGURE_SCRIPT}

post-build:
	${MKDIR} ${WRKDIR}/home ${WRKDIR}/var/cache/sympow/datafiles
	for file in `${LS} ${WRKSRC}/datafiles/*.txt`; do	\
		${SETENV} HOME=${WRKDIR}/home SYMPOW_PKGCACHEDIR="${WRKDIR}/var/cache/sympow"	\
		${WRKSRC}/sympow -txt2bin "`${GREP} -c AT $${file}`" <$${file} $${file%txt}bin; \
	done

post-install:
	${STRIP_CMD} ${STAGEDIR}${PREFIX}/bin/${PORTNAME}
	${INSTALL_DATA} ${WRKSRC}/datafiles/*.bin ${STAGEDIR}${DATADIR}/datafiles

.include <bsd.port.mk>
