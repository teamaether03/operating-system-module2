# Chapter 5 — File Systems

**Core problem:** Programs need to store data persistently and retrieve it efficiently after RAM is cleared or the machine reboots.

**NOTE :** all the commands as written in bash.

---

## 1. Files

**Problem:** How do we store and organize persistent data?
**Solution:** A file is the OS abstraction for persistent data stored on secondary storage.

### A file has:

* Name — human-readable identifier
* Content — actual data
* Type — regular file, executable, device, etc.
* Permissions — who can read/write/execute
* Metadata — information about the file

### Experiment

```bash
echo "Hello OS" > test.txt
cat test.txt
file test.txt
ls -l test.txt
```

---

## 2. Directories

**Problem:** Thousands of files cannot be managed efficiently as one flat collection.
**Solution:** A directory organizes files and other directories into a hierarchical namespace.

### Example:

```text
/
├── home/
│   └── user/
│       ├── notes.txt
│       └── project/
│           └── main.py
├── etc/
└── var/
```

**Important idea:** A directory primarily maps names → filesystem objects.

### Experiment :

```bash
mkdir os_test
cd os_test
touch file1.txt file2.txt
mkdir subdir
ls -la
pwd
```

---

## 3. Metadata

**Problem:** The OS needs information about a file without reading its entire content.
**Solution:** Metadata stores properties describing the file.

### Common metadata:

* Size
* Owner
* Group
* Permissions
* Timestamps
* Inode number
* File type
* Link count

### Experiment :

```bash
stat file1.txt
```

**Important Linux concept:**

inode = filesystem data structure containing a file's metadata and pointers/references to its data blocks.

The filename itself is associated with the directory entry, not stored as the inode's identity.

---

## 4. File Allocation

**Problem:** File contents must be placed somewhere on the physical storage device. How should the filesystem allocate blocks?

### A. Contiguous Allocation

```text
File A → [10][11][12][13][14]
```

**Advantage:** Fast sequential and random access.
**Problem:** External fragmentation and difficult file growth.

### B. Linked Allocation

```text
[10] → [25] → [7] → [31]
```

Each block points to the next.
**Advantage:** Files can grow easily.
**Problem:** Poor random access.

### C. Indexed Allocation

```text
Index Block
   ↓
[10][25][7][31]
```

An index structure stores references to the file's data blocks.
**Advantage:** Supports efficient random access.
**Problem:** Requires additional metadata/storage.

### Modern approach

Modern filesystems generally use more sophisticated block-allocation structures rather than simple textbook schemes.

For example, Linux filesystems commonly use extents: a single extent can describe a contiguous range of blocks.

---

## 5. Common File Systems

**Problem:** Different storage devices and operating systems need different ways to organize, allocate, protect, and recover data.

### Linux :

| Filesystem | Typical use                             |
| ---------- | --------------------------------------- |
| ext4       | General-purpose Linux filesystem        |
| XFS        | Large-scale/high-performance storage    |
| Btrfs      | Advanced features, snapshots, checksums |
| tmpfs      | RAM-backed temporary filesystem         |

### Other important filesystems :

| Filesystem | Typical use                      |
| ---------- | -------------------------------- |
| NTFS       | Windows                          |
| APFS       | macOS/iOS                        |
| exFAT      | Cross-platform removable storage |
| FAT32      | Older/removable devices          |

### Experiment :

See mounted filesystems: `df -T`
See block devices and filesystem types: `lsblk -f`
Check your root filesystem: `findmnt /`

---

## The Mental Model

```text
Application
     ↓
File
     ↓
Directory / Namespace
     ↓
Filesystem
     ↓
Blocks / Extents
     ↓
Storage Device
```

And the key problem-solving pattern:

```text
Need persistent data
        ↓
	Files

Need organization
        ↓
	Directories

Need information about files
        ↓
	Metadata / Inodes

Need to place data on storage
        ↓
	Allocation

Need a complete storage-management strategy
        ↓
	Filesystem
```

---
