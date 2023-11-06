# ELISA Safety Studies Framework

## Introduction

The ELISA project is organised into a number of working groups, each with a different focus. Some of these groups focus on a domain or *vertical* for safety applications involving Linux (e.g. Automotive), while others focus on cross-domain aspects or *horizontals* of the associated work (e.g. Safety Architecture).

Contributors to these working groups work together to discuss and investigate various topics related to their focus. The purpose of this document is to define a common framework for conducting this work and publishing the results, to provide a coherent frame of reference for consumers of the published material and a consistent set of working practices for contributors.

The following Capitalised Terms are used in this document with a specific meaning

* **Studies**: Collaborative activities undertaken by the ELISA working groups.
* **Study Results**: Outputs of *Studies* that are shared in a working group's GitHub repository
* **Study Framework**: The information and procedures in this document
* **Published Results**: *Study Results* that have been subject to the review and publication procedures described in [Process](#process), which are published in:
    - the `main` branch of a working group's GitHub repository, and/or
    - in a generated format (e.g. a PDF, or as HTML via GitHub Pages or on https://elisa.tech/)

Other common terms that have a specific meaning in the context of the *Studies* or *Study Results* are defined in the [Glossary](#glossary).

This document is organised into the following sections:

* [Glossary](#glossary): Definitions for a common set of safety- or Linux-related terms that have a specific meaning in the Studies and Study Results
* [Background](#background): The overall motivation and objectives of ELISA and the Studies
* [Framework](#framework): The conceptual framework and underlying assumptions that are intended to inform the Studies
* [Guidance](#guidance): Information for individuals and organisations wishing to use the Study Results to inform their own projects
* [Process](#process): ELISA's policies and procedures relating to the conduct of Studies and the creation, review and publication of Study Results by working groups

# Glossary

The following terms are used in the context of ELISA's Studies with a specific meaning. Definitions with [IEC] link references correspond to the linked definitions, which are from the International Electrotechnical Commission's [glossary](https://std.iec.ch/terms/terms.nsf/welcome?OpenForm).

* **build environment**: The *system* within which the *target software* is constructed using the *toolchain*
* **component**: Discrete part of a *system*. A component may consist of software, hardware, or both.
* **Linux**: Software source code and related materials provided by the [Linux kernel project](https://www.kernel.org/)
* **other risk reduction measure**: [[IEC]](https://std.iec.ch/terms/terms.nsf/0/CC0395B48CDB99C8C125771F002F5900?OpenDocument) Measure to reduce or mitigate risk that is separate and distinct from, and does not use, *safety-related systems*
* **safe state**: [[IEC]](https://std.iec.ch/terms/terms.nsf/0/EC58D06321CC96DEC125771F002F58E7?OpenDocument) State of the *target equipment* when safety is achieved
* **safety**: [[IEC]](https://std.iec.ch/terms/terms.nsf/0/F260F3CEB4EE58E3C125771F002F58E5?OpenDocument) Freedom from unacceptable risk. In this context, this principally means 'functional safety' as defined by [IEC 61508](https://std.iec.ch/terms/terms.nsf/0/D9612D92E129AA06C125771F002F58E6?OpenDocument) and [ISO 26262](https://www.iso.org/obp/ui/#iso:std:iso:26262:-1:ed-2:v1:en:term:3.67)
* **safety function**: [[IEC]](https://std.iec.ch/terms/terms.nsf/0/4820467B45827253C125771F002F5905?OpenDocument) Function of a *safety-related system* intended to achieve or maintain a *safe state*
* **safety integrity**: [[IEC]](https://std.iec.ch/terms/terms.nsf/0/5191972EF9725AA8C125771F002F5908?OpenDocument) Probability of a *safety-related system* satisfactorily performing the specified *safety functions* under all the stated conditions within a stated period of time
* **safety-related system**: [[IEC]](https://std.iec.ch/terms/terms.nsf/3385f156e728849bc1256e8c00278ad2/8f72c5473ed547a5c125771f002f58ff?OpenDocument) *System* that both:
    - implements the required *safety functions* necessary to achieve or maintain a *safe state* for the *equipment under control*
    - is intended to achieve, on its own or with other *safety-related systems* and *other risk reduction measures*, the necessary *safety integrity* for the required *safety functions*
* **system**: Combination of hardware and/or software that is intended to achieve a specific purpose. A system may contain subsystems, and may be part of a larger system.
* **target equipment**: [[IEC]](https://std.iec.ch/terms/terms.nsf/0/B9C576C31B4E488DC125771F002F58E9?OpenDocument) the equipment, machinery, apparatus or plant for which *safety* is to be achieved
* **target software**: The software components of the *safety-related system* that we are considering
* **target system**: The *safety-related system* (specific or assumed) under consideration. For the purposes of this document, this is assumed to include a *component* incorporating software based on *Linux* or used in conjunction with *Linux*
* **toolchain**: The software used to construct the *target software*, including its dependencies. Note that in some cases (e.g. gcc) projects providing parts of the toolchain may also provide components (e.g. glibc) that could form part of the *target software*, or be replaced by equivalent components (e.g. musl)

# Background

## ELISA's mission

ELISA's mission (as stated on project [website](https://elisa.tech/) is "to make it easier for companies to build and certify Linux-based safety-critical applications – systems whose failure could result in loss of human life, significant property damage or environmental damage".

To accomplish this, the [ELISA charter](https://elisa.tech/wp-content/uploads/sites/75/2020/08/elisa_technical_charter_082620.pdf) specified an intention to "define and maintain a common set of elements, processes and tools that can be incorporated into Linux-based, safety-critical systems amenable to safety certification."

TODO:

* What is the assumed context of what we’re trying to achieve?
    - Description of current development process for Linux?
    - Assuming that Linux is being used in the *context* of safety, but what does this mean?
      - Not necessarily relying on it *alone* for safety
    - Types of use in a safety context that we would explictly rule out?
      - e.g. hard real time

## Motivation

TODO:
* Why does ELISA only publish 'Studies' instead of making and maintaining a 'Safe Linux'?
* Why is the 'traditional' approach from safety standards not feasible for Linux?
* Why might someone want to use Linux in safety?

## Objectives

TODO:

* What are the Studies intended to achieve?
* What methods / approaches are we exploring / developing?
    - Inferred design approach
      - Describe system behaviour needed and demonstrate it through testing
      - Problem: system may do other things that you have not considered
        - How to ‘discover’ or account for these?
        - Or show that it cannot interact / interfere with parts we care about?
* What inputs are required from users / other collaborators
    - e.g. Expert domain review by getting Linux maintainers to review our arch model as part of submitting to kernel project docs
* What can we deliver as part of ELISA?
    - e.g. example safety analysis in the kernel
    - e.g. document describing methodologies
    - Distinguishing which elements of the kernel are *always* relevant
    - Common architectural model for kernel with interfaces
      - Could have an automatic check to detect when interface changes are being proposed as part of a change

# Framework

TODO:

* What do we need to define as a common framework to inform / frame all ELISA studies?
    - Terminology (e.g. this document's [Glossary](#glossary))
    - Standard set of assumptions
      - If a study deviates from these, it must specify how and why
    - Standard disclaimers
    - Common document verification criteria

# Guidance

## User Inputs and Responsibilities

Organisations making use of Published Results must necessarily provide the following inputs, as these are context-specific:

* Project definition
    - What is the purpose of the system using Linux and what role does Linux
      (or applications depending on Linux) play in that system?
* Project Context
    - System specification, including high-level system architecture
* Target standard for compliance
    - e.g. ISO 26262:2018
* Execution environment
    - Other system components or inputs that inform the execution environment
      for applications that depend on the Linux implementation
* Safety Concept
    - Safety goals for the target system, and strategies or solutions to be
      provided by other processes, or components of the target system

For ELISA's studies, we will document our assumptions about these inputs where relevant, to help organisations to understand how the results of the studies may inform their work or apply to their project context. However, organisations making use of the study results in this way are responsible for confirming their accuracy and suitability for the target context.

TODO:

* Further guidance about using / validating Study Results, or repeating Studies in the user's specific context

# Process

TODO:

* Define *ELISA’s* criteria and processes, *not* our recommendations for safety projects!
* Working principles / policies
    - e.g. Publish in Github, Accessible to all to comment on
* Document formats / requirements?
    - e.g. Written in English, written in form that people can read without special tools, text-based (to permit review in Github)

