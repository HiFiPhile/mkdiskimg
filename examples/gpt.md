# GPT Partition Table Example

## Common GPT Type GUIDs

| Type | GUID | Description |
|------|------|-------------|
| EFI System | `C12A7328-F81F-11D2-BA4B-00A0C93EC93B` | FAT partition for UEFI boot |
| Linux filesystem | `0FC63DAF-8483-4772-8E79-3D69D8477DE4` | Generic Linux data |
| Linux root (x86-64) | `4F68BCE3-E8CD-4DB1-96E7-FBCAF984B709` | Root filesystem |
| Linux home | `933AC7E1-2EB4-4F13-B844-0E14E2AEF915` | Home directory |
| Linux swap | `0657FD6D-A4AB-43C4-84E5-0933C84B4F4F` | Swap partition |

## Basic GPT Configuration

```json
{
  "name": "gpt_disk",
  "size": "256M",
  "partition_table": "gpt",

  "parts": [
    {
      // EFI System Partition
      "filesystem": "vfat",
      "size": "64M",
      "label": "EFI",
      "gpt_type": "C12A7328-F81F-11D2-BA4B-00A0C93EC93B",
      "uploads": [
        {"source": "efi/*", "target": "/"}
      ]
    },
    {
      // Linux root filesystem
      "filesystem": "ext4",
      "size": "128M",
      "label": "rootfs",
      "gpt_type": "4F68BCE3-E8CD-4DB1-96E7-FBCAF984B709",
      "tarballs": [
        {"source": "rootfs.tar.gz", "target": "/"}
      ]
    },
    {
      // Data partition - use remaining space, up to -34 sectors from end (GPT backup)
      "filesystem": "ext4",
      "end": "-34",
      "label": "data",
      "gpt_type": "0FC63DAF-8483-4772-8E79-3D69D8477DE4"
    }
  ]
}
```

## Notes

- GPT partitions don't use the `type` field (no p/l/e distinction)
- GPT partitions don't support the `active` bootable flag (use ESP instead)
- GPT partitions don't use `mbr_id` (use `gpt_type` instead)
- Partition labels can be up to 36 characters (UTF-16)
- Default start offset is 1M (2048 sectors) for alignment
