# Chapter 1 : Introduction of Operating System

## A Flow

```text
User (Application)  ->  Operating System  ->  Hardware
```

> **Mental Model:** Keep this flow in mind.

> **NOTE:** All the commands listed below are in **bash**.

---

# 1. Evolution of Operating Systems

## Before Operating Systems

- User interacted directly with hardware.
- One program at a time.
- Programs loaded manually.
- No memory protection.
- Extremely inefficient.

### Problems

- CPU remained idle.
- Manual resource handling.
- Difficult debugging.
- No security.
- Poor hardware utilization.

---

## 2. Batch Operating Systems

**Idea:** Execute multiple jobs automatically.

### Advantages

- Better CPU utilization.
- Reduced manual work.

### Disadvantages

- No interaction while program runs.
- Long waiting time.

---

## 3. Multiprogramming OS

**Idea**

- Keep multiple programs in memory.
- CPU switches whenever one waits for I/O.

**Result:** Higher CPU utilization.

---

## 4. Time-Sharing OS

**Idea**

- CPU divided into small time slices.
- Multiple users feel like they own the computer.

### Examples

- Linux
- Unix

---

## 5. Modern Operating Systems

### Features

- Multitasking
- Multiuser
- Networking
- Virtualization
- Containers
- Cloud support
- Security
- Power management

### Examples

- Linux
- Windows
- macOS
- Android
- iOS

---

# 2. Why Operating System Exists

### Without an OS

```text
Application
     ↓
 Hardware
```

Every application must know:

- CPU
- RAM
- SSD
- Keyboard
- GPU
- Network Card

Every application would need hardware-specific code.

This is impossible to maintain.

### With an OS

```text
Application
     ↓
Operating System
     ↓
 Hardware
```

The OS acts as an abstraction layer.

### Benefits

- Simpler software development.
- Hardware independence.
- Security.
- Resource sharing.
- Stability.

---

# 3. Responsibilities of an Operating System

An OS has three primary responsibilities.

## A. Resource Manager

Manages:

- CPU
- RAM
- Storage
- Network
- Devices

**Example**

Chrome, VS Code, Spotify all run together because the OS shares resources.

---

## B. Hardware Abstraction

Applications don't communicate directly with hardware.

Instead:

```text
App
 ↓
System Calls
 ↓
Kernel
 ↓
Hardware
```

---

## C. Protection

The OS prevents:

- One program reading another's memory.
- Unauthorized disk access.
- Illegal hardware access.
- Privilege escalation.

---

# 4. Services of an Operating System

## A. Program Execution

- Loads program into memory.
- Creates a process.
- Starts execution.
- Terminates after completion.

**Example**

Double-clicking Chrome.

---

## B. Process Management

Responsible for:

- Process creation
- Scheduling
- Context switching
- Synchronization
- Termination

**Example**

Running Chrome + VS Code + Discord simultaneously.

---

## C. Memory Management

Responsible for:

- RAM allocation
- Deallocation
- Virtual memory
- Memory protection

**Example**

- Chrome gets 2 GB RAM.
- VS Code gets 600 MB.

---

## D. File Management

Responsible for:

- Creating files
- Reading
- Writing
- Renaming
- Deleting
- Permissions

**Example**

Saving a PDF.

---

## E. I/O Management

**I/O = Input / Output**

### Examples

- Keyboard
- Mouse
- Display
- Printer

The OS provides a common interface for all devices.

---

## F. Device Management

Controls hardware devices using device drivers.

### Examples

- GPU driver
- Wi-Fi driver
- Printer driver

Without drivers: Hardware becomes unusable.

---

## G. Security & Protection

Provides:

- Authentication
- Permissions
- Access control
- Encryption support
- Isolation

**Example**

Normal users cannot modify system files.

---

## H. Resource Allocation

Allocates:

- CPU time
- RAM
- Disk space
- Network bandwidth

**Goal:** Fair and efficient sharing.

---

## I. Error Detection

Detects:

- Memory errors
- Hardware failures
- Disk errors
- Invalid operations

**Example**

Blue Screen (Windows) or Kernel Panic (Linux).

---

## J. Networking Services

Provides:

- TCP/IP stack
- Socket APIs
- Wi-Fi management
- Ethernet management
- DNS
- Routing

Without the OS, Internet access would be extremely difficult for applications.

---

# 5. Resources Managed by an Operating System

## CPU

Manages:

- Scheduling
- Time slicing
- Context switching
- Priorities

**Example**

100 processes share a 4-core CPU.

---

## Memory

Manages:

- RAM
- Cache coordination
- Virtual memory
- Paging
- Protection

---

## Storage

Manages:

- HDD
- SSD
- NVMe

### Tasks

- File systems
- Space allocation
- Caching
- Permissions

---

## I/O Devices

### Examples

- Keyboard
- Mouse
- Webcam
- Printer
- Speakers

The OS coordinates communication between applications and devices.

---

## Network Resources

Manages:

- Network interfaces
- Ports
- IP addresses
- Packets
- Bandwidth

**Example**

Chrome and Discord use the same Wi-Fi connection simultaneously.

---

# 6. Types of Operating Systems

## Batch OS

- Executes jobs in batches.
- No user interaction.

---

## Multiprogramming OS

- Multiple programs remain in memory.
- CPU switches when one waits.

---

## Multitasking OS

Runs multiple tasks seemingly at the same time.

### Examples

- Windows
- Linux
- macOS

---

## Multiuser OS

Supports multiple users simultaneously.

**Example**

Linux servers

---

## Multiprocessing OS

Uses multiple CPUs or CPU cores.

**Example**

Modern desktops and servers.

---

## Real-Time Operating System (RTOS)

Designed for predictable timing.

### Types

- Hard Real-Time
- Soft Real-Time

### Examples

- Aircraft control
- Medical devices
- Robotics

---

## Distributed Operating System

Controls multiple computers as one system.

**Goal**

- Resource sharing
- Parallel computation

---

## Network Operating System

Focuses on providing network-based services.

### Examples

- File sharing
- Remote login
- User authentication

---

## Embedded Operating System

Runs on dedicated hardware with limited resources.

### Examples

- Routers
- Smart TVs
- Washing machines
- IoT devices

---

## Mobile Operating System

Optimized for smartphones.

### Examples

- Android
- iOS

---

# FEW PRACTICALS (LINUX)

## Check OS Information

```bash
uname -a
```

### Output

```text
Linux kali 6.19.11+kali-amd64 #1 SMP PREEMPT_DYNAMIC Kali 6.19.11-1kali1 (2026-04-09) x86_64 GNU/Linux
```

| Field | Single-line Explanation |
|-------|-------------------------|
| Linux | Kernel name (the OS kernel currently running). |
| kali | Hostname (name of your computer). |
| 6.19.11+kali-amd64 | Kernel version installed on your system. |
| #1 | First kernel build/release by the Kali maintainers. |
| SMP | Symmetric Multiprocessing; kernel supports multiple CPU cores. |
| PREEMPT_DYNAMIC | Kernel can dynamically switch preemption modes for responsiveness/performance. |
| Kali | Linux distribution that built the kernel. |
| 6.19.11-1kali1 | Kali's package version of this kernel. |
| (2026-04-09) | Date the kernel was compiled. |
| x86_64 | 64-bit CPU architecture (AMD64/Intel 64). |
| GNU/Linux | GNU user-space tools running on the Linux kernel. |

---

## Kernel Version

```bash
uname -r
```

---

## Operating System Details

```bash
cat /etc/os-release
```

| Field | Single-line Explanation |
|-------|-------------------------|
| PRETTY_NAME | Human-readable operating system name. |
| NAME | Official operating system name. |
| VERSION_ID | Installed OS version number. |
| VERSION | Full version string of the distribution. |
| VERSION_CODENAME | Development branch/codename of the release. |
| ID | Short identifier used by software packages. |
| ID_LIKE | Base distribution from which this OS is derived. |
| HOME_URL | Official website of the distribution. |
| SUPPORT_URL | Official support/community forum. |
| BUG_REPORT_URL | Website for reporting bugs. |
| ANSI_COLOR | Default terminal color used by the distribution. |

### When RAM is used?

- Running applications
- Kernel
- Browser tabs
- Games
- IDEs
- Background services

### When Swap is used?

- RAM becomes nearly full.
- Inactive memory pages are moved from RAM to disk.
- Prevents applications from crashing due to low memory.
- Much slower than RAM because it uses storage (SSD/HDD).

---

## CPU Information

```bash
lscpu
```

## Memory Information

```bash
free -h
```

## Storage Information

```bash
lsblk
```

## Connected PCI Devices

```bash
lspci
```

> PCI (Peripheral Component Interconnect) is a hardware bus that allows expansion devices to communicate with the CPU through the motherboard.

Modern systems mostly use PCI Express (PCIe).

Examples:

- GPU
- Network Card
- Wi-Fi Card
- Sound Card
- SSD Controller
- USB Controller

| Device | Single-line Explanation |
|---------|-------------------------|
| Host bridge | Connects the CPU with RAM and the PCI bus. |
| PCI bridge | Connects one PCI bus to another PCI bus. |
| ISA bridge | Provides compatibility with legacy hardware devices. |
| IDE interface | Controller for older IDE/PATA storage devices. |
| Bridge (PIIX4 ACPI) | Handles ACPI features such as power management. |
| System peripheral (VMware VMCI) | VMware communication interface between guest and host. |
| VGA compatible controller | Virtual graphics card used for display output. |
| SCSI storage controller | Controller that manages virtual hard disks in VMware. |

---

## USB Devices

```bash
lsusb
```

## Running Processes

```bash
ps aux
```

| Field | Single-line Explanation |
|-------|-------------------------|
| USER | User who owns the process. |
| PID | Unique Process ID assigned by the kernel. |
| %CPU | Percentage of CPU currently used. |
| %MEM | Percentage of RAM currently used. |
| VSZ | Total virtual memory allocated to the process. |
| RSS | Actual physical RAM currently used. |
| TTY | Terminal associated with the process. |
| STAT | Current state of the process (Running, Sleeping, etc.). |
| START | Time when the process started. |
| TIME | Total CPU time consumed since it started. |
| COMMAND | Program or command that launched the process. |

---

# ONE LINE DEFINITION FOR OPERATING SYSTEM

> **An Operating System is a resource manager and abstraction layer between applications and hardware. Its primary job is to manage CPU, memory, storage, devices, and networking efficiently while providing security, isolation, and standardized services so applications don't need to control hardware directly.**
