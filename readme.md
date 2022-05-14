
# secure-fs-for-proot

Provide file system security for proot container

## Description

The official proot program on android, although providing a containerized file system, makes
the entire filesystem readable and writable by any user of the guest os. There is no file system
permission management provided. This is because of the limitation of the android file system
in that only the non-root user is the owner of the container guest fs.

Having a secure container file system is important for having a stable guest os container.

In our implementation of a secure file system, we provide modifications to the proot system that provide protected rootfs. Only the root use has readable and writable access to the root guest fs, unless he explictly gives permissions for all users to have access.

In a unix file permssions, each file system has the following attribute:
user(rwx), group(rwx) and other(rwx)

rwx means read, write, execute.

For the secure proot container fs, our implementation only allows users access based on the permissions on the

other(rwx) attributes.

If the root chooses to give read access then he should set other(r) permission,
For write access he should set other(w) permission.

Setting user(rwx) and group(rwx) and other() means the file/directory will not be accessible by users on the system.

The following directories are exempt from the secure fs and are accessible by all users:
/tmp /home /dev /sys /proc
This is because it is not possible to provide fine grained control on permissions in these directories.

We modified proot version 5.1.0 which has termux patches. 

## Dependencies

```
sudo apt install build-essential

wget https://github.com/termux/proot/archive/refs/heads/master.zip
unzip master.zip

cp path.c proot-master/src/path/
cp GNUmakefile proot-master/src

cd proot-master/src
make V=1

```

## Usage

```

cd proot-master/src
./proot

```

## Test with alpine image
```

mkdir ~/tmp
cd ~/tmp
mkdir alpine
curl -O https://dl-cdn.alpinelinux.org/alpine/v3.15/releases/aarch64/alpine-minirootfs-3.15.4-aarch64.tar.gz

cd alpine
tar -zxvf ../alpine-minirootfs-3.15.4-aarch64.tar.gz

cd ~/tmp
proot -r alpine

#now you are inside alpine container
id
cat /etc/alpine-release

touch /testing


```
