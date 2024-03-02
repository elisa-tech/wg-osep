# **CheckList for Safety Claims on a Generic Linux System**

## Index

[Terms and Abbreviations](#Terms-and-Abbreviations)

[References](#References)

[Purpose of the document](#Purpose-of-the-document)

[Structure of the document](#Structure-of-the-document)

[Hardware Platform](#Hardware-Platform)

[Toolchain](#Toolchain)

[Linux Safety Checklist](#Linux-Safety-Checklist)

[License: CC BY-SA 4.0](#License-CC-BY-SA-40)

## **Terms and Abbreviations**

   See the matching section in *Interference Scenarios for an ARM64 Linux System*

## **References**

1. ***Interference Scenarios for an ARM64 Linux System***
2. ***Safety Requirements for a Generic Linux System***
3. [CC BY-SA 4.0 Deed | Attribution-ShareAlike 4.0 International | Creative Commons](https://creativecommons.org/licenses/by-sa/4.0/) - <https://creativecommons.org/licenses/by-sa/4.0/> License


## **Purpose of the document**
This checklist is meant to help with preparing a Linux-based safety solution.

The challenge with using Linux, is that it is a very complex, ever-evolving, crowd-sourced system, and it doesn't follow the traditional safety processes and workflows.

Furthermore, unlike typical off-the-shelf commercial components, it doesn't already come with a safety manual.

Even the architectural descriptions that can be found through documentation, books, courses, etc. are not created with safety in mind.

The risk is that one might overlook aspects that are paramount to safety, but typically deemed irrelevant for Linux users, and even to kernel developers they might appear as very niche.
The list here provided (as-is) does not claim to be sufficient, nor correct (Linux evolves rapidly), and it is strongly advised to confirm that the statements made are still correct and relevant.

However, it is an attempt to draw attention to aspects that might not be immediately obvious.
One immediate example of such an aspect is the fact that it is practically impossible to talk about safety without defining the reference hardware platform.

The entire document will mostly avoid entering into the details of both hardware implementation and Linux internals, beyond what is strictly required to justify each individual item of the checklist.
Similarly, it won't provide extensive justification of why something might be relevant, from a safety perspective.

All of the background justification, related to hardware (ARM64), Linux kernel architecture, and allocation of safety requirements, can be found in the documents mentioned in the [References](#References) section.

Last but not least, this document is not meant to provide solutions to the problems listed: solutions are often heavily contextualised, and must be designed to suit specific needs, in terms of use cases, safety requirements and system design.

The evaluation of the very same mitigation strategy can range from overkill to insufficient, depending on where/how it is applied.


## **Structure of the document**
- The next three sections are a brief - but necessary - premise about hardware, toolchains and other contexts: Linux is a code-base, but what gets deployed is a combination of hardware and binary code, and often it coexists with other contexts. These aspects cannot be overlooked.
- In the following section, the actual list unfolds.



## **Hardware Platform**

When a system is expected to meet certain safety requirements, these requirements are typically allocated to some high-level functionality, which is what constitutes the actual focus of interest in a safety context.

The system is then iteratively broken down into components, and safety requirements are percolated accordingly. At each iteration, the components having to support safety requirements may be further broken down, until the complexity of the components identified is deemed to be treatable.

At the very bottom of the stack of components lies the hardware. Hardware components may be qualified to a given safety level. Or not.

The level of safety qualification (or lack of) presented by the relevant hardware components can affect significantly the overall strategy used for the qualification of the entire system.

For example, by using chips that have been both designed and produced following qualified processes, implemented with qualified tools, it is possible to make assumptions about hardware safety.

Lacking the ability to make these assumptions, it might be necessary to introduce additional validation and/or redundancy, for compensating the uncertainty associated to the non-qualified hardware.

This is a preliminary safety check that is not strictly related to the use of Linux, but it might be worth mentioning in case one doesn't have much exposure to Functional Safety.


## **Toolchain**

Simply put, the toolchain is what converts the source code (mostly C, with a splash of assembly) into machine-executable binary object.

A minimalist toolchain is comprised of:
 * *compiler*, converting C sources into assembly, applying various sorts of optimisations, as specified through command line switches, passed when the tool is invoked
 * *assembler*, converting the assembly code into object code
 * *linker*, stitching together the object files produced by the assembler, and generating the static memory map

However, modern toolchains can muddle the picture by introducing certain extra features:
 * *code injection*, to implement certain functionality transparently (some might say behind the back), for example:
   * *Control Flow Integrity*: security hardening feature that attempts to detect if a function is not being entered or exited through the designated entry/exit points. This is intended to thwart attacks that rely on the re-arrangement of the execution of instructions, through the localisation and exploitation of fragments of binary code (gadgets) that are leveraged out-of-order (ROP/JOP: Return-Oriented-Programming / Jump-Oriented-Programming)
   * *Stack Canaries*: to detect the accidental (or malicious) overwriting of the call stack, the compiler can inject code at the preamble/epilogue of a function, placing an additional value on the call stack, when invoking a function, and verifying at the exit that said value was not overwritten.
 * *speculative inlining* (typically customisable through hints), where the compiler can decide if/when to follow hints present in the code about performing replacement of code, rather than invoking a function. But it can also decide that some "regular" function is worth being inlined, even when it was not explicitly requested.
 * *randomisation of structure layout*: as security measure, the compiler can alter the order of the fields of a structure, when it meets certain criteria (e.g. fields are exclusively pointers), because the only constraint impose by the C language on structure layout is that the address of a structure must coincide with the address of its first field. Altering the order of subsequent fields can thwart attempts of an attacker to alter specific memory locations, based on the expected content. The layout is randomised at compile time. However, this means that a bug overwriting part of a structure can expose different behaviour, depending on what layout was picked by the compiler for a certain build round.
 * *Link-Time-Optimisation*: the neat steps described in the toolchain flow above are altered in this way:
   * the compiler still generates assembly (as in the previous sequence)
   * the assembler no longer produces object code, but rather a bitstream, retaining more information from the previous phase
   * the linker starts by creating a preliminary binary file, and then it iteratively refactors code sequences, taking advantage of patterns that might emerge only when putting together multiple bitstream files. Eventually, a binary file emerges, but it can look very different, with functions potentially being optimised away, or even emerging, as by-product of linker-driven refactoring.

These examples show how converting source code into instructions that can actually be executed by a processor is not a trivial step, and there are plenty of opportunities for the sequence to diverge from the nominal behaviour, making a case for the need to qualify the toolchain as well.
And because of the variance in the output of the very same toolchain, based on the options selected and the associated values, it is thus necessary for the toolchain to be used with the same parameters that were used during its qualification.

It is therefore necessary, as preliminary step, to verify that the toolchain is qualified accordingly to safety requirements, and used accordingly to its qualification. Lacking this, then it becomes necessary to identify an adequate mitigation strategy.


## **Other Execution Contexts**

This section is particularly tied to the hardware configuration that one might have chosen (see reference architecture document and related interference).
On modern processors it is common to have additional hardware contexts, which expose low level functionality that is not available to the operating system.
From a very high level perspective, these modes bring two capability:
  * Hypervisor Mode: just as the operating system can run multiple applications in parallel, gating their access to certain features, isolating them from each other and abstracting the hardware, so the hypervisor can execute in parallel multiple operating systems, controlling their access to resources and abstracting the hardware. And, just as an application can do little to protect itself from the operating system, so an operating system can do very little to protect itself from the hypervisor.
  * Secure Mode: Sometimes it is necessary to provide strong guarantees that certain data (e.g cryptographic private keys or other secrets) is not accessible to the rest of the system (which might be compromised); this ensures also that extra efforts can be put into developing the code that *does* have access. A secure mode ensures that a system designer can always rely on certain ground truths.

The way these additional contexts are specified and implemented also grants them almost unfettered access to the kernel memory. The kernel might not even be aware of running on top of an hypervisor, or that a secure mode is mediating its access to certain memory.
Needless to say, these privileges also provide an unbounded ability to interfere.

**Can the kernel do anything to protect itself against this interference?** Possibly, but it would come very close to sprinkling checksums and redundancy all over. This would mean diverging significantly from a vanilla kernel *and* also introducing a noticeable overhead.

**What else can be done?** It depends. One might attempt to rely on significant redundancy and then deploy some form of safe monitor, responsible for comparing the behaviour of the paired systems. Alternatively, one could qualify these additional contexts that have the ability to interfere. Considering that usually these contexts are already subject to a very rigorous process, because they can do so much damage if buggy, it might be easier to qualify them than to qualify the Linux kernel. Furthermore, they tend to be more specialised pieces of software, implementing less functionality, with less code, than a full-blown operating system.
The choice lies with the system designer and integrator, however these listed here are probably the most common options available.
Regardless, though, one should assess if interference can come from these additional contexts, and how to deal with it.

## **Linux Safety Checklist**

The items presented below are not necessarily coupled with each other, but they are loosely ordered, starting from the most fundamental ones, with the intention of creating a "base of safety" for each new one. This doesn't prevent a design where mitigation takes a different route.
However, even if addressed through a fundamentally different approach, they should still be somehow addressed explicitly, even if only to say that they might not be applicable, or that they are handled otherwise.

The analysis covers safety woes, which need to be addressed when making any safety claims.

Safety woes belong primary to two classes:
* integrity - the safety-related functionality is in a corrupted state\
In its basic form, integrity mandates the detection of corruption, within an allotted time, so that some pre-defined reaction can happen in a timely way. Stricter integrity requirements would instead dictate that the corruption doesn't impair the functionality. 

* availability - the safety-related functionality becomes unavailable\
Having any actual availability requirement will have an impact in some form on the integrity requirements. High availability doesn't automatically imply that corruption cannot happen, but that it cannot cause interruption of a service. Restarting the affected component or having redundancy are examples of non-preventive mitigation, however, any mitigation needs to be evaluated against the specific use-case at hand.

Some of the cases considered might appear as double-failure, which is usually ignored in the context of functional safety, however they are instead cascading failures stemming from a single one, and therefore fully relevant to this analysis.


**Items List:**

* [Homogeneity of the test/validation/verification environment](#Homogeneity-of-the-test/validation/verification-environment)
* [Intra-kernel spatial interference](#Intra-kernel-spatial-interference)
* [Inter-process interference for resources and side effects](#Inter-process-interference-for-resources-and-side-effects)

### **Homogeneity of the test/validation/verification environment**

This list item is not specific to Linux architecture, but it cannot be overlooked, because doing so can compromise the usefulness of any further work.

Field testing and verification are bound to be part of any safety argumentation, because - ultimately - theories must be put to the test, and data must be gathered, to back whatever argumentation might have been put forward.

This cannot happen, until and unless both measurements and verification are aligned with the specific system being analysed. Discrepancy between actual target system and stand-ins can cause different timings and different paths of execution, hiding problems with the target configuration.

Some examples:
  * **Hardware architecture and revision**
    * Different architectures (e.g. ARM64 vs x86_64) are not implicitly equivalent, even when it comes to executing generic code, because it is mingled with, and exposed to, architecture specific routines. Furthermore, even if it is the same source code, it will translate to extremely different sets of machine instructions.
    * Even within the same architecture, there can be various revisions, with different number of registers, peripherals, timing, etc.
    * Even within the same revision, especially in the case or ARM processors, many hardware features are considered to be optional, or even customer-defined, or configurable. Caches, interrupt routing, presence of certain coprocessors, can (and will) be different, altering the flow of the execution.

  * **Discrete peripherals population**

    Variance in number, type or even make of peripherals can introduce uncontrolled changes in he behaviour of the system, due to difference in device drivers being loaded, event patterns, utilisation of system memory.
    
  * **Toolchain parameters**

    Some of the functionality mentioned above might be in different states of activation (certain options are not even binary; e.g. optimisation can have any of 4 values, typically)

  * **Kernel version**

    The fact that the instances of the OS being analysed are all Linux instances is a good start, but far from being sufficient. One needs to confirm that they are the same version, including additional patching that - in the case of distro vendors - might introduce unforeseen variance.

    The truly reliable way to ensure consistency is to compare the SHA signature of the HEAD of the code bases, in case one is attempting to reuse existing measurements from the field. But it's fundamentally a risky approach, and if the signatures are different, then one should prove that the code bases are equivalent, effectively analysing the different patching levels.

  * **Kernel configuration**

    Even after having confirmed that indeed, one is using exactly the same code base, the configuration options must be confirmed to be compatible. Once more, it's trivial to detect identity, but the complexity of proving equivalence between non-identical kernel configurations can escalate rapidly and soon become unmanageable.

  * **Applications**

    As if dealing with hardware configurations, toolchains setup and the kernel itself was not enough, applications too can interfere with measurements, in various ways and with different levels of subtlety:
    *  the presence of different applications in user-space, even if unrelated to safety, can alter the behaviour of the system, for example altering memory pressure, network traffic, cpu load
    *  the presence of monitoring applications can alter the power saving patterns; for example a particularly aggressive monitoring of system states can prevent the system from entering into lower power states, leaving which would have greater latency. Once the monitoring application is removed, the system might be outside of acceptable operating margins.


  * **Presence of monitoring equipment**

    Monitoring equipment - e.g. a serial port can have similar effects to the presence of a monitoring application:
    * detection of monitoring hardware (like a USB-to-serial FTDI adapter) can cause the loading of additional kernel modules
    * depending on the overall system configuration, some script might alter the logging level, causing heightened kernel activity and delays due to the higher traffic on the console.
    * should the logging exceed the log buffer capacity, it might even cause the notification of important events to be dropped
    * having to power additional Hardware peripherals might force power rails and clock sources to stay active, hiding issues related to their timely wakeup
    * even subtler issues: leaving monitoring options enabled but not active, like a disconnected serial port, might cause ground lines to be floating and generate spurious interrupts that, again, might hide latency problems due to the system being kept active.

### **Intra-kernel spatial interference**

Premise: the Linux kernel is monolithic, with the following design choices:

* one single memory map is shared across all cores that are executing in kernel mode at any given time
* most of the physical memory is mapped as writable in the kernel address space, in the **linear map**
  * this applies only to 64bit systems due to their vast address space
  * there are some exceptions, like the kernel text, which is not writable, but what is described in the following sections applies to the writable parts
* each and every function being executed in kernel mode has write access to any page of physical memory that is mapped as writable in kernel mode
* writable memory is used for various types of content:
  * data used by device drivers, irrespective of which safety requirements might be allocated to them
  * kernel metadata, including any sort of housekeeping data required by various subsystems, like scheduling, memory management, workqueues, locking, networking, filesystems, cryptographic functions, call stacks, etc.
  * processes management and execution: this covers both the memory used to host various components of user-space processes (code, constants, stack, heap, buffers) and the memory used to keep track of these allocations and their respective contents.

* kernel memory is allocated primarily through 3 different mechanisms (although there are few more additional corner cases, e.g. special memory used for storing permanent data, over SPI or I2C busses)
  * statically at linking time
  * dynamically at runtime from the linear map (and thus also physically contiguous, when allocated in size greater than one page)
  * dynamically at runtime from the virtual memory allocator

The side effects of such a design are that spatial interference can affect various classes of software components.
They are listed down below, and each one of them, if not somehow taken care of, is sufficient to tear down the associated safety claims.

The difficult aspect in dealing with these problems is that - at least for the purpose of detecting interference - it is not easy to assess if/when/where it might have happened.
To give an example, detecting that a task is not being executed can be relatively easy, by monitoring its execution. 

But judging if the data used by said task got corrupted might not be so easy. Of course introducing some level of hardening or redundancy would help, but it is unfeasible to alter the whole Linux kernel by sprinkling checksums all over it.

* The simplest (but not easier to handle!) form of interference is the direct overwrite of data which would be expected to support safety requirements.\
  In this simple case, an erratic behaviour alters incorrectly one or more system operating parameters.

  The following is a list of possible targets for direct corruption; it is intended that in this context one has to consider those which have to implement safety requirements:

  * stacks (e.g. used by kernel threads, processes performing syscalls, interrupts, exceptions)\
    Can be overwritten, trashing parameters being passed between functions
  
  * data used by device drivers, both for housekeeping and for their core functionality, including buffers\
  Can be altered in ways that are not noticeable (specific to each case).

  * data used by housekeeping components (e.g. scheduler, timers, memory managers, file systems, networking, pipes, etc.)

  * pages used for backing userspace processes\
    Can be any part of a process (code, rodata, stack, heap) and it can be accessed directly by the kernel, through the linear map; protections setup in the process page tables are completely irrelevant. A typical mistake is to assume that processes are somehow protected from the kernel, at least while they are not invoking syscalls. It couldn't be more wrong. Processes involved in safety scenarios are actually perhaps more exposed than non safe processes.\
    Why? because this type of interference acts on physical pages, and a victim process needs to have physical pages in use. Processes with safety requirements are likely to be marked as non swappable and have a higher residency in physical pages (ideally 100%), therefore becoming statistically more exposed.

* A somewhat less obvious (and thus more vicious) type of interference is the cascaded one: a first component interferes with a second one, which then misbehaves and affects a third, which was part of a safety scenario.\
  For example, one might be tempted to assume that, after pre-allocating all the necessary memory for all the safe processes, it would not be possible for memory allocators to interfere with them.\
  But all it takes is a corruption of the metadata used for housekeeping by the memory allocator (**which is a single error**), for having a risk that a page already assigned to a safe process might be handed out again to a different requestor.\
  And it would be incorrect to classify such an interference as double failure, because it's a cascaded interference.

### **Inter-process interference for resources and side effects**

* starting-stopping-restarting of processes\
As described in the previous point, each memory operation can have a risk associated with it, because it can cause perturbations in the state of a system which could otherwise be considered to be safe (with appropriate argumentation).

Introducing runtime perturbations that alter the allocation of memory pages will always introduce a risk that the underlying memory manager(s) might have been already exposed to interference, and thus could cascade it, not only to what is being started, but also to anything else that might be already running.

* use of cgroups\
Even putting aside for a moment the risks associated with the previous point, there is also a problem of managing memory pressure, while dealing with runtime allocations: assuming that an existing, running process is allowed to allocate additional memory at runtime, then various problems may arise, related to memory availability. For example, the system might be out of physical pages and have to kill/swap out some other process, introducing an unpredictable latency. This, in reality, is either a red herring or a red flag. Assuming that the system is running exclusively same-level safety applications, it should never reach a point where it goes out of memory. Hence the red herring. In case it does, the imaginary fish turns into very real fabric. Something went wrong in the system design.

However, in a mixed criticality context, one might be tempted to think that it would be ok and risk-free to use cgroups to reserve a certain amount of memory for safe applications, and then leave the non safe ones to fight for whatever might be left. Besides the fact that the underlying memory allocators are NOT partitioned based on cgroups, and the risk of interfering with existing safe processes persists, now there is also a problem of having enabled additional non safety qualified code (cgroups) that is very invasive for all the resources it can shape. In fact, the same reasoning can be applied to both CPU allocation and network bandwidth, which are 2 other major use cases for cgroups. There ain't no such thing as a free lunch.

* use of the security module / SELinux\
In a similar spirit, one might be tempted to rely on security features (e.g. SELinux) for implementing access control and building an argumentation that unsafe processes would not be allowed to perform certain actions that would interfere with safe processes. While this is not automatically wrong, one must account for its implications:
  * The Linux Security Module (LSM) is a very large collection of hooks scattered across the entire kernel, and for a very good reason: the security model used by linux is comprised by various frameworks and when it comes to performing access control, it is event-driven, meaning that the attempt to perform an action generates an event which triggers a validation process that can result in approval/rejection of the action. To do this, each and every action which would need to be validated must be instrumented. SELinux is one of the options available for performing the validation of the actions at runtime. It is chosen for this example simply because it is one of the most popular and probably the most complex one. SELinux can potentially tap into all the hooks available and also happens to perform aggressive memory operations in its attempt to cache and reuse the results of the evaluation of previous events. This very pervasive, memory intensive, non qualified component certainly doesn't help with safety argumentation, unless one can prove that whatever might be going on under the hood can be trusted sufficiently.


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

