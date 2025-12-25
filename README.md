# CEEN: A Hybrid-Kernel Operating System Architecture for Clarity, Efficiency, and Elegance

## Abstract

This paper presents **CEEN**, a next-generation operating system designed from the ground up with no dependency on UNIX, NT, or ELF-based ecosystems. CEEN aims to establish a new foundation for modular, performant, and secure system design through a hybrid kernel architecture, a custom boot process, and an integrated software distribution ecosystem. Unlike conventional OS structures, CEEN defines its own executable format, package management model, and dependency resolution framework. The system’s guiding principle—**Clarity, Efficiency, Elegance, Nothing else**—informs every component, from its low-level memory manager to its high-level desktop environment.

---

## 1. Introduction

Operating system design has remained remarkably conservative since the emergence of UNIX and Windows NT. Modern kernels replicate legacy abstractions, sacrificing clarity and efficiency for compatibility. CEEN challenges this paradigm by rethinking the entire stack: bootloading, kernel structure, file system, and user-space model.

Built on a hybrid kernel, CEEN seeks to combine the monolithic kernel’s performance with the microkernel’s modularity and fault isolation. The project introduces a suite of custom subsystems—**CFS (Ceen File System)**, **CPM (Ceen Package Manager)**, and **CDE (Ceen Desktop Environment)**—integrated under a cohesive design philosophy rather than retrofitted for compatibility.

---

## 2. System Architecture

### 2.1 Hybrid Kernel Design

CEEN’s kernel is structured as a hybrid core. Core services such as scheduling, memory management, and interrupt handling reside in kernel space, while peripheral services (e.g., file system handlers, network stacks, and I/O daemons) operate in user space via a low-latency IPC framework.

The kernel exports a small and stable set of system calls, emphasizing message-based communication. The IPC system is optimized for zero-copy data transfer between user and kernel contexts, maintaining microsecond-level responsiveness.

### 2.2 Process Management

The CEEN Process Manager introduces the concept of **Task Objects**, which encapsulate threads, address spaces, and capability handles. It uses a balanced-tree scheduler inspired by Linux’s Completely Fair Scheduler (CFS), extended with real-time policies and asymmetric multi-core awareness.

Capabilities replace traditional user/group permissions, providing fine-grained access control over all kernel objects.

### 2.3 Memory Management

CEEN employs a **Hierarchical Memory Model**, integrating virtual paging, kernel heap allocation via a hybrid slab/slub model, and user-space allocators. Memory tagging, guard pages, and per-process isolation are enforced at the hardware level.

The MMU abstraction layer allows seamless portability across architectures such as **x86_64**, **ARM64**, and **RISC-V** without altering core kernel logic.

### 2.4 Interrupt and Event Handling

The system features a unified **Interrupt Dispatch Table (IDT)** with dynamic reload capability, supporting both local and I/O APICs for SMP scalability. **Deferred Procedure Calls (DPCs)** enable prioritized background task execution, balancing real-time responsiveness and throughput.

---

## 3. Core Components

### 3.1 CFS: The Ceen File System

CFS is a transactional, journaling file system using extent-based allocation and metadata separation for high performance and reliability. It supports copy-on-write snapshots, ACLs, and rich file attributes.

CFS introduces an **Object Metadata Layer**, allowing flexible file typing and dynamic attributes similar to object storage semantics. It integrates directly with the Virtual File System (VFS) and can be mounted across devices and sandboxes.

### 3.2 CPM: The Ceen Package Manager

Unlike traditional dependency managers (APT, RPM, Pacman), CPM adopts a hash-based static bundle model.

Each package resides in: `/pkgroot/<hash>/`


This ensures immutability and reproducibility. CPM supports atomic updates, rollbacks, and manifest-based dependency resolution.

This model resembles functional package systems such as **Nix**, but prioritizes performance and simplicity, leveraging system-level caching and binary deduplication.

### 3.3 CDE: The Ceen Desktop Environment

CDE is a modular, GPU-accelerated environment built around the **Ceen Display Server (CDS)** and the **Ceen UI Toolkit (CUT)**.

Unlike X11 or Wayland, CDS operates as a message-based composition system where every window is an addressable object. The rendering stack utilizes a Vulkan-like API abstraction layer (**CEEN-GFX**) to enable hardware acceleration, sandboxed rendering, and predictable frame scheduling.

---

## 4. Bootloader and Initialization

### 4.1 Custom Bootloader

CEEN employs its own bootloader specification (**CeenBoot**), not ELF-based, to load kernel images and initialization modules. The bootloader passes a memory map, device descriptors, and initial package mounts to the kernel.

It supports runtime command shells for debugging and recovery.

### 4.2 Init System

The initialization daemon (**cinit**) is event-driven. Instead of static service scripts, system startup occurs through reactive events triggered by hardware discovery and mount readiness.

Initialization logic is scriptable in a lightweight DSL or a Lua subset, providing transparency and flexibility.

---

## 5. User-Space Architecture

### 5.1 System Libraries

System libraries (**libceen** and **libceen++**) abstract system calls into a stable API layer, decoupled from POSIX semantics. The design encourages native CEEN applications while enabling compatibility shims for portability layers.

### 5.2 Shell Environment

The **CEEN Shell (csh)** is designed for extensibility and direct kernel messaging. Unlike legacy shells, it interacts with kernel services via structured IPC commands rather than textual parsing.

---

## 6. Security Model

Security is based on **object capabilities**, replacing hierarchical permissions. Each process receives explicit tokens defining which kernel or user-space objects it may access.

A secondary **Mandatory Access Control (MAC)** layer enforces system-wide policies, enabling sandboxed applications and privilege isolation without performance overhead.

---

## 7. Networking Stack

The networking stack is modular, protocol-agnostic, and message-oriented. Each layer (Ethernet, IP, TCP, UDP, etc.) operates as a kernel or user-space module registered via the IPC bus.

Zero-copy ring buffers ensure high throughput, while network sockets are treated as system objects governed by capabilities.

---

## 8. Development and Debugging

CEEN provides a native debugger integrated with QEMU environments:

```bash
qemu-system-x86_64 -kernel ceen.krn -serial stdio -d int,cpu_reset
```

The kernel supports GDB stubs and runtime tracing for low-level diagnostics. A custom build system, **CEENBuild**, replaces traditional makefiles, leveraging LLVM IR-level caching and dependency graphs.

---

## 9. Logging and Event Management

System-wide logging occurs through the **Event Bus**, a unified mechanism for real-time diagnostics and auditing.

Every subsystem publishes structured events to: `/system/logpipe`

This ensures consistent metadata and timestamps without reliance on external daemons.

---

## 10. Virtualization and Future Work

CEEN integrates a lightweight hypervisor layer (**CHL**) within the kernel for native virtualization.

Future goals include full support for containerized sandboxes, distributed file synchronization, and formal verification of kernel components.

---

## 11. Conclusion

CEEN represents a return to first principles in operating system design. By discarding historical dependencies and adopting a clean, hybrid architecture, it demonstrates that clarity and performance are not mutually exclusive.

The system’s emphasis on isolation, reproducibility, and design coherence positions it as a foundation for future experimental and production-grade computing environments.

---

**CEEN**  
**Clarity. Efficiency. Elegance. Nothing else.**

**Ammari Riadh Abderahmane (ARA)**
