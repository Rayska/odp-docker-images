FROM ubuntu:24.04

ENV DPDK_VERSION=v22.11.6

RUN apt-get update

RUN apt-get install -y --no-install-recommends \
  software-properties-common \
  dirmngr \
  gnupg-agent

RUN sed -e 's/^deb http/deb [arch=amd64] http/g' /etc/apt/sources.list -i

RUN dpkg --add-architecture armhf

RUN rm -f /etc/apt/sources.list.d/ubuntu.sources

RUN cat <<EOF > /etc/apt/sources.list.d/ubuntu.sources
# amd64 repositories (use archive.ubuntu.com and security.ubuntu.com)
Types: deb
URIs: http://archive.ubuntu.com/ubuntu/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Architectures: amd64
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: http://security.ubuntu.com/ubuntu/
Suites: noble-security
Components: main restricted universe multiverse
Architectures: amd64
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
EOF

RUN cat <<EOF > /etc/apt/sources.list.d/ubuntu_armhf.sources
# armhf repositories (use ports.ubuntu.com)
Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports/
Suites: noble noble-updates noble-backports
Components: main restricted universe multiverse
Architectures: armhf
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg

Types: deb
URIs: http://ports.ubuntu.com/ubuntu-ports/
Suites: noble-security
Components: main restricted universe multiverse
Architectures: armhf
Signed-By: /usr/share/keyrings/ubuntu-archive-keyring.gpg
EOF

RUN apt-get update --fix-missing

RUN apt-get install -y \
  crossbuild-essential-armhf \
  libc6:armhf \
  libcli-dev:armhf \
  libconfig-dev:armhf \
  libcunit1-dev:armhf \
  libpcap-dev:armhf \
  libssl-dev:armhf \
  libstdc++-10-dev:armhf \
  pkg-config-arm-linux-gnueabihf \
  autoconf \
  automake \
  ccache \
  clang \
  gcc \
  gcc-10 \
  git \
  iproute2 \
  kmod \
  libtool \
  meson \
  net-tools \
  ninja-build \
  python3-pip \
  sudo

RUN pip3 install \
  pyelftools

RUN cd $HOME && \
    git clone http://dpdk.org/git/dpdk-stable --branch ${DPDK_VERSION} --depth 1 ./dpdk && \
    cd dpdk && \
    meson setup build --cross-file config/arm/arm32_armv8_linux_gcc && \
    cd build && \
    meson configure -Dlibdir=lib/arm-linux-gnueabihf && \
    meson configure -Dprefix=/usr && \
    meson configure -Ddisable_apps=* && \
    meson configure -Dtests=false && \
    meson configure -Denable_drivers=crypto/*,dma/*,net/pcap && \
    ninja install && \
    ldconfig && \
    cd $HOME && \
    rm -r ./dpdk
