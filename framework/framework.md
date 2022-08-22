# ELISA Safety Studies Framework

This document describes the following:

* [Background](#background): The motivation and objectives of the Linux- and safety-related studies undertaken and published by the ELISA project.
* [Framework](#framework): The conceptual framework and underlying assumptions that are intended to inform these studies, and guidance for individuals and organisations wishing to use the study results to inform their own projects
* [Process](#process): ELISA's policies and procedures relating to the creation, review and publication of study results by working groups

## Glossary

The following terms are used in this document with a specific meaning. Definitions with [IEC] link references correspond to the linked definitions, which are from the International Electrotechnical Commission's [glossary](https://std.iec.ch/terms/terms.nsf/welcome?OpenForm).

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
* **target system**: The *safety-related system* (specific or assumed) under consideration. For the purposes of this document, this is assumed to include a *component* incorporating software based on *Linux*

### ISO 26262 Definitions

* **architecture**: The representation of the structure of the *system* that allows identification of building blocks, their boundaries and interfaces, and includes the allocation of requirements to those building blocks.
* **assessment**: The examination of whether or not a *system* achieves the safety objectives.
* **audit**: The examination of an implemented process with regard to the process objectives.
* **baseline**: The version of an approved set of one or more work products that serves as a basis for change.
* **component**: A non-system level *element* that is logically or technically separable and is comprised of more than one hardware part or one or more software units. (TODO - Normalize this definition with the component above)
* **configuration data**: The data that is assigned during element build and that controls the element build process. (TODO - Reword to make more comprehensible)
* **element**: A system, hardware or software component, hardware part, or software unit.
* **error**: The discrepancy between a computed, observed or measured value or condition, and the true, specified or theoretically correct value or condition.
* **failure**: The termination of an intended behaviour of an element due to the manifestation of a fault.
* **fault**: An abnormal termination that can cause an *element* to fail.
* **fault tolerance**: The ability to deliver a specified functionality in the presence of one or more specified *faults*.
* **fault tolerant time interval**: The minimum time-span from the occurrence of a *fault* to the possible occurrence of a hazardous event, if the safety mechanisms are not activated. (TODO -- Is there a better or more generic terminology for this?)
* **inspection**: The examination of work products, following a formal procedure, to detect safety anomalies.
* **latent fault**: A fault whose presence is not detected by a safety mechanism nor perceived by the driver within an appropriate time.
* **malfunctioning behavior**: A *failure* or unintended behavior of an *element* with respect to its design intent.
* **modified condition/decision coverage**: (MC/DC) A code coverage metric that indicates the percentage of single condition outcomes that independently affect a decision's outcome.  In addition, it expects that all code blocks and execution paths have been tested.
* **quality management**: (QM) The coordinated activities to direct and control an organization with regards to quality.
* **random hardware failure**: A failure that can occur unpredictably during the lifetime of a hardware element and that follows a probability distribution.
* **regression strategy**: The strategy to verify that an implemented change did not affect the unchanged, existing and previously verified parts or properties of an *element*.
* **robust design**: A design that can function correctly in the presence of invalid inputs or stressful environmental conditions.
* **safety**: The absence of unreasonable risk.
* **safety anomaly**: Any conditions that deviate from expectations and that can lead to harm.
* **safety measure**: An activity or technical solution to avoid or control *systematic failures* and to detect or control *random hardware failures*, or mitigate their harmful effects
* **software unit**: An atomic-level software *component* of the software architecture that can be subjected to stand-alone testing.
* **system**: A set of *components* or subsystems that relates at least a sensor, a controller, and an actuator with one another. (TODO -- Normalize with the above definition)
* **systematic failure**: A *failure* related in a deterministic way to a certain cause, that can only be eliminated by a change of the design or of the manufacturing process, operational procedures, documentation or other relevant factors.
* **systematic fault**: A *fault* whose *failure* is manifested in a deterministic way that can only be prevented by applying process or design measures.
* **testing**: The process of planning, preparing, and operating or exercising an *element* to verify that it satisfies specified requirements, to detect *safety anomalies*, to validate that requirements are suitable in the given context and to create confidence in its behavior.
* **verification**: The determination whether or not an examined object meets its specified requirements.
* **work product**: The documentation resulting from one or more associated requirements of the development process.

# Background

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
* Why is 'traditional' approach from safety standards not feasible for Linux?
* Why might someone want to use Linux in safety?

## Objectives

TODO:

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
    - Terminology (e.g. this document's [glossary](#glossary))
    - Standard set of assumptions
      - If a study deviates from these, describe how
    - Standard disclaimers
    - Common document verification criteria

## User Inputs and Responsibilities

Organisations making use of ELISA's studies must necessarily provide the following inputs, as these are context-specific:

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

# Process

TODO:

* Define *ELISA’s* criteria and processes, *not* our recommendations for safety projects!
* Working principles / policies
    - e.g. Publish in Github, Accessible to all to comment on
* Document formats / requirements?
    - e.g. Written in English, written in form that people can read without special tools, text-based (to permit review in Github)

