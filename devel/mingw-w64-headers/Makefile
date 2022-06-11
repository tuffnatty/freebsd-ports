# Created by: Damjan Jovanovic <damjan.jov@gmail.com>
# $FreeBSD$

PORTNAME=	mingw-w64-headers
DISTNAME=	mingw-w64-v10.0.0
PORTVERSION=	10.0.0
CATEGORIES=	devel
MASTER_SITES=	SF/mingw-w64/mingw-w64/mingw-w64-release

MAINTAINER=	damjan.jov@gmail.com
COMMENT=	mingw-w64 headers for Windows/${FLAVOR} cross-development

LICENSE=	GPLv2+ LGPL21+ ZPL21 PD
LICENSE_COMB=	multi

FLAVORS=	i686 amd64
# There are also arm and aarch64, with zero documentation and no way to test
FLAVOR?=	${FLAVORS:[1]}
PKGNAMEPREFIX=	${FLAVOR}-
.if ${FLAVOR} == amd64
TARGETARCH=	x86_64-w64-mingw32
.else
TARGETARCH=	${FLAVOR}-w64-mingw32
.endif
PLIST_SUB=	TARGETARCH=${TARGETARCH}

WRKSRC_SUBDIR=	mingw-w64-headers
USES=		gmake tar:bz2
GNU_CONFIGURE=	yes
GNU_CONFIGURE_PREFIX=${PREFIX}/${TARGETARCH}/mingw
CONFIGURE_ARGS=	--host=${TARGETARCH} --enable-sdk=all

.include <bsd.port.mk>
