# Rocky Linux Bootc Image
**This is unofficial**  

A simple example on how to create Rocky Linux bootc images.

It all started from this [thread](https://forums.rockylinux.org/t/made-a-nother-bootc-image-for-rocky-9/17140) and I tried to adopt in a personal repo but failed. 
Now with EL10 and 9.6 ostree and bootc is finally part of the foundation and can now be built with a few lines as this repository demostrates.

This is for those that want to build their own piplines and want to understand all the steps to generate the base bootc image.

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

Here is a example using bootc-image.builder in 3 steps on how to build a qcow2 disk based on this image.
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
This is just a example on how to build the base image, to explore more options look at the [Rocky Linux Container Images](https://quay.io/repository/rockylinux/rockylinux?tab=tags) for other images to build from and edit or base new files under `10/` for other options.
