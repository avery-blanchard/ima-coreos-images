# Container IMA on CoreOS
Dockerfile and resources for building FCOS and RHCOS images with container IMA

## Usage
Build the images (see instructions below), push to a remote repository, and apply machine configs to the cluster with the new OS image. 

Note: make sure you have pull secrets.

### Red Hat CoreOS
Run command: \
`sudo docker build     --build-arg KERNEL_VERSION=${KERNEL_VERSION}     --build-arg DTK_IMAGE=${DTK_IMAGE}     --build-arg RHCOS_IMAGE=${RHCOS_IMAGE}     -t ima-rh-coreos .`

Where kernel version is the version used in the coreOS image, DTK image is the name of the developers tool kit image on quay.io, RHCOS image is the name and version of the coreOS image used in the cluster, and git token is a github authentication token (repository is private).

### Fedora CoreOS
Run command: \
`sudo docker build --build-arg KERNEL_VERSION=${KERNEL_VERSION} GIT_TOKEN=${GIT_TOKEN} -t ima_fcos38 .`

Where kernel version is the version used in the coreOS image, and git token is a github authentication token (repository is private).
