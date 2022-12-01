FROM ubuntu:20.04 as build
ARG dpdk_version=20.11.5
ARG ovs_version=2.16.3

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get -y install build-essential python3-pip liblua5.3-dev \
    cmake wget unzip libnuma-dev pciutils libpcap-dev libelf-dev linux-headers-generic \
    && pip3 install meson ninja pyelftools

WORKDIR /opt

# 下载编译 DPDK
RUN wget -q https://fast.dpdk.org/rel/dpdk-${dpdk_version}.tar.xz \
    && tar xf dpdk-${dpdk_version}.tar.xz

RUN cd dpdk-stable-${dpdk_version} \
    && meson build \
    && ninja -C build \
    && ninja -C build install \
    && cp -r build/lib /usr/local/

# 下载编译 OVS
RUN wget https://codeload.github.com/openvswitch/ovs/zip/refs/tags/v${ovs_version} -O ovs-${ovs_version}.zip \
    && unzip ovs-${ovs_version}.zip

RUN cd ovs-${ovs_version} \
    && ./boot.sh \
    && ./configure --with-dpdk=static CFLAGS="-Ofast -msse4.2 -mpopcnt" \
    && make -j \
    && make install

#####################################################
FROM ubuntu:20.04

# 复制相关文件
COPY --from=build /usr/local /usr/local/
WORKDIR /root

RUN apt-get update \
    && apt-get -y --no-install-recommends install liblua5.3 libnuma-dev pciutils \
    libpcap-dev python3 iproute2 net-tools iputils-ping vim iperf iftop tcpdump \
    && ldconfig \
    && echo 'export PATH=$PATH:/usr/local/share/openvswitch/scripts' | tee -a /root/.bashrc \
    && echo 'export DB_SOCK=/usr/local/var/run/openvswitch/db.sock' | tee -a /root/.bashrc
