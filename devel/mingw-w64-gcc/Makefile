# Created by: Gerald Pfeifer <gerald@FreeBSD.org>
# Adapted for MinGW-w64 by: Phil Krylov <phil@krylov.eu>

PORTNAME=	gcc
PORTVERSION=	${GCC_DEFAULT}
CATEGORIES=	devel
MASTER_SITES=	# not applicable
DISTFILES=	# not applicable
PKGNAMEPREFIX=	${FLAVOR}-mingw-w64-

MAINTAINER=	phil@krylov.eu
COMMENT=	Meta-port for the default MinGW-w64 cross-compiler version \
		of the GNU Compiler Collection

FLAVORS=	i686 amd64
FLAVOR?=	${FLAVORS:[1]}

TARGETARCH=	${FLAVOR:S/amd64/x86_64/}-w64-mingw32

# Once GCC 4.8 isn't an option any longer, we can simply use GCC_DEFAULT
# below. (The expression rewrites 4.8 to 48.)
RUN_DEPENDS=	${TARGETARCH}-gcc${GCC_DEFAULT:S/.//}:lang/gcc${GCC_DEFAULT:S/.//}@${FLAVOR}_w64_mingw32

USES=		cpe
CPE_VENDOR=	gnu
CPE_VERSION=	${GCC_DEFAULT}

NO_ARCH=	yes
NO_BUILD=	yes

PLIST_FILES=	bin/${TARGETARCH}-g++ bin/${TARGETARCH}-gcc bin/${TARGETARCH}-gfortran

do-install:
.for c in gfortran g++ gcc
	    ${LN} -s ${TARGETARCH}-${c}${GCC_DEFAULT} ${STAGEDIR}${PREFIX}/bin/${TARGETARCH}-${c}
.endfor

.include <bsd.port.mk>
