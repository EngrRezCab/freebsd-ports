PORTNAME=	omniauth-saml
DISTVERSION=	1.10.6
CATEGORIES=	security rubygems
MASTER_SITES=	RG
PKGNAMESUFFIX=	1

MAINTAINER=	bofh@FreeBSD.org
COMMENT=	OmniAuth strategy for SAML
WWW=		https://github.com/omniauth/omniauth-saml

LICENSE=	MIT
LICENSE_FILE=	${WRKSRC}/LICENSE.md

RUN_DEPENDS=	rubygem-omniauth1>=1.3.2:security/rubygem-omniauth1 \
		rubygem-ruby-saml>=1.9<2:security/rubygem-ruby-saml

USES=		gem

NO_ARCH=	yes

.include <bsd.port.mk>
