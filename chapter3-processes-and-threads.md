# Chapter 3 — Processes & Threads

> **Note:** all the commands are written in bash.

---

# 1. Program vs Process

## Program

A program is a passive file containing instructions stored on disk.

Examples: firefox, python, vim, Process.

## Process

A process is a program that is currently executing in memory.

A process contains:

- Program code
- Data
- CPU registers
- Stack
- Heap
- Open files
- Process ID (PID)

### Example

```text
chrome (Program)  ->  Double Click  ->  Chrome Process (Running)
```

You can open Chrome three times.

```text
Program : 1
Processes : 3
```

-> Same program, multiple processes.

## Program vs Process

| Program | Process |
|----------|---------|
| Static | Dynamic |
| Stored on disk | Loaded into RAM |
| Passive | Active |
| No PID | Has PID |
| Doesn't execute | Executes instructions |

## Practical

Show running processes:

```bash
ps
```

Detailed list:

```bash
ps -ef
```

Interactive viewer:

```bash
top
```

Modern alternative:

```bash
htop
```

Current shell PID:

```bash
echo $$
```

Find the PID of a process:

```bash
pgrep firefox
```

or

```bash
pidof firefox
```

Stop a running process:

```bash
kill PID
```

or

```bash
kill -15 PID
```

Force kill process:

```bash
kill -9 PID
```

Kill by name:

```bash
pkill firefox
```

To terminates/kill all processes with a given name:

```bash
killall process_name
```

To verify the termination:

```bash
pa aux | grep firefox
```

To set the priority level (use nice):

```bash
nice -n VALUE command
```

Example:

```bash
nice -n 10 python3 script.py
```

To check Nice value:

```bash
ps -o pid,ni,comm
```

Example:

```text
PID   NI COMMAND
2120   0 firefox
3001  10 python3
4005  19 backup
```

Change the nice value of an existing process:

```bash
renice NICE_VALUE -p PID
```

To verify:

```bash
ps -o pid,ni,comm -p PID
```

> **Note:** lower nice value, high the CPU priority.

Also, make sure that the Nice Value Range is

```text
-20 -----------------------------> 19
highest priority           lowest priority
```

---

# 2. Process Control Block (PCB)

PCB is a kernel data structure that stores all information about a process.

Without PCB, the OS cannot manage a process.

## PCB Stores

- Process ID (PID)
- Process State
- Program Counter
- CPU Registers
- Scheduling Information
- Memory Information
- Open Files
- Permissions
- Parent Process ID (PPID)

## Real Life Example

Think of PCB as a student's record file.

It stores everything the school needs to manage the student.

---

# 3. Process Lifecycle

A process changes states while executing.

## States

```text
--------------------------------
New
 │
 ▼
Ready
 │
 ▼
Running
 │
 ├────────► Waiting
 │             │
 │             ▼
 └────────── Ready
 │
 ▼
Terminated
--------------------------------
```

## New

Process is created.

## Ready

Waiting for CPU.

## Running

Currently executing.

## Waiting (Blocked)

Waiting for:

- Keyboard input
- Disk
- Network
- File

> **Note:** in Waiting state, CPU is not used.

## Terminated

Execution finished.

### Example

Run Firefox

```text
New -> Ready -> Running -> Waiting (Web page downloading) -> Ready -> Running -> Terminated
```

---

# 4. Scheduling (Overview)

Scheduling is the process of deciding which process gets the CPU next.

Performed by the CPU Scheduler.

## Why Needed?

CPU is limited.

Many processes compete for it.

The scheduler decides the execution order.

## Goals

- Maximum CPU utilization
- Fairness
- Fast response
- High throughput
- Low waiting time

## Common Scheduling Algorithms

- FCFS (First Come First Serve)
- SJF (Shortest Job First)
- Round Robin
- Priority Scheduling
- Multilevel Queue

---

# 5. Context Switching

Context Switching is the process of saving one process's state and loading another process's state.

## Why?

CPU can execute only one process per core at a time.

The OS rapidly switches between processes.

## Steps

```text
Running Process A
↓
Save Registers into PCB
↓
Load PCB of Process B
↓
Run Process B
```

## Context Includes

- Registers
- Program Counter
- Stack Pointer
- CPU Flags

## Drawback

Context switching is overhead.

No useful work is done during switching.

Too many switches reduce performance.

Observe Context Switches:

```bash
vmstat 1
```

Look at:

```text
cs
```

It shows context switches per second.

---

# 6. Threads

A thread is the smallest unit of CPU execution inside a process.

A process may contain one or multiple threads.

## Single Thread

```text
Process
↓
Thread
```

## Multi-threaded Process

```text
Process
 ├── Thread 1
 ├── Thread 2
 ├── Thread 3
 └── Thread 4
```

## Threads Share

- Code
- Heap
- Global Variables
- Open Files

## Threads Have Their Own

- Stack
- Registers
- Program Counter

## Benefits

- Faster execution
- Better responsiveness
- Parallelism
- Lower overhead than processes

## Example : Chrome

```text
Browser Process
├── UI Thread
├── Rendering Thread
├── Network Thread
├── Audio Thread
└── GPU Thread
```

Observe Threads or Show threads:

```bash
ps -eLf
```

or

```bash
top -H
```

---

# 7. Multitasking

Multitasking is the ability of an operating system to run multiple processes seemingly at the same time.

## Reality

Single CPU core

```text
Task A
↓
Task B
↓
Task C
↓
Task A
↓
Task C
```

> **NOTE:** This happens so fast that users think everything runs simultaneously.

## On Multi-core CPU

```text
Core 1 → Chrome
Core 2 → VS Code
Core 3 → Music
Core 4 → Terminal
```

Provide True parallel execution.

## Types

### Preemptive Multitasking

OS interrupts processes automatically.

Example:

- Linux
- Windows
- macOS

### Cooperative Multitasking

Process voluntarily gives up CPU.

Old operating systems used this.

## Experiment

Open many applications.

Run:

```bash
top
```

Observe CPU usage changing.

---

# 8. Inter-Process Communication (IPC) (Concept Only)

Processes are isolated for security and stability.

Sometimes they must exchange data.

IPC provides mechanisms for communication.

## Why Processes Need to Communicate

Examples:

- Browser ↔ GPU Process
- Spotify ↔ Audio Service
- Web Server ↔ Database
- Parent ↔ Child Process
- Terminal ↔ Shell

Without IPC, processes cannot share data or coordinate tasks.

## Message Passing (Concept)

Processes exchange messages through the operating system.

```text
Process A
↓
OS
↓
Process B
```

### Characteristics

- Safe
- Easy to manage
- No shared memory required
- Slightly slower due to OS involvement

### Real-world Examples

- Client ↔ Server communication
- Microservices
- Chat applications

## Shared Memory (Concept)

The OS creates a memory region accessible by multiple processes.

```text
Process A
        │
        ▼
 Shared Memory
        ▲
        │
Process B
```

### Characteristics

- Very fast
- High performance
- Processes directly read/write memory
- Requires synchronization (e.g., locks) to avoid data corruption

### Real-world Examples

- Video streaming
- Image processing
- High-performance databases
- AI applications exchanging large tensors

## Message Passing vs Shared Memory

| Message Passing | Shared Memory |
|-----------------|---------------|
| Uses OS to transfer data | Shares same memory region |
| Simpler | More complex |
| Safer | Faster |
| Higher overhead | Lower overhead |
| Best for distributed communication | Best for high-speed local communication |

## Commands Summary

Show running processes:

```bash
ps
```

Detailed process list:

```bash
ps -ef
```

Monitor running processes:

```bash
top
```

Interactive process monitor:

```bash
htop
```

Display current shell's PID:

```bash
echo $$
```

Monitor system activity and context switches (cs):

```bash
vmstat 1
```

Display all threads:

```bash
ps -eLf
```

Monitor threads instead of processes:

```bash
top -H
```

---

# Summary

- Program = Static executable file on disk.
- Process = Running instance of a program in memory.
- PCB = Kernel structure storing all process information.
- Lifecycle = New → Ready → Running → Waiting → Ready → Terminated.
- Scheduler decides which process gets the CPU.
- Context Switching saves one process's state and restores another's.
- Thread = Smallest execution unit within a process.
- Multitasking allows multiple tasks to share CPU time efficiently.
- IPC enables isolated processes to exchange data using mechanisms like Message Passing and Shared Memory.

---
