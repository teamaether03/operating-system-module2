# Chapter 8 — Modern Computing

> NOTE : All the commands are written in BASH.

---

## 1. Virtual Machines (VMs)

**Problem:** A physical machine can run only one OS environment directly, but developers need to run multiple isolated OS environments on the same hardware.

**Solution:** A Virtual Machine emulates a complete computer system so multiple guest OSs can share one physical machine.

### Architecture :

```text
Hardware → Hypervisor → Guest OS → Applications
```

**Host:** Physical machine.
**Hypervisor:** Manages CPU, RAM, storage, and devices between VMs.
**Guest OS:** OS running inside the VM.
**VM:** Virtual CPU, memory, disk, network interface, etc.

### Types :

**Type 1 — Bare-metal:** Hypervisor runs directly on hardware.

* VMware ESXi
* Microsoft Hyper-V
* Xen

**Type 2 — Hosted:** Hypervisor runs on an existing OS.

* VirtualBox
* VMware Workstation

### Experiment :

If using Linux:

```bash
lscpu
free -h
lsblk
```

These show the CPU, memory, and storage resources available to the current environment.

**Key idea:** VM virtualizes hardware

---

## 2. Containers

**Problem:** VMs provide strong isolation, but each VM requires a complete guest OS, making them relatively heavy.

**Solution:** Containers isolate applications while sharing the host OS kernel.

### Architecture :

```text
Hardware → Host OS → Container Runtime → Containers → Applications
```

A container typically contains:

* Application
* Dependencies
* Libraries
* Configuration

But not a complete kernel.

### Linux mechanisms behind containers :

Containers rely heavily on:

* **Namespaces** → isolate processes, networking, mounts, users, etc.
* **cgroups** → control CPU, RAM, and other resources.
* **Capabilities** → restrict privileged operations.
* **Filesystem isolation** → provide separate filesystem views.

### Experiment :

See namespaces:

```bash
lsns
```

See control groups:

```bash
ls /sys/fs/cgroup
```

See your current processes:

```bash
ps aux
```

**Key idea:** Container virtualizes/isolate the environment, not an entire machine

---

## 3. Docker — Concept

**Problem:** "Works on my machine" happens because developers have different libraries, dependencies, configurations, and environments.

**Solution:** Docker packages an application and its dependencies into a container image so it can run consistently across environments.

### Important concepts :

**Image:** A read-only template containing the application and its dependencies.
**Container:** A running instance of an image.
**Dockerfile:** Instructions used to build an image.
**Registry:** Stores and distributes images.

### Example:

```text
Dockerfile
     ↓
   Image
     ↓
 Container
     ↓
 Application
```

### Basic experiment :

Check Docker:

```bash
docker --version
```

Run a container:

```bash
docker run hello-world
```

List running containers:

```bash
docker ps
```

List all containers:

```bash
docker ps -a
```

List images:

```bash
docker images
```

Run an interactive Linux container:

```bash
docker run -it ubuntu bash
```

Inside the container:

```bash
cat /etc/os-release
```

Exit:

```bash
exit
```

---

## VM vs Container :

| Feature        | VM                | Container                 |
| -------------- | ----------------- | ------------------------- |
| Virtualizes    | Hardware/system   | OS environment            |
| Guest OS       | Yes               | No separate kernel        |
| Startup        | Slower            | Fast                      |
| Resource usage | Higher            | Lower                     |
| Isolation      | Strong            | Strong, but kernel shared |
| Typical use    | Full OS isolation | Applications/services     |

**Critical distinction:**

```text
VM = virtual machine
Container = isolated process environment
```

---

## 4. Real-World Architecture

Modern applications rarely run as one giant program.

**Problem:** A large application becomes difficult to deploy, scale, update, and maintain as a single process.

**Solution:** Break the system into independent services and deploy them using containers/VMs.

### Example — Hackathon Web Application

```text
                      Internet
                          │
                    Load Balancer
                          │
              ┌---────────┴────────---┐
              │                       │
        Frontend Container  	 Frontend Container
              │                       │
              └---────────┬────────---┘
                          │
                     API Gateway
                           │
         ┌-----────────────┼────────────----┐
         │                 │                │
    Auth Service     API Service       AI Service
      Container        Container         Container
         │                 │                │
         └-----────────────┼────────────----┘
                           │
                       Database
```

### Where VMs fit

```text
  Physical Server
        │
    Hypervisor
        │
 ┌────--┼─────┐
 VM1   VM2    VM3
  │     │      │
Docker Docker Docker
  │     │       │
Containers / Services
```

This is common in cloud infrastructure: VMs provide infrastructure isolation, while containers provide application-level packaging and isolation.

---

## Core Mental Model:

```text
Physical Hardware
       ↓
Virtual Machines
       ↓
Operating Systems
       ↓
Containers
       ↓
Applications
       ↓
Services
```

### Remember these three:

```text
VM → Give me another computer
Container → Give me an isolated environment
Docker → Package and run that environment consistently
```
