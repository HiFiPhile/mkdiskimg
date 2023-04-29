#  mkdiskimg

Create disk image file for embedded system or virtual machine.

## Usage

`mkdiskimg config.json`

## Example configuration file

- Create `full.img` with 64M size and mbr label.
- Create one 1M vfat partition; one 50M ext4 partition, and remaining space for a swap partition. Size can be specified by `start` and `end` sector, or with `size`.
- Extract rootfs archieve `root_fs/rootfs.tar.gz` to ext4 partition.
- Copy `fat_files/boot.bin` to vfat partition.
- Copy all files from `misc_files` to `/root/` of ext4 partition.
- Write `Hello, world!` in `testfile` of vfat partition.

```json
{
    "name" : "full",
    "size" : "64M",
    "label" : "mbr",
    "parts" : [
        {
            "filesystem" : "vfat",
            "type" : "p",
            "mbr_type" : "0x0b",
            "gpt_type" : "C12A7328-F81F-11D2-BA4B-00A0C93EC93B",
            "start" : "2048",
            "end" : "4095"
        },
        {
            "filesystem" : "ext4",
            "type" : "p",
            "mbr_type" : "0x83",
            "size" : "60M"
        },
        {
            "filesystem" : "swap",
            "type" : "p",
            "mbr_type" : "0x82",
            "end" : "-40"
        }
    ],
    "tarballs" : [
        {
            "part" : "2",
            "target" : "/",
            "source" : "root_fs/rootfs.tar.gz"
        }
    ],
    "uploads" : [
        {
            "part" : "1",
            "target" : "/boot.bin",
            "source" : "fat_files/boot.bin"
        },
		{
            "part" : "2",
            "target" : "/root/",
            "source" : "misc_files/*"
        }
    ],
    "writes" : [
        {
            "part" : "1",
            "path" : "/testfile",
            "content" : "Hello, world!"
        }
    ]
}
```