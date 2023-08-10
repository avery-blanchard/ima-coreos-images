# Container IMA on CoreOS
Dockerfile and resources for building FCOS and RHCOS images with container IMA

## Usage
Build the images (see instructions below), push to a remote repository, and apply machine configs to the cluster with the new OS image. 

Note: make sure you have pull secrets.

### Red Hat CoreOS
Run command: 

`sudo docker build     --build-arg KERNEL_VERSION=${KERNEL_VERSION}     --build-arg DTK_IMAGE=${DTK_IMAGE}     --build-arg RHCOS_IMAGE=${RHCOS_IMAGE}     -t <image_name> .`

Where kernel version is the version used in the coreOS image, DTK image is the name of the developers tool kit image on quay.io, RHCOS image is the name and version of the coreOS image used in the cluster, and git token is a github authentication token (repository is private).

To apply the changes, use apply a machine config to Open Shift: 
```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: ima-rhcos
spec:
  osImageURL:  hypervisorepo:5000/<image_name>:latest 
```
### Fedora CoreOS
Run command: 

`sudo docker build --build-arg KERNEL_VERSION=${KERNEL_VERSION} --build-arg GIT_TOKEN=${GIT_TOKEN} -t <image_name>.`

Where kernel version is the version used in the coreOS image, and git token is a github authentication token (repository is private).

To rebase the rpm-ostree: 

`sudo rpm-ostree rebase --experimental ostree-unverified-registry:100.64.0.255:5000/<image_name> --bypass-driver`

`systemctl reboot`


