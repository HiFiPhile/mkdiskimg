#  mkdiskimg

Create disk image file for embedded system or virtual machine.

## Usage

`mkdiskimg config.json`

## Example configuration file

- Create `full.img` with 64M size and mbr label.
- Create one 1M vfat partition; one 60M ext4 partition, and remaining space for a swap partition. Size can be specified by `start` and `end` sector, or with `size`.
- Extract rootfs achieve `root_fs/rootfs.tar.gz` to ext4 partition.
- Copy `fat_files/boot.bin` to vfat partition.
- Copy all files from `misc_files` to `/root/` of ext4 partition.
- Write `Hello, world!` in `testfile` of vfat partition.

```json
{
    "name" : "full",
    "size" : "64M",
    "partition_table" : "mbr",
    "parts" : [
        {
            "filesystem" : "vfat",
            "type" : "p",
            "mbr_id" : "0x0b",
            "start" : "2048",
            "end" : "4095",
            "uploads" : [
                {
                    "target" : "/boot.bin",
                    "source" : "fat_files/boot.bin"
                }
            ],
            "writes" : [
                {
                    "path" : "/testfile",
                    "content" : "Hello, world!"
                }
            ]
        },
        {
            "filesystem" : "ext4",
            "type" : "p",
            "mbr_id" : "0x83",
            "size" : "60M",
            "tarballs" : [
                {
                    "target" : "/root/",
                    "source" : "root_fs/rootfs.tar.gz"
                }
            ],
            "uploads" : [
                {
                    "target" : "/",
                    "source" : "misc_files/*"
                }
            ]
        },
        {
            "filesystem" : "swap",
            "type" : "p",
            "mbr_id" : "0x82",
            "end" : "-40"
        }
    ],
}
```