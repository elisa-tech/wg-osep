# Safety analysis approach

## Objectives

OSEP is documenting and apply a safety analysis process based on [STPA](https://psas.scripts.mit.edu/home/get_file.php?name=STPA_handbook.pdf) that is suitable for use with Linux and other open source software.

Our objective is to specify a system context and an example set of safety goals for a safety-related system involving the Linux kernel, in order to enable the safety analysis and specification of a set of safety responsibilities that we may assign to the kernel in that context (and possibly other contexts), at a useful level of detail.

* By *system context* we mean either a concrete system design, or an abstraction representing a class of system designs.
* By *safety goals*, we mean a set of system-level criteria that must be satisfied in order to avoid specific negative outcomes or consequences.
* By *safety responsibilities*, we mean the behaviour or properties that are required to avoid violating the safety goals for the given system context. This may involve cooperation with other safety mechanisms, which are required to operate when it is not possible to avoid violating a high level safety goal.

In ISO 26262 terminology, this is equivalent to defining the assumptions of use (AoU) for Linux (or any FOSS component, or integration of components) as a Safety Element out of Context (SEooC).

You can find more information about this approach and its intended role as part of a wider engineering process in the [*Refining the RAFIA approach*](https://sched.co/za1z) talk from the ELISA Spring Workshop 2022.

Our purpose with undertaking this kind of analysis in ELISA is to describe and provide examples of a method for identifying and documenting the risks associated with using Linux in a given context, and to examine how its existing features may be used to help to identify, control and/or mitigate these risks. The results of this analysis may then be used to derive the safety requirements that should apply for a system using Linux in such a context

For example, certain Linux configurations may help to address some of the risks that we identify, while others - including default kernel configurations - may introduce additional and possibly unacceptable risks (e.g performance optimisations that may have unintended or unpredictable consequences). Some mitigations for these risks may be outside the scope of Linux, involving AoUs on applications using Linux or on other components integrated with Linux as part of an operating system.

## Expected outputs

The outputs of this process are expected to be:

* A description of the *system context* and *safety goals* that we have assumed for the purpose of the analysis
* A specification of the risks that are considered in the analysis, and any exclusions from scope
* *Safety responsibilities* for both Linux and the other system components identified in the *system context*, and specific requirements relating to these
* Specific scenarios that might lead to these safety requirements being violated, which can be used to derive test cases and fault injections, and to identify where additional mitigations, safety mechanisms and/or requirements are needed to deal with these scenarios
* Documents capturing the results of Linux feature analysis (or analysis of other interacting components) that was undertaken as part of the investigation

## Current status

The OSEP group plans to start by applying this approach to the Automotive working group’s Telltale use case, and documenting the process that is applied alongside the results as we do so.

We invite other ELISA working groups and contributors to consider the following:

* What other *system contexts* and *safety goals* should we consider for analysis?
* What specific Linux features or properties should we focus on in our analyses?
* How might ELISA working groups collaborate in applying, refining and documenting this process and its results?
* Are there any external communities with which we might collaborate?

We would also welcome input from other open source contributors and communities interested in functional safety.
