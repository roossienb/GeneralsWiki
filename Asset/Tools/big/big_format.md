# BIG File Format

## Supplied by xezon

### BIG HEADER

```text
.BIG signature (4 bytes) - it must be 0x46474942 - 'BIGF'
.BIG file size (4 bytes)
FILE HEADER count (4 bytes)
BIG HEADER + FILE HEADER (count) + LAST HEADER size in bytes (4 bytes)
```

### FILE HEADER

```text
File data offset (4 bytes) - position in the .BIG file where the content of this specific file starts
File data size (4 bytes)
File name - null terminated string
```

### LAST HEADER

```text
Unknown value (4 bytes)
Unknown value (4 bytes)
```

## Supplied by Thyme Wiki

Source: <https://github.com/TheAssemblyArmada/Thyme/wiki/BIG-File-Format>

BIG files are an archive format used in many games published by Electronic Arts.
The supported features vary between games, with some using compression or encryption, but for SAGE, the files are
trivially concatenated together and wrapped with a header containing a series of index entries that located a given file
within the archive.

### Header

```cpp
struct BIGFileHeader
{
    uint32_t id;
    uint32_t archive_size;
    uint32_t file_count;
    uint32_t data_start;
};

struct IndexEntry
{
    int32_t file_size;
    int32_t position;
    char file_name[n];
};
```

The header "id" is a FourCC that is either "BIGF" for Generals/Zero Hour or "BIG4" for the Battle for Middle Earth
games. Thyme will accept either of these as valid. Next is the size of the archive in bytes stored as a little endian
32bit integer.

All other integers in the header are big endian and thus require byte swapping on popular CPU architectures such as x86
and common modes of ARM. "file_count" provides the number of files the archive contains and thus how many index entries
there are and "data_start" is the offset to the end of the header.

The index entries themselves consist of a "file_size" and "position" allowing the game to locate files within the
archive and stay within its bounds. "file_name" is a null terminated string used to identify the file and includes the
relative path to the file from the game directory if the file existed in the normal file system. This path length is
limited such that n must be less than or equal to 260 characters in keeping with the path limit on Windows which is all
the game buffers for.

With the information in the index it is possible to locate and read files within the archive which constitute the rest
of the file.
