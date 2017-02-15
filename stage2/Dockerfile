FROM scratch
ADD rootfs.tar /
ADD ghc-arm.tar /
ENV PATH /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
RUN apt update && \
    apt -y dist-upgrade && \
    apt -y --no-install-recommends install llvm-3.7 libgmp-dev libncursesw5 && \
    rm -rf /var/lib/apt/lists/*
USER pi
ENV PATH /home/pi/.cabal/bin:$PATH
WORKDIR /home/pi
CMD bash
