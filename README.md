# Rocky Linux Bootc Base Image
A simple example on how to create Rocky Linux bootc images

## Usage
This shows a example on how to create and build Rocky Linux 10 image using podman:
```
sudo podman build 
  --platform="linux/amd64" \
  --security-opt=label=disable \
  --cap-add=all \
  --device /dev/fuse \
  --iidfile /tmp/image-id \
  -t rocky-bootc \
  -f 10/Containerfile 
```
This will build a bootc image based on the configuration and containerfile under `10/. It will later be available under localhost/rocky-bootc:latest.

## Next
To use this image look at [bootc-image-builder](https://github.com/osbuild/bootc-image-builder)

For example in 3 steps on how to build a qcow2 disk based on this image.
1. Create the file config.toml with the following:
```
[[customizations.user]]
name = "alice"
password = "bob"
key = "ssh-rsa AAA ... user@email.com"
groups = ["wheel"]
```
2. Create the folder ```mkdir output``` and run:
```
sudo podman run 
  --rm \
  -it \
  --privileged \
  --pull=newer \
  --security-opt label=type:unconfined_t \
  -v ./config.toml:/config.toml:ro \
  -v ./output:/output \
  -v /var/lib/containers/storage:/var/lib/containers/storage \
  quay.io/centos-bootc/bootc-image-builder:latest \
  --type qcow2 \
  --use-librepo=True \
  localhost/rocky-bootc
```
3. The qcow2 file will be available under `output`. Create a VM and then log into this machine using the credentials in `config.toml`.


If already using Bootc then push to a registry so it can be fetched. [Here](https://developers.redhat.com/articles/2025/03/12/how-build-deploy-and-manage-image-mode-rhel#push_the_container_image_to_the_registry) is a article oon how to explore immutable in Enterprise Linux further using a registry and automatic updates.

## Tips
This is just a example on how to build the base image, to explore more options look at the [Rocky Linux Container Images](https://quay.io/repository/rockylinux/rockylinux?tab=tags) for other options to build from based on this and edit the files under `10/` for other options.
