ARG DTK_IMAGE
ARG RHCOS_IMAGE
ARG GIT_TOKEN 

FROM ${DTK_IMAGE} as builder
ARG KERNEL_VERSION

WORKDIR /build/

RUN git clone -b coreos https:/${GIT_TOKEN}@github.com/avery-blanchard/unshare-kprobe

WORKDIR /build/unshare-kprobe

RUN make KVER=${KERNEL_VERSION}

FROM ${RHCOS_IMAGE}
ARG KERNEL_VERSION

COPY --from=builder /etc/driver-toolkit-release.json /etc/
COPY --from=builder /build/unshare-kprobe/container_ima.ko /usr/lib/modules/${KERNEL_VERSION}/

# This is needed to autoload the module at boot time.
RUN depmod -a "${KERNEL_VERSION}" && echo container_ima > /etc/modules-load.d/container_ima.conf

