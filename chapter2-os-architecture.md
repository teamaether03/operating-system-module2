# Chapter 2 — OS Architecture

> **NOTE :** All the commands which are listed below, is in bash.

---

## 1. Components of an Operating System

**Problem:** Managing hardware directly is extremely complex.  
**Solution:** Divide the OS into specialized components, each handling one responsibility.

| Component | Purpose |
| ------------------------------ | --------------------------------------------- |
| Kernel | Controls hardware and system resources |
| Shell | Takes user commands and passes them to the OS |
| System Libraries | Provide ready-made functions for programs |
| System Utilities | Tools for managing the system |
| Device Drivers | Allow OS to communicate with hardware |
| Filesystem | Organizes and stores data |
| Init System | Starts the operating system and services |
| Package Manager | Installs and updates software |
| Desktop Environment (Optional) | Provides graphical interface |

---

## 2. Kernel

The Kernel is the core part of the operating system that directly controls the hardware.

### Responsibilities

- CPU Scheduling
- Memory Management
- Process Management
- File Management
- Device Management
- Security & Permissions
- Networking

### Without Kernel

- Programs cannot use CPU
- No memory allocation
- No hardware communication
- No multitasking

### Experiment

```bash
# Current Kernel
uname -r

# Detailed Kernel Information
uname -a

# Kernel Logs
dmesg | less
```

---

## 3. Shell

The Shell is a command interpreter that allows users to communicate with the operating system.

### Example

The shell converts these commands into system calls.

```bash
ls
pwd
mkdir test
```

### COMMANDS

```bash
# Check Current Shell
echo $SHELL

# Available Shells
cat /etc/shells

# Current Running Shell
ps -p $$
```

---

## 4. Shell vs Kernel

| Shell | Kernel |
| -------------------------------- | -------------------- |
| User Interface | Core of OS |
| Accepts commands | Executes requests |
| Runs in User Space | Runs in Kernel Space |
| Doesn't access hardware directly | Controls hardware |

### Command Interpreter

The shell is called a Command Interpreter because it reads commands, understands them, and requests the kernel to perform them.

**Example ;**

```text
mkdir project
```

**Flow :**

```text
User
   ↓
Shell
   ↓
System Call
   ↓
Kernel
   ↓
Filesystem
```

### GUI vs CLI

#### GUI (Graphical User Interface)

- Windows
- Icons
- Mouse
- Easy to use

#### CLI (Command Line Interface)

- Commands
- Keyboard
- Faster
- Better for automation

> **NOTE :** Both eventually communicate with the Kernel.

### How Commands Reach the Kernel

**Example :**

```bash
cat file.txt
```

**Flow :**

```text
User
 ↓
Shell
 ↓
cat program
 ↓
System Calls
 ↓
Kernel
 ↓
Filesystem
 ↓
Disk
```

### Real-world Example

**You type :**

```bash
cp a.txt b.txt
```

**The shell starts the cp program.**

The cp program requests:

- Open file
- Read data
- Create destination file
- Write data
- Close files

> **NOTE:** All these operations are performed by the Kernel.

---

## 5. User Space vs Kernel Space

**Problem :** If every program could directly access hardware, one buggy or malicious program could crash the entire system.  
**Solution :** Separate execution into two protected areas.

### User Space

Runs:

- Chrome
- VS Code
- Python
- Firefox
- Games

Cannot directly access:

- RAM
- CPU
- Disk
- Hardware

- Must request the Kernel.

### Kernel Space

Runs:

- Kernel
- Drivers
- Scheduler
- Memory Manager

Has full hardware access.

### Architecture

```text
Applications
Browser
Python
VS Code
--------------------
User Space
--------------------
System Calls
--------------------
Kernel Space
Kernel
Drivers
Filesystem
Scheduler
--------------------
Hardware
```

---

## 6. System Calls ( syscall )

A System Call is the interface through which a user program requests services from the Kernel.

**Why Needed :** Problem -> User programs cannot directly access hardware.  
**Solution:** Provide controlled entry points into the Kernel.

### Common System Calls

| System Call | Purpose |
| ----------- | ---------------------- |
| open() | Open file |
| read() | Read file |
| write() | Write file |
| close() | Close file |
| fork() | Create process |
| exec() | Run program |
| wait() | Wait for child process |
| exit() | End process |

### Experiment

```bash
# Trace system calls
strace ls

# Trace file operations
strace cat file.txt
```

---

## 7. User Programs vs System Programs

**Program:** A program is a set of instructions written to make a computer perform a specific task.

### User Programs

Programs used to perform user tasks.

**Examples :**

- Chrome
- Firefox
- VLC
- VS Code
- LibreOffice

### System Programs

Programs that help operate the operating system.

**Examples :**

- ls
- cp
- mv
- ps
- top
- systemctl

### Utilities

Small programs performing specific tasks.

**Examples :**

- ls
- cp
- mv
- grep
- find
- chmod
- ping

### Background Services (Daemon)

Programs running continuously in the background.

**Examples :**

- SSH Server
- Network Manager
- Cron
- Printing Service

### Commands

```bash
# Check Running Services
systemctl list-units --type=service

# Running Processes
ps aux
```

---

## 8. Boot Overview

**Problem :** A computer cannot directly start the operating system after power-on.  
**Solution :** A sequence of programs gradually loads the OS.

### Boot Process

```text
Power On
      ↓
BIOS / UEFI
      ↓
Bootloader (GRUB)
      ↓
Kernel Loaded
      ↓
Init System (systemd)
      ↓
System Services Start
      ↓
Login Screen / Shell
```

### Components

- **BIOS / UEFI :** Initializes hardware.
- **Bootloader :** Loads the Kernel into memory.
  - Example : GRUB
- **Kernel :** Starts CPU scheduling, memory management, drivers, and mounts the root filesystem.
- **Init System :** Starts essential services.
  - Modern Linux uses : systemd
- **User Login :** User logs in and receives either:
  - GUI
  - Shell

### Experiment

```bash
# Current Boot Time
uptime

# Detailed Boot Analysis
systemd-analyze

# Boot Performance
systemd-analyze blame

# Current Init Process
ps -p 1 -f
```

### Complete OS Architecture Flow

```text
User
   ↓
Shell / GUI
   ↓
User Programs
   ↓
System Calls
   ↓
Kernel
   ↓
Drivers
   ↓
Hardware
```

---
