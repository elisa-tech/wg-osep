
# **Using Linux in a Safe System**

## Index

[Terms and Abbreviations](#Terms-and-Abbreviations)

[References](#References)

[Disclaimer](#Disclaimer)

[Purpose of the document](#Purpose-of-the-document)

[Structure of the document](#Structure-of-the-document)

[Considerations](#Considerations)

[Examples of Safety-oriented System Design Concepts](#Examples-of-Safety-oriented-System-Design-Concepts)

[License: CC BY-SA 4.0](#License-CC-BY-SA-40)

## **Terms and Abbreviations**

   See the matching section in *Interference Scenarios for an ARM64 Linux System*

## **References**

1. ***Interference Scenarios for an ARM64 Linux System***
2. ***Safety Requirements for a Generic Linux System***
3. ***CheckList for Safety Claims on a Generic Linux System***
4. [CC BY-SA 4.0 Deed | Attribution-ShareAlike 4.0 International | Creative Commons](https://creativecommons.org/licenses/by-sa/4.0/) - <https://creativecommons.org/licenses/by-sa/4.0/> License


## **Disclaimer**
This is not intended to be a standalone replacement for a course on safety,
nor for an introduction to safety-compliant design.
It is meant to give a practical perspective on the elements that one has
learned from the theory about safety, and to show how effective engineering
considerations, methods and practices still apply.

## **Purpose of the document**
The considerations provided here are meant to help with designing a safe
system incorporating Linux.

There are many reasons why one might want to use Linux in a system, but its
safety is certainly not one of them - nor should it be.

Linux is neither conceived, nor evolved, to be safe. To be fair, it has
never made any claim to be safe, therefore it should not be expected to be safe.
Safety is not something that happens by accident. These considerations are
intended to help with designing a system that is safe **despite** using Linux.

All of the background justification, related to hardware (ARM64), Linux kernel
architecture, and allocation of safety requirements, can be found in the documents
mentioned in the [References](#References) section.

In particular, the present document can be seen as a counterpart to the document
**CheckList for Safety Claims on a Generic Linux System**.
While the Checklist is meant to help with detecting problems, the present document
wants to help with avoiding problems, upfront.

The same disclaimer made for the checklist is valid here too: this document is
not meant to provide full solutions to the problems listed.
Solutions are often heavily contextualised, and must be designed to suit specific
needs, in terms of use cases, safety requirements and system design.

The evaluation of the very same mitigation strategy can range from overkill to
insufficient, depending on where/how it is applied.


## **Structure of the document**
- The document starts with a section containing design considerations.
- The following section contains several examples of how to apply such
  considerations to the design of the Linux-bearing system.


## **Considerations**

**Premise:** some of the following considerations assume a fairly high level
of freedom in designing the overall system.
While one must acknowledge that in practice this freedom will not be always
available, due to design constraints, it is equally true that attempting to
cope with these constraints can lead to an even higher total cost of ownership.

* **Clearly define requirements toward system integrity and availability, upfront**

  Clarifying the purpose and the scope of the design goes a long way in defining criteria for
  identifying acceptable solutions.

  Strict requirements on availability can drastically alter the system design.

  For example, having a requirement to simply report failures within a maximum
  allotted time, is usually significantly simpler than having a stricter requirement
  to guarantee that a certain functionality will not be interrupted.

  Therefore, the intended use-case plays a major role in curbing potential
  system designs.

* **Linux is not intrinsically safe**

  While Linux has many remarkable properties, safety is not one of them, which means
  that it won't be possible to just allocate safety requirements to Linux, and expect
  them to be automatically satisfied. Quite the opposite, actually.

  Having to cope with safety requirements assigned to Linux will involve a price to
  be paid, in one form of another.

  The problem with said cost is that it might *also* affect negatively the goals
  that led to the selection of Linux, in the first place. For example, if Linux was
  selected because of its a rapid turnaround in fixing security bugs, selecting
  an approach that would introduce an lengthy qualification process would undermine
  the advantage of the rapid turnaround.

* **Aggressively placing safety requirements on anything but (components of) the Linux kernel**

  The natural consequence of the previous point is that one should explore - given a
  specific set of safety requirements for the system as a whole, if they could be
  satisfied through some component other than Linux (more on this later).

  There are several additional reasons for favouring an external component, besides
  what has been mentioned already:
  *  much greater probability for the component to be simpler (and thus easier to qualify)
  *  opportunity to have a better control over the component (e.g. use a software
     architecture that is better suited for safety, formalised processes, etc.)
  *  decouple release cycles of the Linux kernel from the safety component
  *  other non-technical reasons (e.g. different licensing)

  A specific application or use case, will have a broader set of requirements,
  like TCO, MTBF, weight, repairability, just to mention a few, and this can 
  and will affect the decision of what's the optimal compromise between
  allocating specific safety requirements on either Linux or other components.

## **Examples of Safety-oriented System Design Concepts**

**Premise:** applying the initial consideration from the previous sections, it is
necessary to define what is required at system level in terms of both availability
and integrity.

The simpler - and most common - case is to require detection of interference
affecting components that are recipient of safety requirements.

Interference affecting a component with safety requirements must be both detected
and reported, in a way that is compatible with the associated time requirements.
At this point, appropriate action(s) will be taken, in accordance with the
pre-established policy, to ensure adequate availability
(e.g. restarting, onlining of backup system, etc.)

As mentioned earlier, guaranteeing availability can be much more difficult
and/or costly.

* **Application Monitoring**

  Monitoring is a simple approach to detection of interference that affects
  component with safety requirement.
  
  It can save from extensive hardening.

  However three conditions must be met:
  1. It must be possible to express a check, in a way that can
     systematically, reliably and unequivocally produce a YES/NO verdict
     about the health parameter of interest.
  2. The check, while rigorous, must be sufficiently lightweight to be
     compatible with the timing requirements, so that it can ensure the
     reaction will happen within the time constraint expressed by the
     system at hand.
  3. The observation whether the condition is met (or not) can be performed from a
     context enjoying same or better safety qualification, provided that
     it is immune from the interference it is meant to detect.
     And the context is able to take whatever action might be dictated by the
     specific policy in place.

  Assuming that these two conditions are met (e.g. the system contains either a
  watchdog or an equivalent component -- more on this later) then the opportunistic
  approach is to identify an high-level activity that can be tied to the monitor.

  A very crude example of this validation could be to require that an application
  responsible for performing safety-relevant operations must ping a watchdog periodically.

  As long as the application pings the watchdog within the set deadline, the watchdog
  does not trigger any exceptional operation.

  The example is crude, but it illustrates how - for example - all the internal Linux
  components involved with booting, loading and executing the application might arguably
  not need to be subject to any safety requirement, provided that the final product of
  their operation can be monitored to ensure that it satisfies the required criteria.

  In this case one must accept, though, that safety claims are possible only for
  what is being monitored, either directly or indirectly.
  In the example, the WD can only directly detect that there has been some
  form of temporal interference. The cause might have been anything, as
  long as it resolved into a noticeable temporal interference, e.g. through
  cascading.

  If this is not acceptable, then additional requirements must be formulated,
  and additional monitoring (or other means of mitigation) must be deployed.

  It is fundamental to be careful in choosing  how/where to implement the 
  monitor and how to prime/ping it, to avoid involving large chunks of the 
  kernel architecture.

  Priming/pinging the watchdog becomes the mechanism for meeting (some) safety
  requirements. Involving component that could otherwise be avoided is
  likely to resul in additional unnecssary, expensive, tedious, error-prone
  work.

  It is usually far more effective to implement multi-stage monitoring
  mechanisms, to ensure that the core component remains simple and easy to qualify,
  while additional complexity of the validation can be achieved by chaining multiple
  monitoring processes, terminating at the bottom with a very simple one.

* **Watchdogs / Safety Islands / Other Exception Contexts**

  Continuing from the previous point, there are three typical design approaches,
  for implementing the monitoring.
  
  *  **Watchdog**

     A watchdog is a well known (simple) method for imposing mandatory behaviour on
     a system; the basic idea is to implement a "dead-man button": if a component
     is not producing a predefined stimuli, periodically, the watchdog will
     inderogably trigger a predefined event.
     Typically a hardware watchdog is a peripheral that enforces one of the
     following policies:
     *  NMI: the watchdog generates a Not Maskable Interrupt, which forces a core
        to execute a predefined interrupt handler. The handler runs with its own
        sanitsied context, and can be assumed to be in a sane/safe/secure state.
        However, it retains also the access to the system state.

        One could rely on the NMI interrupt service routine to take over and
        initiate a reset, or power down, or whatever is required.
     *  reset: it usually consists in the electrical state of the system being
        taken to its default values, paired with software re-initialisation.
     *  power down: power is cut off, at least from the components associated
        to safety requirements.

     This is fairly boilerplate information, but it is worth mentioning that
     safety requirements apply not only to software but to hardware as well.
     The Watchdog IP must be safety qualified, to be relied on.

     However, for the watchdog to support safety claims, the following must
     be proven:
     *  both design and implementation of the WD is compatible with the
        system safety requirements
     *  the WD must be adequately isolated from the rest of the system at
        physical level, so that the chance that physical anomalies
        (e.g. temperature, voltage, current) will reach it is in line with
        the safety requirements.
     *  the initialisation of the watchdog can be considered sufficiently safe
        (same or better safety level than the required one)
     *  it must be impossible for a single logical failure in the system to
        make it ineffective; this case covers:
        *  somehow a failure either disables or relaxes either the time
           constraint of watchdog or action-upon-expiring
        *  somehow a failure triggers one or more pings to the watchdog,
           when instead it should expire

     Note: it is safe to allow for a more restrictive re-configuring of the
     watchdog, because even if it happened accidentally, it would only speed
     up the expiration of the predefined timing period.

     Continuing with the healthy approach of avoiding the introduction of
     unnecessary dependency for the safety claims, the frugal designer will
     recognise that the last thing one wants to do is to rely on Linux to
     configure the watchdog, not only because of all the unnecessary safety
     requirements placed on Linux, but also because such an approach would
     leave a risk of having the system in an undefined state, potentially
     indefinitely, should the Linux boot fail before configuring successfully
     the watchdog.

     Obviously, *if* there were additional requirements directly involving
     the kernel itself, then the design should be adjusted accordingly.
     E.g. it might be necessary to ensure that some peripheral has started
     correctly, even before the related userspace might be ready.
     
     The astute designer will notice that, instead, another component is
     typically involved in booting Linux, the bootloader.

     A bootloader is usually a fairly small software component that is
     invoked directly by the boot rom (other cases are possible and
     discussed below). The bootloader, provided that it is at least as safe
     as the overall required safety level, could initialise the watchdog,
     and then pass the control to the Linux boot, confident that the watchdog
     will kick-in, should it not be pinged in time. Typically the bootloader
     runs in single thread mode, without the need for complex memory
     management. In fact the MMU is often disabled.
     
     And the responsibility of pinging can be assigned to the high-level
     safe application that constitutes the payload of the application.

     It is possible to lift safety requirements even from the bootloader,
     in case an external hardware watchdog is available and its initialisation
     can can be configured permanently e.g. by connecting adequate pull up / pull down.

     Choices are, as always, driven by overall system optimisation, design
     constraints and cost structures.

     But this "no holds barred" approach allows for extra flexibility,
     while trying to minimise the cost of integrating Linux, and, despite
     all, still fulfil safety requirements.

     As mentioned in the previous note, it is unnecessary to expect that
     the bootloader (or the hardwired pull up/down) settings will configure
     the same period which will be enforced past the boot phase, it is
     sufficient for the watchdog to enforce that each period being
     programmed shall not be larger than the previous one.

     The typical approach would be thus to configure through the pins a
     timeframe reasonably long to allow for kernel boot and spawning of the
     designated safe userspace process, which would then proceed with
     lowering the ping threshold according to its needs.

     Additional restrictions peppered along the booth sequence are possible,
     but not strictly required.

  * **Safety Island**

    "Safety Island" is a fancy name for referring to what is typically a
    special core, within a System on a Chip, that can be used as foundation
    for building safety claims. Characteristics are:
    *  Its HW design, implementation and validation are compatible with safety
       requirements. This means that it is intentionally isolated by other
       components in the same SoCi, to fulfil the independence requirements
       needed when decomposing safety requirements or when protecting safety
       workloads from the interference of other SW/HW elements.
    *  Usually is a microcontroller core
    *  Its operating parameters cannot be affected by other components in
       the system (e.g. the cores running Linux).
    *  It can either directly or indirectly affect the operating parameters
       of other components in the system (e.g. it can initiate a reset).
    *  Usually runs a safe RTOS that has a safety qualification compatible
       with the safety requirements for the overall system.
    *  Can act as complex, programmable, watchdog.
    *  Could even perform some simple safety related tasks, where it makes
       sense.

    Unsurprisingly, this sort of feature is not available in any randomly
    picked SoC designs, however one could still implement a sort of
     "poor man's safety island" by selecting a standalone component with
    similar characteristics (e.g. a safety rated microcontroller w/ PCB)
    and integrate it with the larger non-safety qualified system, through
    some shared interface (e.g. CAN, I2C or SPI) giving said microcontroller
    the ability to e.g. powercycle the main system, while also performing
    potentially sophisticated monitoring, that can evolve through complex
    state graphs, in lockstep with the primary system.
 
    
    This approach can be interesting as viable solution for converting
    an existing non-safe system design into a safety qualifiable one.

  * **Other Exception Context**
 
    As discussed in the "Checklist" document, on a modern SoC it is not
    uncommon to find out that the kernel is not the most privileged
    execution context, and that other contexts are capable of interfering
    with it in undetectable (by the kernel) ways. And the only solution
    is likely to be requiring that these contexts must be safety qualified.
    The hypervisor is a good example of such scenario.
    Considering architecture specific examples, one might think either at
    ARM TEE or Intel SGX.

    Well, this could be then leveraged to implement a Safety Monitor for
    the kernel. The simplest implementation could be the virtualisation
    of the SoC watchdog toward multiple operating systems, however it
    could go beyond that, and reach higher levels of complexity.

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
