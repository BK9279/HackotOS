# HackotOS

A monolithic x86_64 kernel operating system built from scratch, featuring a modern architecture designed for educational purposes and performance optimization.

## Table of Contents

- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Features](#features)
- [System Requirements](#system-requirements)
- [Build Instructions](#build-instructions)
- [Installation & Setup](#installation--setup)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Contributing](#contributing)
- [License](#license)

---

## Project Overview

HackotOS is a fully-featured, monolithic x86_64 kernel operating system designed to demonstrate core OS concepts including:

- **Memory Management**: Virtual memory, paging, and memory protection
- **Process Management**: Task scheduling, context switching, and process isolation
- **Interrupt Handling**: Hardware and software interrupt processing
- **Device Drivers**: Support for common hardware peripherals
- **Filesystem**: Block device management and file operations
- **User Mode Execution**: Privilege separation between kernel and user space

This project serves as both an educational resource for understanding OS internals and a foundation for experimental kernel development.

### Goals

- **Educational**: Provide clear, well-documented code demonstrating OS fundamentals
- **Modular**: Organize components in a maintainable structure
- **Performance**: Optimize for speed without sacrificing clarity
- **Extensibility**: Allow easy addition of new drivers and subsystems

---

## Architecture

### Overview Diagram

```
┌─────────────────────────────────────┐
│     User Space Applications          │
├─────────────────────────────────────┤
│          System Call Interface        │
├─────────────────────────────────────┤
│     Monolithic Kernel                │
│  ┌──────────────────────────────┐   │
│  │ Process/Task Management       │   │
│  ├──────────────────────────────┤   │
│  │ Memory Management (MMU, VM)   │   │
│  ├──────────────────────────────┤   │
│  │ Interrupt & Exception Handler │   │
│  ├──────────────────────────────┤   │
│  │ File System & VFS Layer       │   │
│  ├──────────────────────────────┤   │
│  │ Device Drivers & I/O          │   │
│  ├──────────────────────────────┤   │
│  │ Synchronization Primitives    │   │
│  └──────────────────────────────┘   │
├─────────────────────────────────────┤
│     Hardware Abstraction Layer       │
├─────────────────────────────────────┤
│  x86_64 CPU, Memory, Devices         │
└─────────────────────────────────────┘
```

### Key Components

#### 1. **Boot & Initialization**
- BIOS/UEFI bootloader integration
- Kernel entry point (assembly)
- Early memory setup and paging initialization
- BSS section initialization

#### 2. **CPU & Interrupt Management**
- GDT (Global Descriptor Table) setup
- IDT (Interrupt Descriptor Table) configuration
- IRQ and exception handlers
- Interrupt vector management
- CPU feature detection and initialization

#### 3. **Memory Management**
- **Physical Memory Manager**: Bitmap or buddy allocator for physical pages
- **Virtual Memory**: Page tables, address translation, TLB management
- **Heap Management**: Kernel heap allocator
- **Virtual Address Space**: Kernel and user space separation

#### 4. **Process & Task Management**
- Process Control Blocks (PCBs)
- Thread scheduling (Round-robin, priority-based)
- Context switching with task state switching
- Process creation, termination, and state management
- Inter-process communication (IPC) primitives

#### 5. **Synchronization Primitives**
- Spinlocks for critical sections
- Mutexes and semaphores
- Condition variables
- Reader-writer locks

#### 6. **File System**
- VFS (Virtual File System) abstraction layer
- Block device interface
- Inode and dentry caching
- File operations (open, read, write, seek, close)
- Directory management

#### 7. **Device Drivers**
- PIT (Programmable Interval Timer) for timekeeping
- PS/2 keyboard driver
- Serial port (UART) for console I/O
- AHCI/SATA disk controller support
- Network device drivers (when applicable)

#### 8. **System Calls**
- User-to-kernel interface
- System call dispatcher
- Argument validation and error handling
- Common syscalls: fork, exec, exit, wait, open, read, write, etc.

---

## Features

### Implemented Features

- ✅ x86_64 Protected Mode with Long Mode (64-bit) execution
- ✅ Paging-based virtual memory with 4KB/2MB/1GB page sizes
- ✅ Preemptive multitasking with round-robin scheduling
- ✅ Memory protection and privilege levels (Ring 0/3)
- ✅ Interrupt and exception handling
- ✅ Basic file system support
- ✅ Keyboard and console I/O
- ✅ Process creation and management
- ✅ User mode execution with system calls

### Planned Features

- [ ] Advanced scheduling algorithms (CFS, priority inheritance)
- [ ] Advanced filesystem features (journaling, ACLs)
- [ ] Network stack (TCP/IP)
- [ ] Cryptographic operations
- [ ] Container/virtualization support
- [ ] Advanced graphics subsystem

---

## System Requirements

### Build Requirements

- **GNU/Linux system** (development machine)
- **GCC or Clang** compiler (x86_64 cross-compiler recommended)
- **Binutils** (assembler, linker)
- **Make** (build automation)
- **NASM** or **GNU AS** (for assembly code)
- **QEMU** (for emulation and testing)
- **grub-mkrescue** or equivalent (for bootable image creation)

### Hardware Requirements (for bare metal)

- **x86_64 compatible CPU** with at least:
  - Protected Mode support
  - Long Mode (64-bit) support
  - PAE (Physical Address Extension)
  - NX bit (No-Execute)
- **At least 512 MB RAM** (1 GB recommended)
- **Storage device** (USB or disk)

---

## Build Instructions

### 1. Install Dependencies

#### On Ubuntu/Debian:
```bash
sudo apt-get update
sudo apt-get install build-essential nasm grub-pc-bin xorriso qemu-system-x86
```

#### On Fedora/RHEL:
```bash
sudo dnf install gcc make nasm grub2-tools xorriso qemu-system-x86
```

#### On Arch:
```bash
sudo pacman -S base-devel nasm grub xorriso qemu-system-x86
```

### 2. Clone the Repository

```bash
git clone https://github.com/BK9279/HackotOS.git
cd HackotOS
```

### 3. Configure the Build

```bash
# Create a build directory
mkdir build
cd build

# Configure the kernel (optional, if using CMake or autoconf)
cmake .. -DCMAKE_BUILD_TYPE=Release
# OR
./configure --enable-optimization
```

### 4. Compile the Kernel

```bash
# Build the kernel and bootloader
make all

# Or build specific components
make kernel
make bootloader
make image
```

### 5. Create Bootable Image

```bash
# Create ISO image for GRUB bootloader
make iso

# Or create USB image
make usb
```

---

## Installation & Setup

### Running in QEMU (Recommended for Testing)

```bash
# Run with default settings
make run

# Run with debugging enabled
make run-debug

# Run with specific amount of RAM
qemu-system-x86_64 -m 2G -cdrom hackotOS.iso

# Run with additional options
qemu-system-x86_64 -m 2G -cdrom hackotOS.iso -enable-kvm -smp 4
```

### Booting on Real Hardware

1. **Create Bootable USB Drive**:
   ```bash
   # Identify USB device (be careful!)
   lsblk
   
   # Write ISO to USB
   sudo dd if=hackotOS.iso of=/dev/sdX bs=4M status=progress
   sudo sync
   ```

2. **Boot from USB**:
   - Insert USB drive
   - Reboot computer
   - Enter boot menu (usually F12, F2, DEL, or ESC depending on manufacturer)
   - Select USB drive
   - Boot HackotOS

3. **First Boot**:
   - Kernel should initialize
   - You should see boot messages
   - Shell prompt should appear (if shell is implemented)

### Configuration

Configuration options can be set in `config.h` or `kernel.conf`:

```bash
# Example configuration options
CONFIG_MAX_PROCESSES=256
CONFIG_PAGE_SIZE=4096
CONFIG_TIMER_FREQUENCY=1000
CONFIG_ENABLE_DEBUG=1
```

Rebuild after configuration changes:
```bash
make clean
make all
```

---

## Usage

### Basic Commands

Once HackotOS boots, you can interact with it through the shell (if available):

```bash
# List processes
ps

# Show memory info
free

# List files in current directory
ls

# Change directory
cd /path/to/dir

# Create a new process
./program &

# View system information
uname -a

# Check kernel version
version
```

### System Calls

User programs can invoke system calls:

```c
#include <syscall.h>

// Example: create a file
int fd = open("/file.txt", O_CREAT | O_RDWR);
write(fd, "Hello\n", 6);
close(fd);

// Example: fork a process
pid_t pid = fork();
if (pid == 0) {
    // Child process
    execve("/bin/sh", NULL, NULL);
}
```

### Debugging

Enable debug output:

```bash
# Recompile with debug symbols
make clean
CFLAGS=-g make all

# Run in QEMU with debugging
qemu-system-x86_64 -m 2G -cdrom hackotOS.iso -s -S &
gdb vmlinux
(gdb) target remote localhost:1234
(gdb) continue
```

---

## Project Structure

```
HackotOS/
├── README.md                    # This file
├── Makefile                     # Build configuration
├── config.h                     # Kernel configuration
├── docs/                        # Documentation
│   ├── architecture.md          # Detailed architecture
│   ├── syscalls.md              # System call reference
│   └── driver_development.md    # Driver development guide
├── include/                     # Kernel headers
│   ├── kernel/
│   │   ├── types.h
│   │   ├── printk.h
│   │   ├── process.h
│   │   ├── memory.h
│   │   └── interrupt.h
│   ├── arch/x86_64/
│   │   ├── cpu.h
│   │   ├── msr.h
│   │   ├── paging.h
│   │   └── gdt.h
│   └── drivers/
│       ├── uart.h
│       ├── keyboard.h
│       └── pit.h
├── kernel/                      # Kernel source
│   ├── main.c                   # Kernel entry point
│   ├── printk.c                 # Logging facility
│   ├── process.c                # Process management
│   ├── memory.c                 # Memory management
│   ├── interrupt.c              # Interrupt handling
│   ├── syscall.c                # System call dispatcher
│   └── sync.c                   # Synchronization primitives
├── arch/x86_64/                 # Architecture-specific code
│   ├── boot/
│   │   ├── boot.s               # Bootloader and entry
│   │   ├── gdt.c                # GDT setup
│   │   └── idt.c                # IDT setup
│   ├── mm/
│   │   ├── paging.c             # Paging implementation
│   │   ├── page_alloc.c         # Physical page allocator
│   │   └── heap.c               # Heap allocator
│   ├── cpu/
│   │   ├── cpu.c                # CPU initialization
│   │   └── switch.s             # Context switching
│   └── io/
│       ├── io.s                 # I/O port operations
│       └── io.h
├── drivers/                     # Device drivers
│   ├── uart/
│   │   ├── uart.c               # Serial port driver
│   │   └── uart.h
│   ├── keyboard/
│   │   ├── keyboard.c           # PS/2 keyboard driver
│   │   └── keyboard.h
│   ├── pit/
│   │   ├── pit.c                # Timer driver
│   │   └── pit.h
│   └── storage/
│       ├── ahci.c               # AHCI controller
│       └── ahci.h
├── fs/                          # File system
│   ├── vfs.c                    # Virtual file system
│   ├── vfs.h
│   ├── inode.c
│   └── inode.h
├── lib/                         # Standard library functions
│   ├── string.c                 # String operations
│   ├── memory.c                 # Memory operations
│   └── math.c                   # Math utilities
├── tools/                       # Build and utility tools
│   ├── grub.cfg                 # GRUB bootloader config
│   └── link.ld                  # Linker script
└── tests/                       # Unit and integration tests
    ├── test_memory.c
    ├── test_process.c
    └── Makefile
```

---

## Contributing

We welcome contributions! Please follow these guidelines:

### Before You Start

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature-name`
3. Read the [CONTRIBUTING.md](CONTRIBUTING.md) file

### Code Style

- Use K&R style with 4-space indentation
- Comment complex logic thoroughly
- Keep functions small and focused
- Use meaningful variable names

### Commit Messages

```
[component] Brief description

Detailed explanation of changes, if needed.
Fixes #issue_number
```

Example:
```
[memory] Improve page allocator performance

- Optimized bitmap search using bit manipulation
- Reduced allocation time from O(n) to O(log n)
- Fixes #42
```

### Testing

- Write tests for new functionality
- Ensure existing tests pass: `make test`
- Test on real hardware if possible

### Pull Request Process

1. Update documentation as needed
2. Ensure code compiles without warnings
3. Add tests for new features
4. Submit PR with clear description
5. Address review feedback

---

## Troubleshooting

### Build Issues

**Problem**: `gcc: error: x86_64-linux-gnu: No such file or directory`
- **Solution**: Install cross-compiler tools
  ```bash
  sudo apt-get install gcc-x86-64-linux-gnu binutils-x86-64-linux-gnu
  ```

**Problem**: `error: unknown type name 'size_t'`
- **Solution**: Ensure kernel headers are properly included
  ```bash
  #define _GNU_SOURCE
  #include <stddef.h>
  ```

### Runtime Issues

**Problem**: Kernel panics on boot
- **Solution**: Check kernel logs with QEMU
  ```bash
  make run 2>&1 | tee boot.log
  ```

**Problem**: QEMU won't start
- **Solution**: Verify QEMU installation and KVM support
  ```bash
  qemu-system-x86_64 --version
  grep -E '(vmx|svm)' /proc/cpuinfo  # Check for virtualization support
  ```

---

## Performance Optimization Tips

1. **Kernel Build**: Use `-O2` or `-O3` optimizations
   ```bash
   CFLAGS="-O3 -march=native" make all
   ```

2. **Enable KVM**: Use KVM acceleration in QEMU
   ```bash
   qemu-system-x86_64 -enable-kvm ...
   ```

3. **Memory Allocation**: Use efficient allocators for frequently allocated objects

4. **Scheduling**: Tune scheduling quantum for your workload

---

## Resources

### Documentation
- [Intel 64 and IA-32 Architectures Software Developer Manual](https://software.intel.com/en-us/articles/intel-sdm)
- [AMD64 Architecture Programmer's Manual](https://www.amd.com/en/technologies/amd64)
- [OSDev.org Wiki](https://wiki.osdev.org/)
- [Linux Kernel Documentation](https://www.kernel.org/doc/)

### Books
- "Operating Systems: Three Easy Pieces" by Remzi H. Arpaci-Dusseau
- "The Linux Kernel Module Programming Guide" by Peter Jay Salzman
- "Understanding the Linux Kernel" by Marco Cesati and Daniel P. Bovet

### Tools
- [QEMU Documentation](https://www.qemu.org/documentation/)
- [GDB Debugging Guide](https://sourceware.org/gdb/documentation/)
- [Binutils Documentation](https://sourceware.org/binutils/docs/)

---

## License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.

---

## Acknowledgments

- OSDev.org community for educational resources
- Intel and AMD for comprehensive documentation
- Contributors and testers

---

## Contact & Support

For questions, issues, or suggestions:

- **GitHub Issues**: [HackotOS/issues](https://github.com/BK9279/HackotOS/issues)
- **Discussions**: [HackotOS/discussions](https://github.com/BK9279/HackotOS/discussions)
- **Email**: Contact via GitHub profile

---

**Last Updated**: December 19, 2025
**Version**: 1.0.0
