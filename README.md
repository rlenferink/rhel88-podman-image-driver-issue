# rhel88-podman-image-driver-issue

This repo contains the files required to reproduce an issue with the [podman image driver](https://docs.podman.io/en/latest/markdown/podman-volume-create.1.html#driver-d-driver)
on a RHEL 8.8 installation.

To reproduce the issue, ensure that a RHEL 8.8 installation is available (Minimal Install with no additional software selected).
After installation, install `podman` (`dnf install podman`) and ensure this repo is available on the node.

Then execute:

```bash
# Step 1: Build the data image with e.g. a configuration file (SUCCESS)
podman build -t localhost/data-image:latest .

# Step 2: Create an image based volume (SUCCESS)
podman volume create --driver image --opt image=localhost/data-image:latest data-volume

# Step 3: Run a test container (normally an application container) with the volume mounted (FAIL)
podman run -it --rm -v data-volume:/data --name app registry.access.redhat.com/ubi8/ubi:8.8-854 echo "Hello"
```

The last step fails with the following error message:

```
ERRO[0000] Unmounting /var/lib/containers/storage/overlay/bc45521eb255049a814ea249d25ed9f90c94cf40339f9a36bab11db50be832bc/merged: invalid argument 
Error: mounting volume data-volume for container cd2947061f577562181f233e3170fdea11f012dd33608981fef8219c057ebae5: mounting volume data-volume image failed: creating overlay mount to /var/lib/containers/storage/overlay/bc45521eb255049a814ea249d25ed9f90c94cf40339f9a36bab11db50be832bc/merged, mount_data="lowerdir=/var/lib/containers/storage/overlay/l/RRZ4UGGDBTW7M2YEQDYV6XB6ZT,upperdir=/var/lib/containers/storage/overlay/bc45521eb255049a814ea249d25ed9f90c94cf40339f9a36bab11db50be832bc/diff,workdir=/var/lib/containers/storage/overlay/bc45521eb255049a814ea249d25ed9f90c94cf40339f9a36bab11db50be832bc/work,nodev,metacopy=on,context=\"system_u:object_r:container_file_t:s0:s0:c230,c395\"": invalid argument
```

## Environment

RedHat 8.8 with SELinux in enforcing mode (default after installation).

