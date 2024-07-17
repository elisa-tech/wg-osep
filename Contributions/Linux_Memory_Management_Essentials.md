# **Linux Memory Management Essentials (Work In Progress)**

## Index

[Terms and Abbreviations](#Terms-and-Abbreviations)

[References](#References)

[Disclaimer](#Disclaimer)

[Purpose of the document](#Purpose-of-the-document)

[Structure of the document](#Structure-of-the-document)

[Kernel-space memory allocations](Kernel-space-memory-allocations)

[User-space memory allocations](User-space-memory-allocations)

[License: CC BY-SA 4.0](#License-CC-BY-SA-40)


## **Terms and Abbreviations**
Plese refer to the Linux Kernel documentation.
   

## **References**

1. [Linux Kernel website](https://www.kernel.org) - <https://www.kernel.org>
2. ***Interference Scenarios for an ARM64 Linux System***
3. [CC BY-SA 4.0 Deed | Attribution-ShareAlike 4.0 International | Creative Commons](https://creativecommons.org/licenses/by-sa/4.0/) - <https://creativecommons.org/licenses/by-sa/4.0/> License


## **Disclaimer**
* This document is not intended to be a replacement for understanding the memory management of the Linux Kernel,
  nor it attempts to be an exhaustive analysis of safety implications.
* Because of the very volatile nature of the code within the Linux Kernel, each of the statements made
  below should not be taken at face value, but rather verified, for any Linux kernel version following
  the one used while writing the document (6.9).
* When referring to specific HW features, the document refers to the ARM64 architecture.

## **Purpose of the document**
This document aims to provide an holistic view of what happens in Linux memory management, so that
one is at least aware of certain features and can use this document as a jumping pad toward more detailed documentation.
Or even to the code base.

## **Structure of the document**
The document is divided in two parts, based on the destinatary of the memory allocations discussed: kernel-space or user-space.
Individual points are numbered for ease of reference, but the numbering is not meant to represent any sequence.

## **Memory management in Linux**

### **Kernel-space memory allocations**

#### **Directly Verifiable Assertions**
The following section presents a set of statements that can be objectively verified e.g. by inspecting the sources.

1. unlike processes memory, kernel memory pages are not swapped, nor dropped silently by the kernel itself,
   although an hypervisor will do to a VM what the kernel does to a process (but this is beyond the control of the kernel)
2. the kernel context (usually EL1 on ARM64) uses one single memory map (page tables) across all the cores
   executing in kernel mode
3. on 64 bit systems (e.g. ARM64 and x86_64), usually almost all physical memory is mapped in a
   (semi)contiguous (there can be holes) range. Memory within this range is both virtually and physically contiguous.
4. physically contiguous memory is treated as a scarce resource, and typically is not provided to userspace,
   unless it explicitly asks for it (e.g. for implementing some DMA buffer)
5. the kernel can access userspace memory in three ways:
   1. through the userspace mapping 
      1. this type of access is limited to few functions, like copy_to_user()/copy_from_user() and put_user()/get_user()
      2. outside the execution of the functions mentioned, this type of access is not possible, because the userspace mappings are made available only while executing such functions
      3. the userspace memory map can implement HW protections against being misused by the kernel
      4. the kernel is able to access process pages in the same sequence and with the same mappings as the process does
   2. through a memory buffer (e.g. a memory area where the kernel regularly needs to perform large amount of read/write operations, like a network buffer)
      1. the memory used by a buffer is well defined and delimited, rather than a generic area, and it is specifically reserved for this purpose
      2. the kernel uses own mappings in EL1, while the user space uses the EL0 ones
      3. very often the kernel doesn't actually access this area directly, but it rather configures a DMA controller to do the transfers, directly to the physical memory.
      4. misconfiguration of these peripherals that can access physical memory directly is a potential problem - a form of mitigation relies on using IOMMUs: hardware components that the kernel can configure as a firewall on the phisical memory bus, to limit what memory is accessible to each memory bus master device.
   3. through the EL1 mappings + linear map
      1. not the intended way for the kernel to access process context, because the intended way is one of the two previously described
      2. attempting to use EL1 mappings would be not very useful, both for legitimate purposes and even for malicious ones:
         1. the sequence of pages mapped in the userspace process is not known, and it can change continuously, as the memory management does its job of optimising page allocation to running processes
         2. some process pages might even be "missing", because they have been either swapped out or dropped (see previous point)
         3. even for an attacker, in a security scenario, because most likely the attacker would need to access user space pages sequentially, or anyway through user mappings (this is why EL1 mappings are allowed to have access to pages containing EL0 code/data: the security risk is relatively low)
         4. it bypasses any protection that the process might employ through its own mapping, which, even for legitimate kernel operations on userspace, would be less safe
6. Assertions about page "mobility", from different perspectives:
   1. employment of physical pages - observing a physical memory page (e.g. a 4kB chunk aligned on a 4kB boundary) and how it is employed: what sort of content it might host, over time.
      1. certain physical pages are put to use for a specific purpose, which is (almost) immutable, for the entire duration of the execution of the kernel:
         1. kernel code from the monolithic portion (except for pages containing kernel init code, which are released after kernel init is completed)
         2. kernel statically allocated data from the monolithic portion (except for pages containing kernel init data, which are released after kernel init is completed)
         3. some kernel dynamically allocated data, used by the kernel itself, and never released, due to the nature of its use (e.g. object caches, persistent buffers, etc.)
         4. memory used for loadable kernel modules (code, data) is tied to the permanence of the module in memory - this is typically stable through the entire duration of the execution. Some exceptions are modules loaded/unloaded as a consequence of certain peripherals (dis)appearing, e.g. USB ones.
      2. other physical pages (most, actually, in a typical system) are available for runtime allocation/release cycles of multiple users:
         1. transient kernel linear memory allocations (kmalloc / get_free_pages)
         2. transient kernel virtually linear memory allocations (vmalloc for kernel address space)
         3. user-space memory allocations (they are always virtually linear), which are by default transient and exposed to repurposing proactively done by the kernel (see below)
   2. transitioning of a logical page - given certain context and content, where it might be located in memory over time - and if it might be even discarded.
      1. the kernel doesn't spontaneously repurpose the utilisation of its own physical pages; therefore it is possible to assume that the logical content of kernel allocations will reamin tied to the associated physical pages, as long as it is not intentionally altered (or subject to interference)
         1. metadata memory used by the kernel for housekeeping purposes related to processes is included in this category; examples: task/cred structures, vmas, maple leaves, process page tables.
      2. memory used by the kernel for the actual userspace processes: the content of this logical page determines its life cycle and expectancy: certain content such as code or constants can be "re-constructed" by relaoding it from file (code pages are likely to necessitate undergoing re-linking), so the actual logical content might disappear, over time. Other pages, on the other hand, are meant to hold non-re-constructible content, such as stack, heap, and variable data. These pages can, at most, be swapped out, and loaded back later on, but they cannot be simply dropped.
      3. page cache: it is a collection of memory pages containing data read from files, over time; e.g. code or initialised data from a file that was accessed recently; in some cases the page might never have been used, but it was loaded as part of the readahaead optimisation. The life expectancy of these logical pages is heavily affected by how many processes might keep accesing them and the level of memory starvation of the system caused by other processes, with some additional complexity layered on top of this, by the use of containers.
   3. The kernel utilises various optimisations that are meant to take advantage of hardware features, such as multi-stage caching, and also to cope with different memory architectures (like Non Unifor Memory Architecture - NUMA). The main goals are:
      1. avoid having to propagate too frequently write operations through the layers of HW cache, which is caused by pages being evicted from the cache, due to memory pressure
      2. avoid having multiple cpus writing to the same page, in a NUMA system, where only one cpu has direct write access to that memory page, because it would cause cache invalidation
      Therefore, the kernel tends to:
      1. reuse as much as possible a certain page that has just been freed (so called hot page, since it is stll presumably present in the HW cache)
      2. keep for each core a stash of memory pages readily available (which prevents other cores from accessing said pages and introducing additional cache-flush operations)
   4. The MMU, involved in performing address translations, acts also as a bus master, and performs read operations whenever it needs to do an address translation that is not already present in its own local translation cache (TLB - Translation Lookaside Buffer). Having to perform too many of such address translations (page walks) can constitute a significant performance penalty. The TLB is not very large, and accessing lots of different memory addresses that do not belong to the same translation entry can cause severe performance degradation. This is why the kernel actually keeps most of the memory mapped in the linear map, to take advantage of a feature present in many processors, that allows the mapping of large chunks of physical memory (e.g 2MB) as a single entry (or few ones). The kernel code, for example, is kept compact to maximise the efficiency of the fetching operations. For the similar reasons, the linear mapping allows to have memory already mapped, without a need for creating those mappings on the fly.
   5. For what concerns allocation from the linear map (kmalloc / get_free_pages), the kernel attempts to keep the free pages as much continuous as possible, avoiding fragmentation of the free areas.
      1. this is implemented through the concept of the "buddy allocator", meaning that whenever a certain amount of linear memory is requested (either sub-page or multi-page size), it always tries to obtain it from the smallest free slot available, only breaking larger free slots when no alternatives are possible.
      2. the kernel also keeps ready a certain amount of pre-diced memory allocations, to avoid incurring the penalty of having to look for some free memory as a consequence of an allocation request.
      3. folios are structures introduced to simplify the management of what has been traditionally called compound pages: a compound page represents a group of contiguous pages that is treated as a single logical unit. Folios could eventually support the use of optimisations provided by certain pages (e.g. ARM64 allows the use of a single page table entry to represent 16 pages, as long as they are physically contiguous and aligned to a 16 pages boundary, through the "contiguous bit" flag in the page table). This can be useful e.g. when keeping in the page cache a chunk of data from a file, should the memory be released, it could result in releasing several physically contiguous pages, instead of scattered ones.
   6. whenever possible, allocations happen through caches, which means that said caches must be re-filled, whenever they hit a low watermark, and this re-filling can happen in two ways:
      1. through recycling memory as it gets freed: for example in case a core is running short of pages in its own local queue, it might "capture" a page that it is freeing.
      2. through a dedicated thread that can asynchronously dice larger order pages into smaller portions that are then placed into caches in need to be refilled
   7. the kernel can also employ an Out Of Memory Killer feature, that is invoked in extreme cases, when all the existing stashes of memory have been depleted: in this case the killer will pick a user space process and just evict it, releasing all the resources it had allocated. It's far from desirable, but it's a method sometimes employed.
   8. freeing of memory pages also happens in a deferred way, through separate threads, so that there is no overhead on the freer, in updating the metadata associated with the memory that has just been released.
   9. All of the mechanisms described above for memory management are not only memory providers, but memory users as well, because they rely on metadata that cannot be pre-allocated and must be adjusted accordingly to the memory transactions happening, as the system evolves over time; therefore they also consume memory, generating an overhead, and being exposed themselves to interference.
   10. The Linux kernel provides means to limit certain requests a process might present; for example with cgroups it is possible to create logical memory "bubbles" that cannot grow beyond a set size, and associate processes to them, that share the collective limit within the bubble. But this does not much toward separating how the kernel might use the underlying memory, besides setting the constraint as described.
      
#### **Safety-Oriented consideration**
The following considerations are of a more deductive nature.

1. Because of the way pages, fractions and multiples of them are allocated, freed, cached, recovered, there is a complex interaction between system components at various layers.
2. Even using cgroups, it is not possible to segregate interaction at the low level between components with differnet level of safety qualification (e.g. a QM container can and most likely will affect the recirculation of pages related to an ASIL one)
3. Because of the nature of memory management, it must be expected that memory management mechanisms will  interfere with safe processes, either due to a bug or due to the interference toward the metadata they rely on. For example, the memory management might hand over to a requesting entity a memory page that is currently already in use either by a device driver or by a userspace process playing a role in a safety use case.
4. Still due to te complex interaction between processes, kernel drivers and other kernel code, it is practically impossible to qualify the kernel as safe through positive testing alone, because it is impossible to validate all the possible combinations, and it is equally impossible to assess the overall test coverage and the risk associated with not reaching 100%. The only reliable way to test is to use negative testing (simulating a certain type of interference) and confirming that the system response is consistent with expectations (e.g detect the interference, in case of ASILB requirements). And even then, the only credible claim that can be made is that, given the simulated type of interference, on the typology of target employed, the reaction is aligned with the requirements. Other types of targets will require further ad-hoc negative testing.
5. Linux Kernel mechanisms like SELinux and cgroups/containers do not offer any protection against interference originating from the kernel itself.
6. The Linux Kernel must be assumed to be QM, unless specific subsystems are qualified through both positive and negative testing.
7. Even after achieving the ability to make certain claims about the Kernel integrity (or detection of its loss), this doesn't guarrantees system availability: knowing that an interference has occurred doesn't help with ensuring a certain level of system availability.

### **User-space memory allocations**

#### **Assertions**
The following section presents a set of statements that can be objectively verified e.g. by inspecting the sources.

1. given a user-space logical memory page, at a certain moment, it must not be assumed to have a corresponding
   backing physical memory pages; it means that a process might have had some of its pages dropped and/or
   swapped out to disk, or perhaps not even ever loaded.
2. it is not possible to make many assumptions about the state of the logical content of a process pages.
   Perhaps one of the few statments that can be made is that the logical page containing code being executed
   in a certain moment has also a physical backing, while it is being executed.
   It might be possible to introduce some additional certainties, for example by pinning down some memory allocations
   by marking the process as non-swappable. The presence of caching and other
   optimisations like the zero-page make it very difficult to be assertive beyond this point.
3. the management of memory pages associated with a process is handled through the process memory
   map, which consists of several virtual memory areas, representing address ranges within the
   process address space, that are put to some use.
   They come in two flavors:
   1. anonymous mappings:
      1. process heap, stack
      2. zero-initialised variables
      3. vDSO / vvar (code and data provided by the kernel, to optimise the execution of frequently performed operations, like user space reading the time of day)
   2. file-backed mappings:
      1. constants
      2. the main executable associated with the process
      3. the dynamic linker (optional but typically used for ELF files)
      4. linked libraries (e.g. glibc)
4. when the kernel starts a process from scratch (like with init), it sets up mappings for all the virtual memory areas required to get it started,
   but it doesnt actually allocate almost any memory: once the process is scheduled for execution, it will eventually be run 
   for the first time, and as soon as the various areas are accessed, they will trigger page fault exceptions.
      1. when an address is accessed for the first time, it might require that a memory page is allocated to host whatever
         the associated content might be, but it is possible, especially when the process has just been started, that
         even the associated page in he page table is missing.
      2. based on both availability of free pages and type of content asosciated, the access might cause the process
         to sleep; exaples:
         1. no free pages are available (unusual but possible) and the kernel will have to try to obtain one, in one of
            the ways described above
         2. the content needs to be loaded from a file, and the operation is blocking, because the retrieval process is much slower.
      3. the only exception, where pages are specifically allocated before the process is started, is the beginning of the stack; here process arguments and context are stored. 
      Either way, it is difficult to know if/when a process is not going to generate any more faults, and it is very much not deterministic.
5. creations of threads for an existing process reuses the process memory map almost entirely, however an additional stack is
   allocated for each new thread, picking an address range between the live ends of heap and primary stack.
   The sizes of these additional stacks are defined at creation time, but allocation of physical pages happens on-demand.
6. as also decribed in the previous points, the kernel uses various optimisations for dealing with processes on-demand mapping:
   1. a physical page is allocated and mapped to a process only when the process accesses it, otherwise it might not be present:
      1. file backed pages are allocated/mapped only when read/written to
      2. anonymous pages are allocated only when written to
   2. zero page: when a page is known to be empty, it is not reserved and mapped; instead,
      the kernel has one specific (dummy) memory page that is mapped tothe process as read-only
   3. shared libraries:
      when the same library pages are mapped by multiple processes, the library physical pages
      are mapped as read-only into each process address space
   4. copy-on-write:
      1. when a library has own data, this is initially mapped as read-only and shared;
         only when written to, then a separate physical page is reserved for each writing process (threads sahre the same)
      2. same happens for data pages that were treated as zero-page, but then are written to.
   5. folios (see also kernel section): data structures that try to better abstract compound pages and *might* also be used
      to represent optimised contiguous pages on ARM64 (instead of mapping 16 entries, it is possible
      to map a 16-pages chunk of physically contiguous memory, that is also aligned to a 16 pages
      boundary, reducing TLB use).
      1. A folio acts as intermediary between vma and lower level memory management
      2. it might pre-allocate/map more pages than explicitly requested
7. read-ahead: when asked to fetch data from disk, the kernel might attempt to optimise the operation,
   reading more pages than requested, under the assumption that more requests might be coming soon
 

#### **Safety-Oriented consideration**
The following considerations are of a more deductive nature.

1. a process that is supposed to support safety requirements should  not have pages swapped out / dropped / missing,
   because this would introduce:
   1. uncertainty in the timing required to recover the content, if not immediately available
   2. additional risk, involving the userspace paging mechanisms in the fulfilling of the safety requirements
   3. additional dependency on runtime linking, in case the process requires it, and code pages have been
      discarded - reloading them from disk will not be sufficient
2. The optimisations made by the kernel in providing physical backing to process memory make it very
   questionable if it can be assessed when a (part of) a process memory content is actually present in the
   system physical memory.
3. by default, it is to be expected that a process will be exposed to various types of interference from the kernel:
   1. some of a more bening nature, like dropping of pages, or not allocation of not-yet-used one
   2. some limited in extent, but hard or even practicaly impossible to detect, like a rogue write to process physical memory
   3. some of systemic nature, like some form of use-after free, where a process page is accidentally in use also by another component
   4. some of indirect nature, like for example when the page table of the process address space is somehow corrupted
4. again, because of the extremely complex nature of the system, positive testing is not sufficient, but it needs to
   be paired also with negative testing, proving that it is possible to cope with interference and detect it, somehow.
5. the same considerations made about integrity vs. avaialbility for the kernel are valid here too: detecting
   interference doesn't help with keeping it under a certain threshold, and due to the complexity of the system,
   it is not possible to estimate the risk reliably.
6. a single-thread process can interfere with itself, since typically most of its data is writable
7. when dealing with a multi-threaded process, besides simple self interference, one must also consider cross-thread
   interference, where each thread can corrupt not only its own stack, but also the stack of every other process.
   


## **License: CC BY-SA 4.0**

### **DEED**
### **Attribution-ShareAlike 4.0 International**

Full License text: <https://creativecommons.org/licenses/by-sa/4.0/>

**You are free to:**

* **Share** — copy and redistribute the material in any medium or format for any purpose, even commercially.

* **Adapt** — remix, transform, and build upon the material for any purpose, even commercially.

The licensor cannot revoke these freedoms as long as you follow the license terms.

**Under the following terms:**

* **Attribution** — You must give appropriate credit , provide a link to the license, and indicate if changes were made . You may do so in any reasonable manner, but not in any way that suggests the licensor endorses you or your use.

* **ShareAlike** — If you remix, transform, or build upon the material, you must distribute your contributions under the same license as the original.

* **No additional restrictions** — You may not apply legal terms or technological measures that legally restrict others from doing anything the license permits.

**Notices:**

You do not have to comply with the license for elements of the material in the public domain or where your use is permitted by an applicable exception or limitation .
No warranties are given. The license may not give you all of the permissions necessary for your intended use. For example, other rights such as publicity, privacy, or moral rights may limit how you use the material.
