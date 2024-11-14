# **Kernel Self Monitoring**

## Index
[References](#References)

[Purpose of the Document](#Purpose-of-the-Document)

[Structure of the Document](#Structure-of-the-Document)

[Introduction - Linux Kernel: practical safety problems](#Introduction---Linux-Kernel-practical-safety-problems)

[The concept - Safety-oriented self monitoring](#The-concept---Safety-oriented-self-monitoring)

[Constraints for a safety-oriented kernel self monitor](#Constraints-for-a-safety-oriented-kernel-self-monitor)

[Targets for self monitoring activity](#Targets-for-self-monitoring-activity)


## **References**

## **Purpose of the Document**
These are guidelines for safety-hardening certain aspects of the Linux kernel, through self monitoring.

They can act as a touchstone against candidate designs, to avoid known pitfalls. 

## **Structure of the document**
- The document presents a brief overview of what safety problems can be found while using Linux, and how they can be mitigated through self-monitoring.
- The next sections, instead, discuss more in detail typical pitfalls that must be avoided, to make the monitoring useful and what goals should be targeted.

## **Introduction - Linux Kernel: practical safety problems**
The Linux Kernel has many desirable features that make it a prime candidate for many use cases.

However, its lacks of both an intrinsic safety design and safety-oriented features make it challenging to use it as-it-is, for safety applications.

Therefore, there is a risk of having a Linux-based system to experience undetected internal interference, which might compromise the set of safety goals the system must support.

It is a customary mitigation to introduce system-level monitoring, in various forms.

To mention a couple of examples:
- Watchdogs, to detect abnormal delays either in processing events or performing actions.
- Output vetting, to detect abnormal actuator control signals produced by the system.

These external safety measures, though, are not always sufficient, because they might have limited ability to observe the internal status of the kernel, or it might be desirable to exert a tighter control over the evolution of its internal states.
This is where the concept of kernel self monitoring comes into the picture.


## **The concept - Safety-oriented self monitoring**
The previous section highlights the need for a reliable way of allocating at least some safety goals to the Linux Kernel itself, even if not the entire spectrum of system safety requirements. 

The following considerations are not specific to Linux, but nonetheless are relevant:
- Safety Requirements are “Contagious”
  Whatever mechanism, or combination of mechanisms, might be chosen to support safety goals, it must be proven that it is exempt from the form of interference that it is supposed to help detect/deflect.
  At the very least, it must be used in such a way that said interference cannot compromise its detection/deflection activity.
- From a safety perspective, not all types of interference are equal.
  For example, assuming that it’s acceptable to have interruptions of availability, as long as unsafe behavior is avoided, it can be classified as:
  - “Acceptable” interference:
    - Interference that causes misbehavior that can be detected/deflected through external mechanisms (as described in the previous section)
    - Interference that does not propagate to safety-relevant actuators.
  - “Non-acceptable” interference:
    - Interference that causes undetected interruption or degradation in the upholding of safety goals.

      E.g. a passive safety feature like an airbag goes silently offline.
    - Interference that causes outright dangerous malfunctions.

      E.g. a car self steers in the wrong lane.
- Different levels of safety hardening are possible, and similar results can be reached through different measures; design-time tradeoff and constraint play a major role in deciding how to approach the problem.

  E.g.:

  - A user-space process can send a message to a smart actuator, with built-in hardening, to at least detect corruption that the kernel might introduce:
    - Checksumming the message provides a degree of resilience against corruption.
    - Adding a timestamp / progressive message ID, and requiring communication to happen with a minimum periodicity can help with the detection of messages being either discarded or delivered late/out of order.

    However, this design requires an ad-hoc user mode device driver, and a peripheral that can handle the complex communication protocol.
  - Should the previous point be not feasible, for any reason, then the question remains of how this could be achieved by enhancing the kernel.

    Pulling together what discussed so far, it is possible to formulate a set of requirements for whatever component might step up to provide equivalent safety capability. And that is what the next section will discuss.

## **Constraints for a safety-oriented kernel self monitor**
As mentioned earlier, the self monitor needs to meet certain requirements, which are formulated from the perspective of hardening it to the same interference it is supposed to detect.
- The self monitor itself can incur into failure:
  - Due to interference from some other component.
  - Due to its own bugs.
  Such events must be detectable; e.g.by an additional, external component.

  Typically, it could be a watchdog of some kind:
  - An external HW watchdog.
  - An external core, running safety-qualified code (e.g. a safety island)
  - An external execution context running on the same system (e.g. an hypervisor)
- The failure analysis must consider that the meta-data used by the self-monitor is exposed to interference as well:
  - Mistakenly identifying a failure in the metadata as safety-relevant, when instead it is not, lowers the availability, but it is considered safe.
  - Failing to recognise a safety-relevant failure, instead, is not acceptable.
- Whatever mechanisms the self monitor might employ, they must be designed in a way that
  - does not fail silently: it is acceptable that they might fail, when exposed to the same type of interference, but this must happen:
    - in a noticeable way
    - within the maximum time allowed for detecting failures
  - does not introduce hazards of its own

The safety requirements for the safety monitor itself can be summarised with:
- use hardened metadata in some form, e.g.:
  - use redundancy, for non write-protected data
  - write-protect its data, to avoid its silent corruption
- avoid depending on the safe behaviour of other kernel subsystems and features, e.g.:
  - semaphores, scheduler, memory allocators
- avoid altering the system, e.g. through patching its code and/or data

## **Targets for kernel self monitoring activity**
As discussed, external means can be employed, to monitor the system for lack of functionality, however that only addresses one aspect of misbehaviour of a black-box system.

The other important aspect is emergence of the effects of internal interference, at the interface between the system and the rest of the world. In this case, those interfaces used to control actuators.
One can argue that, as long as external monitors check for loss of availability, internal self-interference is not a threat, if it doesn't manifest as unsafe controls for actuators.

The simplest form of self monitoring consists, thus, in sanitising output values, before they reach an actuator and result in unsafe actions. Of course, should the accctuator be capable of vetting the controls it receives, and signalling anomalies, it would not be necessary to perfom kernel self monitoring. But, in presence of "dumb" actuators, the kernel self monitoring assumes the role of safety net, provided it meets the conditions previously described.

The actual monitoring function to apply to each actuator control is heavily depending on said actuator, but they can be seen as:
- combinatory or without memory: the output at any given time is determined by the inputs at the very same time
- with memory: the output at any given moment is also affected by previous values, and vetting it becomes both more difficult and expensive.
