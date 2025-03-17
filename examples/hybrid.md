### Hybrid (MBR + GPT) disk image

- Create `disk.img` qcow2 format image with 1G size and hybrid label.
- Create one 16M EFI vfat partition; four 200M ext4 partitions.
- Extract rootfs achieve `root_fs/rootfs.tar.gz` to 1st ext4 partition.
- Copy all files from `boot` to vfat partition.
- Write `${HELLO}, world!` in `hello` of vfat partition, where ${HELLO} is from environment variable.

```json
{
    "name": "disk",
    "size": "1G",
    "image_format": "qcow2",
    "partition_table": "hybrid",
    "parts": [
        {
            "filesystem": "fat",
            "mbr_id": "0x0C",
            "size": "16M",
            "label": "boot",
            "gpt_type": "c12a7328-f81f-11d2-ba4b-00a0c93ec93b",
            "uploads": [
                {
                    "target": "/",
                    "source": "boot/*"
                }
            ],
            "writes": [
                {
                    "target": "/hello",
                    "content": "${HELLO} world"
                }
            ]
        },
        {
            "filesystem": "ext4",
            "size": "200M",
            "mbr_id": "0x83",
            "label": "root_a",
            "gpt_type": "69DAD710-2CE4-4E3C-B16C-21A1D49ABED3",
            "tarballs" : [
                {
                    "target" : "/",
                    "source" : "root_fs/rootfs.tar.gz"
                }
            ],
        },
        {
            "filesystem": "ext4",
            "size": "200M",
            "mbr_id": "0x83",
            "label": "root_b",
            "gpt_type": "69DAD710-2CE4-4E3C-B16C-21A1D49ABED3"
        },
        {
            "filesystem": "ext4",
            "size": "50M",
            "mbr_id": "0x83",
            "label": "cal",
            "gpt_type": "0fc63daf-8483-4772-8e79-3d69d8477de4"
        },
        {
            "filesystem": "ext4",
            "end": "-34",
            "mbr_id": "0x83",
            "label": "data",
            "gpt_type": "0fc63daf-8483-4772-8e79-3d69d8477de4"
        }
    ],
    "binarys": []
}
```