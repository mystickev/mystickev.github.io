# MEMORY ACQUISITION IN WINDOWS AND LINUX

In this blog, we will be looking at state-of-the-art methods of memory acquisition from windows and Linux systems and comparing reliability based on the results of each approach. Memory forensics is a category of digital forensics whose primary focus is to investigate and analyze information stored/acquired from a system’s volatile memory (or RAM). 

A treasure of information that is held in the RAM includes and is not limited to:


* logged-in users
* Process objects and threads
* Network connections
* Open files
* A list of commands executed

To collect all this information, we will need to perform a memory dump. Note, as one extracts RAM, the ram area keeps expanding as it is still operational. Therefore, there should be enough disk storage to accommodate these changes. We will start by discussing how memory is managed by operating systems which gives us an insight into how memory forensics tools work under the hood.


### MEMORY MANAGEMENT OVERVIEW IN WINDOWS

Modern operating systems assign a running process to an abstract of RAM known as virtual memory. Virtual memory gives processes the impression that they run on continuous unlimited memory which is not true. In reality, this process may run on non-contiguous pages of physical memory or may have even been paged out to a physical memory unit i.e the hard drive

To manage these regions of memory, a Memory Management Unit (MMU) is utilized. Several advantages of using an MMU for the management of memory include:

* Providing each process with its protected view of system memory
* Monitor and restrict read/write operations by implementing privilege rules

When the physical memory is full, the operating system temporarily swaps out memory contents to the hard drive, freeing up space in physical memory. This process is known as **paging**.

When a thread attempts to access a swapped-out page, the  **MMU(Memory Management Unit)**  generates a page fault interrupt. The operating system responds to this interrupt by transferring the requested page back into physical memory. This allows the process to continue its execution without interruption.

The current location of a virtual address, whether it is in physical memory or on the hard disk, is indicated by the valid flag in the PDE (Page Directory Entry) and PTE (Page Table Entry) which are the data structures of a process. A value of 1 indicates that the virtual address is in physical memory, while a value of 0 indicates that it is on the hard disk.

Other flags within these data structures specify the status of the memory page. i.e, flags could indicate that the memory page is in transition or is a non-swappable address. These flags allow the operating system to make informed decisions about how to manage virtual memory.

Several data structures can be extracted from a memory image that provides insight into the current state of a machine. These structures include:


* EPROCESS - The EPROCESS block is a data structure in Windows operating systems that represent a process. It contains important information about a process that includes a process identifier, security context, and the virtual address space for that process. The EPROCESS block is stored in the system address space and is used to keep track of the state and information about all processes running on the system. Its also used by the operating system to manage and control the execution of processes i.e Allocating resources, managing memory, and enforcing security policies.
* ETHREAD - Threads are represented by an ETHREAD block on a windows operating system. This block, along with the structures it points to, exists in the system address space. The Client/Server Runtime Subsystem (CSRSS) also maintains a structure for each thread created in a Windows subsystem application. In addition, for threads that have called a User or Graphics Device Interface (GDI) function, the kernel-mode Windows subsystem (Win32k.sys) maintains a per-thread structure that is linked to the ETHREAD.
* KTHREAD - By examining the execution time, cycle time, and scheduling information within the KTHREAD, forensic analysts can determine the exact time frame during which a particular process or thread was executed. The **TEB** can provide important information about the execution of user-mode code, such as Windows Sockets, which can be valuable in determining the activities of a process or thread. Furthermore, the KTHREAD can provide information about the state of a process or thread at the time of a system event such as a crash or malicious activity.


#### MEMORY ACQUISITION

There are two main ways that memory acquisition can be divided from context to tooling, 

Hardware-Based and Software-Based. For this article, we will cover only software methods based on the operating system.

### WINDOWS

**DumpIt**

DumpIt is a minimal and free tool that is used to create a physical memory dump of Windows computers. It is compatible with both 32-bit (x86) and 64-bit (x64) systems. When used, DumpIt generates a raw memory dump file in the current directory without any further prompts, except for a confirmation question before starting the dump process. This feature makes it ideal for incident response scenarios, as it can easily be deployed on a USB drive for quick access.

After Downloading the tool, run it and accept with ‘Y’ on the prompt provided as shown below

![Dump-it](images/image1.png "image_tooltip")


Then leave the rest for the tool to take care of. Simple as that!

**WinpMem**

An open-source command line tool that is used on windows systems to acquire memory. 

Three different independent methods to create a memory dump. One method should always work even when faced with kernel-mode rootkits.

A read device interface is used instead of writing the image from the kernel like some other imagers. This tool supports copy across the network, hash, etc, as well as running analysis on the live system. The dumped image is often in raw form.

Here are some of the options the tool offers


![WinpMEM](images/image2.png "image_tooltip")


![winpmemdump](images/image3.png "image_tooltip")


LINUX

**AVML** 

Acquire Volatile Memory for Linux is a tool developed by Microsoft for capturing the contents of a Linux computer's physical memory. The key advantage of AVML is its ability to operate without the need for installation on the target system and its support for multiple memory dump sources, i.e


* /dev/crash 
* /proc/kcore
* /dev/mem.

**LIME/dd / dcfldd / dc3dd**

LiME, dc3dd, and dcfldd are command-line imaging tools used in digital forensics to create raw images of storage media. They work similarly to the original dd tool, which is available on most Unix and Linux systems. They all have an input file defined by if=/dev/&lt;device> and an output file defined by of=&lt;imagefile>. The forensic versions of dd have additional options for logging and hashing, as well as splitting the output into smaller segments for easier handling. The log=&lt;logfile> option can document the entire output process.

Check **Resources** section below on how to use the above tools.


### CONCLUSION

The tools that we have learned above have one thing in common, a small memory footprint and use during acquisition. They are the fastest when it comes to extracting memory from Windows and Linux systems. As a forensics investigator, it is important to understand the intricacies of memory acquisition and the steps involved.

RESOURCES

* [LiME](https://github.com/504ensicsLabs/LiME)

* [avml](https://github.com/microsoft/avml)

* [DD](https://hub.packtpub.com/extracting-data-physically-dd/)
