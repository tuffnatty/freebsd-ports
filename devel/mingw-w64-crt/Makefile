# Created by: Damjan Jovanovic <damjan.jov@gmail.com>
# $FreeBSD$

PORTNAME=	mingw-w64-crt
DISTNAME=	mingw-w64-v8.0.0
PORTVERSION=	8.0.0
PKGNAMEPREFIX=  ${FLAVOR}-
CATEGORIES=	devel
MASTER_SITES=	SF/mingw-w64/mingw-w64/mingw-w64-release

MAINTAINER=	damjan.jov@gmail.com
COMMENT=	mingw-w64 C runtime for Windows/${FLAVOR} cross-development

LICENSE=	GPLv2+ LGPL21+ ZPL21 PD
LICENSE_COMB=	multi

FLAVORS=        i686 amd64
FLAVOR?=        ${FLAVORS:[1]}

TARGETARCH=	${FLAVOR:S/amd64/x86_64/}-w64-mingw32
WRKSRC_SUBDIR=	mingw-w64-crt

BUILD_DEPENDS=	${PREFIX}/${TARGETARCH}-bootstrap/bin/${TARGETARCH}-gcc:devel/mingw-w64-gcc-bootstrap@${FLAVOR} \
		${LOCALBASE}/${FLAVOR:S/amd64/x86_64/}-w64-mingw32/mingw/include/windows.h:devel/mingw-w64-headers@${FLAVOR}
SSP_UNSAFE=	yes
USES=		gmake tar:bz2
PLIST_SUB+=	TARGETARCH=${TARGETARCH}

GNU_CONFIGURE=	yes
GNU_CONFIGURE_PREFIX=${PREFIX}/${TARGETARCH}/mingw
CONFIGURE_ARGS+=--host=${TARGETARCH} \
		--with-sysroot="${PREFIX}/${TARGETARCH}" \
		--enable-wildcard
.if ${FLAVOR} == i686
CONFIGURE_ARGS+=--enable-lib32 \
		--disable-lib64
PLIST_SUB+=	IS_I686="" \
		IS_AMD64="@comment "
.elif ${FLAVOR} == amd64
CONFIGURE_ARGS+=--disable-lib32 \
		--enable-lib64
PLIST_SUB+=	IS_I686="@comment " \
		IS_AMD64=""
.else
IGNORE=unimplemented flavor ${FLAVOR}
.endif

CONFIGURE_ENV=	PATH=${PREFIX}/${TARGETARCH}/bin:${PREFIX}/${TARGETARCH}-bootstrap/bin:${PATH}
CONFIGURE_ENV+=	ac_cv_prog_CC=${TARGETARCH}-gcc
MAKE_ENV=	PATH=${PREFIX}/${TARGETARCH}/bin:${PREFIX}/${TARGETARCH}-bootstrap/bin:${PATH}

.include <bsd.port.mk>
