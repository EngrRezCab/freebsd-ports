PORTNAME=	thinc
PORTVERSION=	8.3.4
CATEGORIES=	devel python
MASTER_SITES=	PYPI \
		https://github.com/explosion/thinc/releases/download/release-v${PORTVERSION}/
PKGNAMEPREFIX=	${PYTHON_PKGNAMEPREFIX}
PKGNAMESUFFIX=	8

MAINTAINER=	sunpoet@FreeBSD.org
COMMENT=	Refreshing functional take on deep learning
WWW=		https://thinc.ai/ \
		https://github.com/explosion/thinc

LICENSE=	MIT
LICENSE_FILE=	${WRKSRC}/LICENSE

BUILD_DEPENDS=	${PYTHON_PKGNAMEPREFIX}blis>=1.2.0<1.3.0:math/py-blis@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}cymem>=2.0.2<2.1.0:devel/py-cymem@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}murmurhash>=0.28.0<1.1.0:devel/py-murmurhash@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}numpy>=1.19.0,1<3.0.0,1:math/py-numpy@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}preshed3>=3.0.2<3.1.0:devel/py-preshed3@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}setuptools>=0:devel/py-setuptools@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}wheel>=0:devel/py-wheel@${PY_FLAVOR}
RUN_DEPENDS=	${PYTHON_PKGNAMEPREFIX}blis>=1.2.0<1.3.0:math/py-blis@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}catalogue>=2.0.4<2.1.0:devel/py-catalogue@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}confection>=0.0.1<1.0.0:devel/py-confection@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}cymem>=2.0.2<2.1.0:devel/py-cymem@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}murmurhash>=0.28.0<1.1.0:devel/py-murmurhash@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}numpy>=1.19.0,1<3.0.0,1:math/py-numpy@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}packaging>=20.0:devel/py-packaging@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}preshed3>=3.0.2<3.1.0:devel/py-preshed3@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}pydantic2>=1.7.4<3.0.0:devel/py-pydantic2@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}setuptools>=0:devel/py-setuptools@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}srsly>=2.4.0<3.0.0:devel/py-srsly@${PY_FLAVOR} \
		${PYTHON_PKGNAMEPREFIX}wasabi>=0.8.1<1.2.0:textproc/py-wasabi@${PY_FLAVOR}

USES=		python:3.9+
USE_PYTHON=	autoplist concurrent cython pep517

PORTSCOUT=	limit:^8\.

post-install:
	${FIND} ${STAGEDIR}${PYTHON_SITELIBDIR} -name '*.so' -exec ${STRIP_CMD} {} +

.include <bsd.port.mk>
