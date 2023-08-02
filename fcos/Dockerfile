FROM fedora:38 as builder
ARG KERNEL_VERSION
ARG GIT_TOKEN 

RUN dnf install -y \
    git \
    make \
    dwarves \
    libbpf-devel \
    llvm \
    clang \
    bpftool \
    glibc-devel.i686 \
    libbpf

RUN dnf groupinstall -y \
    "Development Tools" \
    "Development Libraries"

# Get the kernel-headers
RUN KERNEL_XYZ=$(echo ${KERNEL_VERSION} | cut -d"-" -f1) && \
    KERNEL_DISTRO=$(echo ${KERNEL_VERSION} | cut -d"-" -f2 | cut -d"." -f-2) && \
    KERNEL_ARCH=$(echo ${KERNEL_VERSION} | cut -d"-" -f2 | cut -d"." -f3) && \
    dnf install -y \
    https://kojipkgs.fedoraproject.org//packages/kernel/${KERNEL_XYZ}/${KERNEL_DISTRO}/${KERNEL_ARCH}/kernel-${KERNEL_VERSION}.rpm \
    https://kojipkgs.fedoraproject.org//packages/kernel/${KERNEL_XYZ}/${KERNEL_DISTRO}/${KERNEL_ARCH}/kernel-core-${KERNEL_VERSION}.rpm \
    https://kojipkgs.fedoraproject.org//packages/kernel/${KERNEL_XYZ}/${KERNEL_DISTRO}/${KERNEL_ARCH}/kernel-modules-${KERNEL_VERSION}.rpm \
    https://kojipkgs.fedoraproject.org//packages/kernel/${KERNEL_XYZ}/${KERNEL_DISTRO}/${KERNEL_ARCH}/kernel-modules-core-${KERNEL_VERSION}.rpm \
    https://kojipkgs.fedoraproject.org//packages/kernel/${KERNEL_XYZ}/${KERNEL_DISTRO}/x86_64/kernel-devel-${KERNEL_VERSION}.rpm

WORKDIR /home


RUN git clone -b coreos https://${GIT_TOKEN}@github.com/avery-blanchard/container-integrity-measurement

WORKDIR /home/container-integrity-measurement

RUN make KVER=${KERNEL_VERSION}

FROM quay.io/fedora/fedora-coreos:stable
ARG KERNEL_VERSION

COPY --from=builder /home/container-integrity-measurement/container_ima.ko /usr/lib/modules/${KERNEL_VERSION}/

COPY --from=builder /home/container-integrity-measurement/probe /usr/lib/modules/${KERNEL_VERSION}/


# This is needed in order to autoload the module at boot time.
RUN depmod -a "${KERNEL_VERSION}" && echo container_ima > /etc/modules-load.d/container_ima.conf

RUN ostree container commit

