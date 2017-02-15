FROM scratch
ADD rootfs.tar /
ENV PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
USER root
RUN apt update && \
    apt -y dist-upgrade && \
    apt -y --no-install-recommends install bison llvm-3.7 libgmp-dev libncurses5-dev autoconf && \
    rm -rf /usr/local/* && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /tmp
ADD http://ftp.gnu.org/gnu/binutils/binutils-2.27.tar.bz2 .
RUN tar -vxjf binutils-2.27.tar.bz2 && \
    mkdir binutils-2.27-build
WORKDIR /tmp/binutils-2.27-build
RUN ../binutils-2.27/configure --prefix=/usr/local --enable-plugins --enable-gold --disable-nls --disable-werror --with-sysroot=/ && \
    make -j && \
    make install

WORKDIR /tmp
ADD http://downloads.haskell.org/~ghc/8.0.2/ghc-8.0.2-armv7-deb8-linux.tar.xz .
RUN mkdir ghc-8.0.2-bin && \
    tar -vxf ghc-8.0.2-armv7-deb8-linux.tar.xz --strip-components=1 -C ghc-8.0.2-bin
WORKDIR /tmp/ghc-8.0.2-bin
RUN ./configure --with-llc=llc-3.7 --with-opt=opt-3.7 --prefix=/usr && \
    make install

WORKDIR /tmp
ADD http://downloads.haskell.org/~ghc/8.0.2/ghc-8.0.2-src.tar.xz .
RUN tar -vxf ghc-8.0.2-src.tar.xz
WORKDIR /tmp/ghc-8.0.2
ADD aclocal.m4.patch .
ADD build.mk mk
RUN patch -p0 < aclocal.m4.patch && \
    autoreconf -i && \
    ./configure --with-llc=llc-3.7 --with-opt=opt-3.7 --prefix=/usr/local && \
    until make -j; do :; done && \
    make install

USER pi
WORKDIR /tmp
ADD https://www.haskell.org/cabal/release/cabal-install-1.24.0.2/cabal-install-1.24.0.2.tar.gz .
RUN tar -vxzf cabal-install-1.24.0.2.tar.gz
WORKDIR /tmp/cabal-install-1.24.0.2
RUN EXTRA_CONFIGURE_OPTS="" ./bootstrap.sh --no-doc

USER root
WORKDIR /tmp
RUN tar --numeric-owner --create --file ghc-arm.tar --transform='s,^./,,' /usr/local /home/pi/.cabal /home/pi/.ghc
