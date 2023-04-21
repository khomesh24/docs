# Using Cloud image to spin virtual machines

## Download the cloud image on a local system

```bash
curl -O "URL"
```

## Create a empty qcow2 image

```bash
qemu-img create -f qcow2 ubuntu-bionic.qcow2 40G
```

## Copy the image into empty qcow2 image

```bash
virt-resize --expand /dev/sda1 <Cloud image>  ubuntu-bionic.qcow2
```

**Note:** Use virt-filesystems command to find the root disk of cloud image.

```bash
virt-filesystems --csv --long --no-title -a <Cloud image>
```

## Set the root password and remove cloud-init package

```bash
virt-customize -a ubuntu-bionic.qcow2 --root-password password:redhat --uninstall cloud-init
```

## Use new image to spin up a VM

```bash
virt-install --ram 12000 --vcpus 4 --disk path=ubuntu-bionic.qcow2,device=disk,bus=virtio,format=qcow2 --import --noautoconsole --vnc --network network:default --name ubuntu
```