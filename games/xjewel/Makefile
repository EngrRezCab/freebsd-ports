PORTNAME=	xjewel
PORTVERSION=	1.6
PORTREVISION=	8
CATEGORIES=	games
MASTER_SITES=	XCONTRIB/../R5contrib
EXTRACT_SUFX=	.tar.z

MAINTAINER=	bofh@FreeBSD.org
COMMENT=	X11 dropping jewels game

LICENSE=	NONE

DEPRECATED=	Unmaintained as last release was in 1994
EXPIRATION_DATE=	2025-05-01

BUILD_DEPENDS=	bdftopcf:x11-fonts/bdftopcf \
		mkfontscale>=0:x11-fonts/mkfontscale

USES=		fonts imake xorg
USE_XORG=	x11 xext

DESKTOP_ENTRIES="XJewel" "" "" "${PORTNAME}" "" ""

.include <bsd.port.pre.mk>

.if ${OPSYS} == FreeBSD
CFLAGS+=	-Wno-error=int-conversion
.endif

post-install:
	@${MKDIR} ${STAGEDIR}${FONTSDIR}
	(cd ${WRKSRC}/bitmaps && ${INSTALL_DATA} seven_seg.pcf.gz \
		${STAGEDIR}${FONTSDIR})

.include <bsd.port.post.mk>
