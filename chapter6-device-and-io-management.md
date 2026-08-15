# Chapter 6 — Device & I/O Management

**Core problem:** The CPU is fast, but devices like disks, keyboards, network cards, GPUs, and USB devices are slow and operate differently. The OS needs mechanisms to communicate with them efficiently without wasting CPU time.

> **NOTE : All the commands are written in BASH.**

---

## 1. Device Drivers

**Problem:** Every hardware device has different registers, protocols, and behavior. The OS cannot directly handle every device in a device-specific way.
**Solution:** Device Driver — software that provides an interface between the OS and a specific hardware device.

```text
Application 
    ↓ 
System Call 
    ↓ 
OS / Kernel 
    ↓ 
Device Driver 
    ↓ 
Hardware Device
```

### What a driver does :

* Initializes the device
* Sends commands to hardware
* Reads device status
* Handles device-specific operations
* Handles device interrupts
* Exposes a standard interface to the OS

### Example :

You execute: `cat file.txt`

The application doesn't need to know how your SSD works.

```text
cat
 ↓
read() system call
 ↓
Filesystem
 ↓
Storage driver
 ↓
Storage controller
 ↓
SSD
```

### Linux experiment :

Shows device files: `ls /dev`
Shows kernel device classes: `ls /sys/class`

---

## 2. Interrupts

**Problem:** A device may take milliseconds to complete an operation. If the CPU continuously checks whether the device is finished, CPU time is wasted.

### Bad approach — Polling

```text
CPU → "Done?"
Device → "No"

CPU → "Done?"
Device → "No"

CPU → "Done?"
Device → "Yes"
```

### Solution — Interrupt

The CPU starts the operation and continues doing other work.

When the device finishes:

```text
Device
   ↓
Interrupt
   ↓
CPU
   ↓
Interrupt Handler
   ↓
Process continues
```

### Example : Pressing a keyboard key:

```text
Key pressed
    ↓
Keyboard controller
    ↓
Hardware interrupt
    ↓
CPU
    ↓
Keyboard driver
    ↓
OS
    ↓
Application
```

### Important terms :

* IRQ — Interrupt Request
* ISR — Interrupt Service Routine
* Interrupt Handler — kernel code that handles the interrupt

### Linux experiment :

Shows interrupt counts for devices: `cat /proc/interrupts/`

---

## 3. DMA — Direct Memory Access

**Problem:** Moving large amounts of data through the CPU is inefficient.

**Without DMA:** Device → CPU → RAM
The CPU must participate heavily in every transfer.

**Solution — DMA**
A DMA controller allows a device to transfer data directly to/from RAM.

```text
        DMA
       ↙   ↘
Device      RAM
```

The CPU mainly:

1. Configures the DMA transfer
2. Starts it
3. Does other work
4. Receives an interrupt when finished

### Example:

Reading a large file:

```text
SSD
 ↓
DMA
 ↓
RAM
 ↓
CPU processes data
```

This is much more efficient than making the CPU manually move every byte.

### Linux experiment :

Shows DMA channels where applicable: `cat /proc/dma`

---

## 4. Buffering

**Problem:** Producer and consumer may operate at different speeds.

### Example:

```text
Keyboard → slow
CPU      → extremely fast
```

If the CPU had to process every character immediately, synchronization would become inefficient.

**Solution — Buffer**
A buffer is temporary memory used to hold data while it waits to be processed or transferred.

```text
Producer
   ↓
[ BUFFER ]
   ↓
Consumer
```

### Example : When you type:

```text
H E L L O
    ↓
Keyboard buffer
    ↓
Terminal
```

The characters can wait in the buffer until the application reads them.

### Why buffering matters :

* Handles speed mismatch
* Allows batching
* Smooths bursts of data
* Reduces unnecessary device operations

---

## 5. Caching

**Problem:** Some data is accessed repeatedly, but the underlying device is slow.

### Example:

```text
RAM → fast
SSD → slower
HDD → much slower
```

Repeatedly reading the same data from storage wastes time.

**Solution — Cache**
Keep frequently/recently accessed data in a faster storage layer.

```text
Application
    ↓
Cache
   ↙ ↘
Hit   Miss
 ↓      ↓
Data   Storage
```

**Cache Hit:** Requested data exists in cache:

`Application → Cache → Data`

* Fast.

**Cache Miss:** Data isn't cached:

`Application → Cache → Storage → RAM → Cache`

* Slower, but the data can now be reused.

### Linux experiment :

Check memory/cache information: `free -h`

Check filesystem/storage cache effects:

```bash
sync
sudo sh -c 'echo 3 > /proc/sys/vm/drop_caches'
```

> **NOTE : Do not run the second command casually on a production system. It clears kernel caches and is mainly useful for controlled experiments.**

---

## The Big Picture :

These five mechanisms solve different problems:

| Mechanism     | Problem                          | Solution                         |
| ------------- | -------------------------------- | -------------------------------- |
| Device Driver | Different hardware interfaces    | Standard OS ↔ hardware interface |
| Interrupt     | CPU wasting time polling         | Device notifies CPU              |
| DMA           | CPU wasting time moving data     | Device ↔ RAM transfer            |
| Buffering     | Producer/consumer speed mismatch | Temporary data storage           |
| Caching       | Repeated slow accesses           | Keep frequently used data nearby |

### Mental Model :

```text
                 APPLICATION
                      ↓
                SYSTEM CALL
                      ↓
                    KERNEL
                      ↓
               DEVICE DRIVER
                      ↓
              ┌───────┴───────┐
              ↓               ↓
          INTERRUPT          DMA
              ↓               ↓
             CPU              RAM
              ↑                 ↑
              └──── DEVICE ─────┘

          BUFFERING + CACHING
          improve the whole path
```

---
