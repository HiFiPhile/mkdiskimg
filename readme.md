# mkdiskimg

Create disk images with partitions and content from JSON configuration.

## Usage

```bash
mkdiskimg config.json
mkdiskimg -h  # Show detailed help
```

## Features

- No root privileges or manual loop device setup required
- JSON configuration with comment support (`//` and `/* */`)
- Multiple partition table formats: MBR, GPT, hybrid, raw
- Various filesystems: ext2/3/4, vfat, swap
- Content population: tarballs, file uploads, raw writes
- Path expansion: environment variables and glob patterns
- Multiple image formats: raw, qcow2, vmdk, etc.

## Configuration

### Root Properties

- `name` - Image filename (without extension)
- `size` - Image size: `64M`, `1G`, or bytes (hex/decimal)
- `image_format` - Format: `raw` (default), `qcow2`, `vmdk`, etc.
- `partition_table` - Type: `mbr` (default), `gpt`, `hybrid`, `raw`
- `imgver` - Optional version string appended to filename
- `parts` - Array of partition definitions
- `binaries` - Array of raw binary writes (disk level)

### Partition Properties

**Layout:**
- `type` - `p` (primary), `l` (logical), `e` (extended)
- `start` - Start sector (auto if 0)
- `end` - End sector (negative = offset from end)
- `size` - Partition size (e.g., `32M`, `1G`)

**Filesystem:**
- `filesystem` - Type: `ext4` (default), `ext3`, `vfat`, `swap`, `raw`
- `block_size` - Filesystem block size in bytes
- `label` - Partition/filesystem label [GPT only]

**Partition Table:**
- `mbr_id` - MBR type ID: `0x83` (Linux), `0x0b` (FAT32), `0x05` (extended) [MBR only]
- `gpt_type` - GPT type GUID (e.g., `C12A7328-F81F-11D2-BA4B-00A0C93EC93B` for EFI) [GPT/hybrid only]
- `active` - Bootable flag [MBR only]

**Content:**
- `raw_image` - Copy raw image file directly to partition
- `tarballs` - Extract tarballs: `[{"source": "rootfs.tar.gz", "target": "/"}]`
- `uploads` - Copy files: `[{"source": "file.txt", "target": "/etc/"}]`
- `writes` - Create files: `[{"target": "/etc/hostname", "content": "myhost"}]`
- `binaries` - Raw binary writes at partition level

### Binary Write Properties

- `source` - Source file path (supports `~` and `$VAR`)
- `source_offset` - Byte offset in source (hex or decimal, default: `0`)
- `target_offset` - Byte offset in target (hex or decimal, default: `0`)
- `size` - Bytes to write (hex or decimal, default: entire file)
- `part` - Partition number (for disk-level binaries only)

## Example

Check [mbr.md](examples/mbr.md), [gpt.md](examples/gpt.md), [hybrid.md](examples/hybrid.md) for more configurations.

```json
{
  // Disk configuration
  "name": "system",
  "size": "128M",
  "partition_table": "mbr",

  "parts": [
    {
      // Boot partition
      "type": "p",
      "filesystem": "vfat",
      "size": "32M",
      "mbr_id": "0x0b",
      "active": true,
      "label": "BOOT",
      "uploads": [
        {"source": "boot/*", "target": "/"}
      ]
    },
    {
      // Root filesystem
      "type": "p",
      "filesystem": "ext4",
      "size": "64M",
      "label": "rootfs",
      "tarballs": [
        {"source": "rootfs.tar.gz", "target": "/"}
      ],
      "writes": [
        {"target": "/etc/hostname", "content": "embedded-system"}
      ]
    }
  ],

  // Raw bootloader write
  "binaries": [
    {
      "source": "u-boot.bin",
      "target_offset": "0x8000"
    }
  ]
}
```

## Installation

### Dependencies

- Python 3
- libguestfs

```bash
# Fedora/RHEL/CentOS
sudo yum install python3-libguestfs

# Debian/Ubuntu
sudo apt-get install python3-guestfs

# Arch Linux
sudo pacman -S libguestfs
```

### Install

```bash
chmod +x mkdiskimg
sudo cp mkdiskimg /usr/local/bin/
```

## Notes

- Default partition start is 1M (2048 sectors) for alignment
- Tarball compression auto-detected: `.gz`, `.bz`, `.xz`, `.Z`, `.lzo`, `.zst`
- Upload sources support glob patterns (`*`, `?`)
- Paths support environment variables (`$VAR`) and tilde (`~`)
- Hybrid partition table: GPT with protective MBR for first 3 partitions

## License

GPLv3