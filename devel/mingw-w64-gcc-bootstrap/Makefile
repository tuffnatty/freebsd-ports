# $FreeBSD$

PORTNAME=	gcc
PORTVERSION=	10.2.0
CATEGORIES=	devel
MASTER_SITES=	GCC/releases/gcc-${DISTVERSION}
PKGNAMEPREFIX=	${FLAVOR}-mingw-w64-
PKGNAMESUFFIX=	-bootstrap

MAINTAINER=	damjan.jov@gmail.com
COMMENT=	A crippled GNU Compiler Collection build, targeting Windows/${FLAVOR}, used only to bootstrap the full mingw-w64 GCC build

LICENSE=	GPLv3 GPLv3RLE
LICENSE_COMB=	multi

FLAVORS=	i686 amd64
FLAVOR?=	${FLAVORS:[1]}

TARGETARCH=	${FLAVOR:S/amd64/x86_64/}-w64-mingw32

LIB_DEPENDS=	libgmp.so:math/gmp \
		libmpfr.so:math/mpfr \
		libmpc.so:math/mpc
BUILD_DEPENDS=	${TARGETARCH}-as:devel/binutils@${FLAVOR}_w64_mingw32 \
		objdump:devel/binutils \
		${LOCALBASE}/${FLAVOR:S/amd64/x86_64/}-w64-mingw32/mingw/include/windows.h:devel/mingw-w64-headers@${FLAVOR}
RUN_DEPENDS=	${TARGETARCH}-as:devel/binutils@${FLAVOR}_w64_mingw32


USES=		compiler:c++11-lang gmake iconv libtool makeinfo perl5 tar:xz
USE_PERL5=	build
PLIST_SUB=	OPSYS=${OPSYS:tl} \
		SUFFIX=${SUFFIX} \
		TARGETARCH=${TARGETARCH} \
		GCC_VERSION=${PORTVERSION}

SUFFIX=		${PORTVERSION:C/([0-9]+).*/\1/}

GNU_CONFIGURE=	yes
CONFIGURE_OUTSOURCE=	yes
CONFIGURE_TARGET=${ARCH:S/amd64/x86_64/}-unknown-${OPSYS:tl}${OSREL}
GNU_CONFIGURE_PREFIX=${PREFIX}/${TARGETARCH}-bootstrap
CONFIGURE_ARGS+=--target=${TARGETARCH} --disable-nls --enable-languages=c,lto \
		--enable-gnu-indirect-function \
		--enable-initfini-array \
		--with-gmp=${LOCALBASE} \
		--with-pkgversion="FreeBSD Ports Collection for ${PKGNAMEPREFIX:C/-//g}" \
		--with-system-zlib \
		--with-sysroot="${PREFIX}/${TARGETARCH}" \
		--with-as=${LOCALBASE}/bin/${TARGETARCH}-as \
		--with-ld=${LOCALBASE}/bin/${TARGETARCH}-ld \
		--enable-static \
		--enable-lto \
		--disable-dw2-exceptions \
		--enable-version-specific-runtime-libs \
		--disable-multilib

ALL_TARGET=	all-gcc
INSTALL_TARGET=	install-gcc

.include <bsd.port.pre.mk>

post-patch:
	@${REINPLACE_CMD} -e '/LIBSTDCXX/ s/stdc\+\+/c\+\+/g ' \
		${WRKSRC}/gcc/cp/g++spec.c
	@${REINPLACE_CMD} -e '/LOCAL_INCLUDE_DIR/ d ' \
		${WRKSRC}/gcc/Makefile.in

post-stage:
	@${RM} ${STAGEDIR}${GNU_CONFIGURE_PREFIX}/bin/${TARGETARCH}-${TARGETARCH}-*
	@${RM} ${STAGEDIR}${GNU_CONFIGURE_PREFIX}/share/info/*
	# Man pages conflict with *-w64-mingw-gcc
	@${RM} -r ${STAGEDIR}${PREFIX}/man
	@${RM} -r ${STAGEDIR}${GNU_CONFIGURE_PREFIX}/lib/gcc/${TARGETARCH}/${PORTVERSION}/include-fixed

	cd ${STAGEDIR}${PREFIX} ; ${FIND} ${TARGETARCH}-bootstrap -type f -o -type l >>${WRKDIR}/PLIST.lib
	cd ${WRKDIR} ; ${SED} -i -e "/PLIST.lib/ r PLIST.lib" ${TMPPLIST}


.include <bsd.port.post.mk>
