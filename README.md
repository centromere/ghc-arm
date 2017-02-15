# Instructions

## stage1

Download [Raspian](https://www.raspberrypi.org/downloads/raspbian/), unzip it,
and use the `file` command to determine the start sector of the root partition:

```
2017-01-11-raspbian-jessie-lite.img: DOS/MBR boot sector; partition 1 : ID=0xc, start-CHS (0x0,130,3), end-CHS (0x8,138,2), startsector 8192, 129024 sectors; partition 2 : ID=0x83, start-CHS (0x8,138,3), end-CHS (0xa9,10,33), startsector 137216, 2578432 sectors
```

Multiply the start sector by the sector size:
```
37216 * 512 = 70254592
```

Mount it:
```
sudo mount -o loop,offset=70254592 2017-01-11-raspbian-jessie-lite.img /mnt
```

Install `qemu-user-static`, copy the arm emulator to the rootfs, and comment
out the entry in `/etc/ld.so.preload`:
```
sudo apt install binfmt-support qemu-user-static
sudo cp `which qemu-arm-static` /mnt/usr/bin
sudo chmod 755 /mnt/usr/bin/qemu-arm-static
sudo vim /mnt/etc/ld.so.preload
```

Tar it up in a way suitable for docker:
```
sudo tar --numeric-owner --create --file rootfs.tar --directory /mnt --transform='s,^./,,' .
mv rootfs.tar /location/of/stage1
```

Build the image:
```
cd /location/of/stage1
docker build -t ghc-arm-stage1 .
```

Extract the installed contents:
```
docker run --rm ghc-arm-stage1 cat /tmp/ghc-arm.tar > /location/of/stage2/ghc-arm.tar
```

## stage2
Build the image:
```
cd /location/of/stage2
mv ../stage1/rootfs.tar .
docker build -t ghc-arm .
```
