PORTNAME=	gcc
PORTVERSION=	10.4.0
CATEGORIES=	lang
MASTER_SITES=	GCC
PKGNAMESUFFIX=	${SUFFIX}

FLAVORS=	native i686_w64_mingw32 amd64_w64_mingw32
FLAVOR?=	${FLAVORS:[1]}

.if ${FLAVOR} != native
PKGNAMEPREFIX=	${FLAVOR:C/_/-/g}-
.endif

MAINTAINER=	salvadore@FreeBSD.org
COMMENT=	GNU Compiler Collection 10
.if ${FLAVOR} != native
COMMENT=	GNU Compiler Collection 10 targeting ${FLAVOR}
.endif
WWW=		https://gcc.gnu.org

LICENSE=	GPLv3 GPLv3RLE
LICENSE_COMB=	multi

ONLY_FOR_ARCHS=	aarch64 amd64 arm armv6 armv7 i386 powerpc powerpc64 powerpc64le

LIB_DEPENDS=	libgmp.so:math/gmp \
		libmpfr.so:math/mpfr \
		libmpc.so:math/mpc
.if ${FLAVOR} == native
BUILD_DEPENDS+=	${LOCALBASE}/bin/as:devel/binutils
RUN_DEPENDS+=	${LOCALBASE}/bin/as:devel/binutils
.else
BUILD_RUN_DEPS=	${FLAVOR:C/_/-/g}-as:devel/binutils@${FLAVOR} \
		${LOCALBASE}/${TARGETARCH}/mingw/include/windows.h:devel/mingw-w64-headers@${FLAVOR:S/_w64_mingw32//} \
		${LOCALBASE}/${TARGETARCH}/mingw/lib/libkernel32.a:devel/mingw-w64-crt@${FLAVOR:S/_w64_mingw32//}
BUILD_DEPENDS=	${BUILD_RUN_DEPS} \
		objdump:devel/binutils
RUN_DEPENDS=	${BUILD_RUN_DEPS}
.endif
.if defined(MAINTAINER_MODE)
BUILD_DEPENDS+=	runtest:misc/dejagnu
.endif

USES=		compiler cpe gmake iconv libtool makeinfo perl5 tar:xz
USE_BINUTILS=	yes
USE_PERL5=	build

CONFLICTS=	gcc10-devel

CPE_VENDOR=	gnu

GCC_VERSION=	${PORTVERSION}
SUFFIX=		${PORTVERSION:C/([0-9]+).*/\1/}
.if ${FLAVOR} != native
TARGETARCHAMD64=${FLAVOR:S/amd64/x86_64/}
TARGETARCH=	${TARGETARCHAMD64:C/_/-/g}
.endif
CFLAGS:=	${CFLAGS:N-mretpoline}
CXXFLAGS:=	${CXXFLAGS:N-mretpoline}

OPTIONS_DEFINE=		BOOTSTRAP GRAPHITE
OPTIONS_DEFAULT=	BOOTSTRAP
BOOTSTRAP_DESC=		Build using a full bootstrap
GRAPHITE_DESC=		Support for Graphite loop optimizations

.if ${FLAVOR} == native && exists(/usr/lib32/libc.so)
OPTIONS_DEFINE_amd64+=		MULTILIB
OPTIONS_DEFAULT_amd64+=	MULTILIB
OPTIONS_DEFINE_powerpc64+=	MULTILIB
#OPTIONS_DEFAULT_powerpc64+=	MULTILIB # https://gcc.gnu.org/bugzilla/show_bug.cgi?id=105010
MULTILIB_DESC=		Build support for 32-bit and 64-bit targets
MULTILIB_CONFIGURE_ENABLE=	multilib
.else
CONFIGURE_ARGS+=	--disable-multilib
.endif

.include <bsd.port.pre.mk>

.if ${ARCH} == amd64
CONFIGURE_TARGET=	x86_64-portbld-${OPSYS:tl}${OSREL}

.elif ${ARCH} == powerpc64
CONFIGURE_ENV+=	UNAME_m="powerpc64"
. if defined(PPC_ABI) && ${PPC_ABI} == ELFv2
CONFIGURE_ARGS+=	--with-abi=elfv2
. else
USE_GCC=	9
. endif
.endif

.if ${FLAVOR} == native
CONFIGURE_ARGS+=	--with-as=${LOCALBASE}/bin/as \
			--with-ld=${LOCALBASE}/bin/ld
PLIST_SUB+=		IS_NATIVE=""
INSTALL_BASE=		${PREFIX}
.else
CONFIGURE_ARGS+=	--target=${TARGETARCH} \
			--with-as=${LOCALBASE}/bin/${TARGETARCH}-as \
			--with-ld=${LOCALBASE}/bin/${TARGETARCH}-ld \
			--with-sysroot="${INSTALL_BASE}" \
			--program-prefix=${PKGNAMEPREFIX}
PLIST_SUB+=		IS_NATIVE="@comment "
INSTALL_BASE=		${PREFIX}/${TARGETARCH}
GNU_CONFIGURE_PREFIX=	${INSTALL_BASE}
.endif

LANGUAGES:=	c,c++,objc,fortran
TARGLIB=	${INSTALL_BASE}/lib/gcc${SUFFIX}
TARGLIB32=	${INSTALL_BASE}/lib32	# The version information is added later
LIBEXEC=	${INSTALL_BASE}/libexec/gcc${SUFFIX}
GNU_CONFIGURE=	yes
CONFIGURE_OUTSOURCE=	yes
.if empty(PORT_OPTIONS:MBOOTSTRAP)
CONFIGURE_ARGS+=--disable-bootstrap
.elif ${FLAVOR} == native
CONFIGURE_ARGS+=--with-build-config=bootstrap-debug
ALL_TARGET=	bootstrap-lean
.else
CONFIGURE_ARGS+=--disable-bootstrap
.endif
INSTALL_TARGET=	install-strip
.if ${UID} != 0
BINMODE=	755
.endif
CONFIGURE_ARGS+=--disable-nls \
		--enable-gnu-indirect-function \
		--enable-plugin \
		--libdir=${TARGLIB} \
		--libexecdir=${LIBEXEC} \
		--program-suffix=${SUFFIX} \
		--with-gmp=${LOCALBASE} \
		--with-gxx-include-dir=${TARGLIB}/include/c++/ \
		${ICONV_CONFIGURE_ARG} \
		--with-pkgversion="FreeBSD Ports Collection" \
		--with-system-zlib \
		--without-zstd
MAKE_ARGS+=	MAKEINFOFLAGS="--no-split"
USE_LDCONFIG=	${TARGLIB}
PLIST_SUB+=	GCC_VERSION=${GCC_VERSION} \
		GNU_HOST=${CONFIGURE_TARGET} \
		PKGNAMEPREFIX=${PKGNAMEPREFIX} \
		PY_PATH=${FLAVOR:C/_/-/g}/ \
		SUFFIX=${SUFFIX}
INFO=		gcc${SUFFIX}/cpp \
		gcc${SUFFIX}/cppinternals \
		gcc${SUFFIX}/gcc \
		gcc${SUFFIX}/gccinstall \
		gcc${SUFFIX}/gccint \
		gcc${SUFFIX}/gfortran
.if ${FLAVOR} == native
INFO+=		gcc${SUFFIX}/libgomp
.endif
# Release tarballs (as opposed to snapshots) always carry this.
INFO+=		gcc${SUFFIX}/libquadmath
.if ${FLAVOR} == native
INFO+=		gcc${SUFFIX}/libitm
.endif
SUB_FILES=	pkg-message
SUB_LIST+=	TARGLIB=${TARGLIB}

.if ${PORT_OPTIONS:MGRAPHITE}
LIB_DEPENDS+=	libisl.so:devel/isl
CONFIGURE_ARGS+=--with-isl=${LOCALBASE}
.endif

CONFIGURE_ARGS+=--enable-languages=${LANGUAGES}

pre-everything::
	@${ECHO_MSG} "Making GCC ${PORTVERSION} for ${CONFIGURE_TARGET} [${LANGUAGES}]"

pre-configure:
	cd ${WRKSRC} ; contrib/gcc_update --touch
	@${RM} ${WRKSRC}/gcc/*/*.info*

.if defined(MAINTAINER_MODE)
full-regression-test: build
	cd ${TEST_WRKSRC}; ${MAKE_CMD} -sk check \
	; ${WRKSRC}/contrib/test_summary
.endif

post-stage:
	${RM} ${STAGEDIR}${INSTALL_BASE}/man/man7/*
	${RM} -r ${STAGEDIR}${TARGLIB}/gcc/*/${GCC_VERSION}/include-fixed/
	# Add target libraries and include files to packaging list.
	${RM} ${WRKDIR}/PLIST.lib

.if (${ARCH} == amd64 || ${ARCH} == powerpc64) && ${PORT_OPTIONS:MMULTILIB}
	${MKDIR} ${STAGEDIR}${TARGLIB32}
	${MV} ${STAGEDIR}${INSTALL_BASE}/lib/lib32 ${STAGEDIR}${TARGLIB32}/gcc${SUFFIX}
.endif

.for d in ${TARGLIB:S/^${PREFIX}\///} ${TARGLIB32:S/^${PREFIX}\///} ${LIBEXEC:S/^${PREFIX}\///}
	cd ${STAGEDIR}${PREFIX} ; if [ -d $d ]; then \
	    ${FIND} $d -type f -o -type l >>${WRKDIR}/PLIST.lib ;\
	fi
.endfor

.if ${FLAVOR} != native
.for F in c++ cpp g++ gcc gcc-ar gcc-nm gcc-ranlib gcov gcov-dump gcov-tool gfortran lto-dump
	${RLN} ${STAGEDIR}${INSTALL_BASE}/bin/${FLAVOR:C/_/-/g}-$F${SUFFIX} \
		${STAGEDIR}${PREFIX}/bin/${FLAVOR:C/_/-/g}-$F${SUFFIX}
.endfor
	${RLN} ${STAGEDIR}${INSTALL_BASE}/bin/${FLAVOR:C/_/-/g}-gcc-${PORTVERSION} \
		${STAGEDIR}${PREFIX}/bin/${FLAVOR:C/_/-/g}-gcc-${PORTVERSION}
	cd ${STAGEDIR}${PREFIX} ; if [ -d ${TARGEARCH}/bin ]; then \
	    ${FIND} ${TARGETARCH}/bin -type f -o -type l >>${WRKDIR}/PLIST.lib ;\
	fi
.endif

	cd ${WRKDIR} ; ${SED} -i -e "/PLIST.lib/ r PLIST.lib" ${TMPPLIST}

.include <bsd.port.post.mk>
