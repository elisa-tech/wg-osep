# OSEP approach to _Enabling Linux in Safety Applications_

Our starting position in this working group is that the resources provided by
the Linux project (and by associated open source projects, such as the Linux
Test Project) are not sufficient by themselves to support safety claims about
the use of Linux as part of an operating system in a safety-critical role. This
is for the simple reason that Linux has not been designed, implemented or
verified with this purpose in mind.

This means that _Enabling Linux in Safety Applications_ involves:

1. Understanding the typical characteristics of Linux-based systems from
   engineering first principles, in order to identify the risks,
2. Understanding how these risks apply to your specific Linux-based system, and
   documenting how that system intends to address these, and
3. Designing system architectures that can manage or mitigate those risks, with
   or without the involvement of Linux itself.

This document explains why we have adopted this position, and what resources
or collaboration opportunities ELISA can provide to support these objectives.

## Why can't ELISA make Linux safe?

ELISA is working towards improving the available resources to support
safety-related arguments involving Linux; for example by engaging directly with
the Linux project to promote good engineering practices, or by analysing and
documenting Linux features, or aspects of its architecture and design.

However, these are long-term efforts, which would require strong engagement from
the Linux community to change the established working practices and priorities
that inform kernel development. However, potential safety-related uses are a
priority for a small subset of the global developer community and product
creation organisations engaging in the ongoing development of Linux. This means
that it is very hard to persuade Linux kernel contributors and maintainers to
take on the additional burden of work involved in the engineering practices and
formalised processes expected by functional safety standards.

Furthermore, many of the software engineering processes described by the
standards require (or are predicated on the existence of) an organisation with
the authority to dictate the priorities of software development and require
developers to comply with formal processes. While this is the norm for a
commercial organisation developing a product, it does not reflect the
organisational model of an open source project such as Linux.

## Why not argue that Linux is already 'proven in use'?

While Linux is used to run much of the Internet, provides the basis for the
Android OS in millions of mobile phones and is used by 100% of the world's
super-computers, it was not developed with functional safety in mind, and
evidence of its use in other domains does not provide the kind of assurances
that are expected for safety applications.

Arguing that pre-existing software can be used in a specific safety application
based on evidence of its use for substantially the same purpose in other
products is described as a 'proven in use' argument.

This approach is described in some safety standards. For example, ISO 26262 part
8 section 14 notes that this approach may apply to "any product whose definition
and conditions of use are identical to or have a very high degree of commonality
with a product that is already released and in operation."

However, the criteria for applying this type of argument have very strict
requirements relating to failure rates from use of the element in comparable
products for the same purpose, based on incident report data collected over long
periods of time[^1].

Even if there were such data available for Linux, we could not make an argument
based on this unless we used the same version of the software, in the same kind
of system for the same purpose.

## Why not fork Linux and safety certify it?

It is theoretically possible to make an argument for using Linux in a
safety-critical role for a lower safety integrity level[^2] application, by
subjecting a specific version of the software to appropriate engineering process
measures e.g. defining requirements, documenting design, performing safety
analysis, developing tests, etc.

Provisions do exist in some safety standards[^3] for this approach to using
pre-existing software as part of a safety-related system. However:

- The time and effort involved in achieving this for Linux is always going to be
  very large, because of the size of its codebase (more than 40 million lines of
  code[^4]) and the very broad scope of its supported functionality, hardware
  and configurations.
- This means that such an approach would inevitably need to focus on a
  tightly-constrained subset of supported functionalities and hardware, to
  reduce the scope of the work required.
- This would typically also require the removal of all extraneous or undesired
  source code and configurations (and modification of the associated build files
  and tests), to avoid having to analyse these and their associated risks, and
  having to justify their inclusion in the software subset.
- Any deficiencies identified by analysis of the chosen Linux version and subset
  with safety implications would also need to be addressed, resulting in further
  modifications to the customised codebase. The need to fix any deficiencies
  that you find can be a disincentive, if this results in more work to do.
- The results of such an approach would only apply for the specific version of
  Linux analysed, for the specific use case(s) considered and for whatever
  constrained set of functions and target hardware was selected.
- As a consequence, any fixes, enhancements or new features added in the
  upstream Linux could not be automatically applied to the customised codebase,
  instead requiring detailed impact analysis to understand their potential risks
  and relevance.
- Maintaining such a codebase would be an ongoing commitment for the lifetime of
  any products making use of the special Linux

Furthermore, the ELISA project has neither the resources nor the mandate from
its member organisations to engage in this kind of effort.

## Why not show that Linux is safe by testing it thoroughly?

A common expectation is that safety can be achieved for software by simply doing
enough testing. However, from a functional safety perspective, testing is not
sufficient unless it is traced to corresponding safety claims, safety
requirements and safety scope of the code being tested and supported by a solid
verification strategy.

Fundamentally these means answering two questions:

**1. What needs to be tested, and why?**

Testing is only one of the verification and validation measures described in
safety standards. All such measures have a dependency on specified requirements
for the software, the system(s) within which it is intended to operate, and the
safety-related functions that the system(s) must provide.

Safety requirements necessarily derive from the system context, as well as the
intended purpose of the system and the risks associated with its use. A set of
requirements for an operating system with  safety applications in mind might be
possible, and might be easier to specify with a discrete set of functions.

The most fundamental requirements for an OS in these contexts are measures or
mechanisms to manage or detect interference between elements of the system, from
spatial (memory) and temporal (scheduling) perspectives, and data communication
between elements and with other systems.

However, precisely what OS functionality needs testing for any given system can
only be determined by reference to that system's safety requirements.

**2. How much testing is enough?**

TODO

[^1]: For example, in ISO 26262, for the _lowest_ category of safety integrity
      the _minimum_ evaluation period required is 12 million hours, and the
      number of observable failures must be lower than than 10^7/h.
[^2]: "In functional safety, safety integrity level (SIL) is defined as the
      relative level of risk-reduction provided by a safety instrumented
      function (SIF)"
      [_Wikipedia_](https://en.wikipedia.org/wiki/Safety_integrity_level)
[^3]: For example, the 'route 3S' compliance route described in clause 7.4.2.12
      of IEC 61508:2010 part 3, or the approach described in ISO/PAS 8926:2024
      with respect to the automotive safety standard ISO 26262.
[^4]: Source: [_Command Linux_](https://commandlinux.com/statistics/linux-kernel-contributors-lines-of-code-statistics/)
