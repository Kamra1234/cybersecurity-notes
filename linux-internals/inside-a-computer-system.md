Today, I finished the TryHackMe room **"Inside a Computer System"**, which teaches about the internal components of a computer system.

I found it very interesting.

These are the things I learned in this room, and also some things that I became curious to understand better after finishing it.

---

First, the basic infrastructure of computer systems is almost similar across all computers. The main differences are the power, speed, and quality of the components.

For example, every computer has important components like these:

If I compare them to the human , I can describe them like this:

### CPU: Brain

It performs calculations and executes instructions.

### RAM: Short-term memory

If we turn off the computer, all data stored in RAM will be lost.

### SSD/HDD: Long-term memory

Even if the computer is turned off, the stored data remains and is not deleted.

### Motherboard: Skeleton

It is where other components are connected so they can communicate with each other.

### GPU: Visual cortex

Mostly used by gamers. It is responsible for graphics and image processing.

### PSU: Oxygen when you wake up and breathe

It provides and regulates power for all computer components.

### Input/Output Devices: Eyes and ears

They allow information to move between humans and computers.

Mouse and keyboard send information to the computer, while monitors and speakers provide information back to us.

### Network Adapter: Hands and language

It allows computers to communicate with others.

There are two main types:

* Wired
* Wireless

---

After understanding these components, I started learning about the boot process, which was very interesting.

When a computer starts, after pressing the power button, electricity is delivered to all internal components so they can turn on and start working.

**Important note:**

This part is very important because if the power delivered to a component is higher than what it is designed to receive, it can damage the component.

---

Now the components are powered on, but they are not "awake" yet.

A system called **UEFI** (the older version is BIOS) takes responsibility for starting the system.

UEFI is a firmware stored on a chip on the motherboard.

UEFI runs a test called **POST (Power-On Self Test)** to check if everything is working correctly and if components like CPU and RAM are properly detected and configured.

---

The system needs to know where to load the operating system from.

UEFI/BIOS has a boot priority list.

For example:

1. SSD
2. USB
3. DVD

It checks these devices to find where the operating system exists.

Finally, a small program called the **Bootloader** runs and loads the operating system from the SSD into RAM.

---

A question may come up:

Why does the operating system need to be loaded into RAM? Why not run directly from SSD?

The answer is:

SSD is permanent storage, but it is slower.

RAM is temporary memory, but it is much faster.

The CPU constantly communicates with the operating system, and this communication needs to happen thousands of times per second.

If the CPU had to work directly with the SSD, the system would become much slower and would not work efficiently.

Even though SSDs are much faster than HDDs, they are still slower than RAM.

---

Now we reach the part where my curiosity increased.

I will talk about Unix-like operating systems.

After the boot process, the next important thing that runs is the **Kernel**.

The Kernel is the heart of the operating system.

The Kernel acts as a bridge between hardware and software.

The Linux Kernel is called **monolithic**.

What does the Kernel do?

The Kernel works as an interface.

For example, a running program says:

"I need 5 MB of memory for this variable."

The Kernel manages the memory and gives that program a specific part of memory.

The important point is that the program itself does not have direct access to all physical memory.

It only has access to the memory area that the Kernel assigned to it.

The Kernel has access to system resources.

---

Here we reach two important concepts:

## User Space

## Kernel Space

Simply:

**User Space** is where applications like Firefox, Bash, and other programs run.

**Kernel Space** is where the Kernel operates and where sensitive system operations happen.

---

Imagine a program wants to perform an action in User Space that only the Kernel can do.

For example, opening a file.

What should it do?

It must send an official request to the Kernel.

This is called:

**System Call (Syscall)**

Examples:

* close()
* exit()
* read()

Linux has around 380 system calls.

---

Calling syscalls directly is difficult.

It is similar to talking directly with a machine language.

Therefore, another layer is created above syscalls called:

**C Standard Library**

It provides easier functions like:

* fopen()
* execv()

These functions call syscalls internally.

---

Now we reach Bash, which can be a little confusing.

Let's start from the beginning.

When a Linux computer boots, after the Kernel starts, the first user-space process is created.

This process usually has:

**PID 1**

It is called:

**init (initialization)**

Its responsibility is to start other services one by one:

* Network services
* Database services
* Web servers
* Other system services

---

For example, imagine a building that needs to open in the morning:

First, someone opens the entrance door.

Then electricity is activated.

Then the elevator is turned on.

Then other employees are informed to start working.

This is similar to what init does.

---

Linux has different initialization systems:

### systemd

Newer and most commonly used today.

### SysVinit

Older initialization system.

And several others.

---

The parent of almost all processes is PID 1.

There are also concepts like parent processes, child processes, and orphan processes, but I will not discuss them here because it becomes too long.

---

We also have:

**systemctl**

It is used to send commands to systemd.

In other words, you normally do not communicate with systemd directly.

You use systemctl as an interface.

---

How do systemctl and systemd communicate?

There is a technology called:

**D-Bus**

It works like a communication channel between systemctl and systemd.

The Kernel is not directly involved in this communication.

However, we know that the Kernel is the foundation of the entire system, so eventually everything depends on the Kernel.

---

Now let's look at the real path of communication with the Kernel.

Example:

I type:

```bash
systemctl start ssh
```

The process is:

1. systemctl sends a message through the D-Bus channel to systemd.

2. systemd receives the message and decides what action needs to be performed.

3. systemd uses C Standard Library functions to perform its tasks.

4. The C Standard Library converts these operations into system calls.

5. The Linux Kernel executes the requested operation.

6. Finally, the Kernel communicates with the hardware.

---

Power Button
      |
      v
PSU
      |
      v
UEFI / BIOS
      |
      v
POST
      |
      v
Bootloader
      |
      v
Kernel
      |
      v
systemd
      |
      v
User Space

But if we want to be very precise:

systemctl and systemd communicate with each other through D-Bus, but if they need to communicate with the Kernel, they can do that separately using system calls.



it.karam
