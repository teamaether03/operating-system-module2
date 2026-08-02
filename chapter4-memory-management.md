# Chapter 4 — Memory Management

> **NOTE:** All the commands are written in bash.

---

## Why Memory Management Exists

**Problem:** Programs need memory to run, but:
- RAM is limited.
- Multiple processes run simultaneously.
- Processes must not access each other's memory.
- Large applications may need more memory than physical RAM.

**Solution:** The Operating System manages memory efficiently using:
- Memory hierarchy
- Address spaces
- Virtual memory
- Paging
- Swapping

---

## 1. Memory Hierarchy (Review)

**Problem:** Fast memory is expensive, while cheap memory is slow.

**Solution:** Use multiple layers of memory.

```
CPU Registers
      ↓
L1 Cache
      ↓
L2 Cache
      ↓
L3 Cache
      ↓
RAM
      ↓
SSD/HDD
```

### Speed vs Capacity

| Memory    | Speed    | Size  | Cost    |
|-----------|----------|-------|---------|
| Registers | Fastest  | Tiny  | Highest |
| Cache     | Very Fast| Small | High    |
| RAM       | Fast     | GBs   | Medium  |
| SSD       | Slow     | TBs   | Cheap   |
| HDD       | Slowest  | TBs   | Cheapest|

### How it works

CPU always searches in this order:

```
Registers
   ↓
Cache
   ↓
RAM
   ↓
Disk
```

The lower it goes, the slower execution becomes.

### Experiment

- Shows RAM usage : `free -h`
- Shows CPU cache information : `lscpu`
- Detailed memory statistics : `cat /proc/meminfo`

---

## 2. Physical Memory vs Virtual Memory

**Problem:** RAM is limited, but applications may need more memory and must be isolated from each other.

**Solution:** The OS provides Virtual Memory, which gives every process its own memory view and maps it to Physical Memory (RAM).

**Physical Memory:**
- Actual RAM installed in the computer.
- Stores data and programs while they are running.
- Limited in size.

**Virtual Memory:**
- Logical memory provided by the OS.
- Each process gets its own virtual address space.
- The OS maps virtual addresses to physical RAM.
- If RAM is full, inactive pages may be temporarily stored in swap.

### Comparison

| Physical Memory          | Virtual Memory             |
| ------------------------ | --------------------------- |
| Actual RAM               | Logical memory             |
| Limited by installed RAM | Can appear larger than RAM |
| Hardware                 | Managed by the OS          |

### Example

```
RAM = 8 GB

Application needs 10 GB
↓
OS keeps active data in RAM
↓
Moves inactive pages to Swap if needed
↓
Application continues running
```

### Commands

```
free -h
swapon --show
```

---

## 3. Address Spaces

**Problem:** If every process used physical memory directly,
- One process could overwrite another
- Programs would crash
- Security would disappear

**Solution:**
Give every process its own Virtual Address Space.
Each process thinks: My memory starts at 0x00000000
Even though physically it is somewhere else.

The OS translates:

```
Virtual Address
        ↓
Memory Management Unit (MMU)
        ↓
Physical Address
```

**Benefits:**
- Isolation
- Security
- Easier programming
- Programs don't care where RAM actually is

### Example

```
Chrome
Virtual: 0x1000

Firefox
Virtual: 0x1000

Both have the same virtual address,

but

Chrome
→ Physical RAM 5GB

Firefox
→ Physical RAM 2GB

No conflict
```

### Experiment

- Show process memory map: `cat /proc/$$/maps` or `pmap $$`

---

## 4. Virtual Memory

**Problem:** What if an application needs 16 GB,
but the system has only 8 GB RAM?

**Solution:**
Use disk as an extension of RAM.
This is called Virtual Memory.
OS moves inactive memory pages to disk.
Only active pages remain in RAM.

### Program

```
Needs 16 GB
↓
RAM
8 GB
↓
Disk stores remaining pages
```

Program still believes it has 16 GB.

**Advantages:**
- Run larger applications
- More multitasking
- Better RAM utilization

**Disadvantages:** Disk is much slower than RAM.
Too much virtual memory usage causes:

**Thrashing**

### Experiment

```
free -h
```
Look at
```
Swap:
```
Watch memory and swap activity live : `vmstat 1`

---

## 5. Thrashing

**Problem:** When RAM is almost full, the OS keeps moving pages between RAM and disk (swap).
The CPU spends more time waiting for memory than executing programs.

**Solution:** There is no "feature" called thrashing— thrashing is a performance problem. It is reduced by:
- Adding more RAM
- Running fewer applications
- Better memory management

```
RAM Full
↓
Page Out
↓
Page In
↓
Page Out
↓
Page In
↓
CPU waits most of the time
↓
Performance drops
```

**Effects:**
- System becomes very slow
- High disk usage
- Programs respond slowly

> **One-line definition:**
> Thrashing is a condition where the OS spends most of its time swapping pages between RAM and disk instead of executing programs.

---

## 6. Memory Management Unit (MMU)

**Problem:** Programs use virtual addresses, but RAM stores data using physical addresses.
How does the CPU know where the actual data is?

**Solution:** The Memory Management Unit (MMU) is a hardware component inside the CPU that translates virtual addresses into physical addresses before accessing RAM.

```
Program
    ↓
Virtual Address
    ↓
MMU
    ↓
Physical Address
    ↓
RAM
```

**Why It Is Important:**
- Enables virtual memory
- Provides process isolation
- Allows programs to use virtual addresses instead of physical ones

> **One-line definition:**
> MMU is a hardware unit inside the CPU that converts virtual addresses into physical addresses.

---

## 7. Paging

**Problem:** Memory allocation becomes difficult if programs require one large continuous block of RAM (external fragmentation).

**Solution:** Split memory into fixed-size blocks.

```
Virtual Memory
Page 1
Page 2
Page 3
Page 4

RAM
Frame 10
Frame 3
Frame 7
Frame 1
```

**NOTE:** Pages can be stored anywhere. No need for continuous memory.

**Key Terms:**
- **Page:** Fixed-size block in virtual memory.
  Example : 4 KB
- **Frame:** Fixed-size block in physical RAM. Same size as page.

**Page Table:** Stores mapping

```
Virtual Page
↓
Physical Frame
```

### Address Translation

```
CPU
↓
Virtual Address
↓
MMU
↓
Page Table
↓
Physical Address
↓
RAM
```

**Benefits:**
- Eliminates external fragmentation
- Better memory utilization
- Enables virtual memory
- Easy relocation

**Disadvantages:**
- Page table consumes memory.
- Extra address translation overhead (reduced using the TLB cache).

### Experiment

- Page size : `getconf PAGE_SIZE` or `getconf PAGESIZE`

---

## 8. Swapping

**Problem:** RAM becomes full. New programs cannot start.

**Solution:** Move an entire inactive process (or, in modern systems, more commonly its inactive pages) from RAM to disk to free memory.

```
RAM :
Chrome
VS Code
Firefox
Python

↓

Firefox inactive

↓

Disk
RAM now has free space.
```

**Swap In:**
```
Disk
↓
RAM
```
which means Process resumes.

**Swap Out:**
```
RAM
↓
Disk
```
which means Process becomes inactive.

**Modern Systems:** Modern operating systems primarily swap pages, not entire processes. Whole-process swapping is mostly a historical concept.

**Drawback:**
- Disk is much slower than RAM.
- Heavy swapping causes major performance degradation.

### Experiment

- Current swap usage : `swapon --show`
- Memory + swap : `free -h`
- Live monitoring : `vmstat 1`

---

## Quick Revision

| Topic            | Solves Which Problem?                          |
| ---------------- | ---------------------------------------------- |
| Memory Hierarchy | Balance speed, cost, and capacity              |
| Address Space    | Process isolation and security                 |
| Virtual Memory   | Run programs larger than RAM                   |
| Paging           | Efficient allocation without contiguous memory |
| Swapping         | Free RAM when memory is exhausted              |

---

## Commands to Remember

```bash
free -h                  # RAM and swap usage
cat /proc/meminfo        # Detailed memory information
lscpu                    # CPU and cache details
cat /proc/$$/maps        # Memory map of current shell
pmap $$                  # Process memory map
vmstat 1                 # Live memory and swap statistics
getconf PAGE_SIZE        # System page size
swapon --show            # Active swap devices/files
```

---
