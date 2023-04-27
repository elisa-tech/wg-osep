# Safety analysis approach

Our objective is to specify a system context and an example set of safety goals for a safety-related system involving the Linux kernel, in order to enable the safety analysis and specification of a set of safety responsibilities that we may assign to the kernel in that context (and possibly other contexts), at a useful level of detail.

* By *system context* we mean either a concrete system design, or an abstraction representing a class of system designs.
* By *safety goals*, we mean a set of system-level criteria that must be satisfied in order to avoid specific negative outcomes or consequences.
* By *safety responsibilities*, we mean the behaviour or properties that are required to avoid violating the safety goals for the given system context. This may involve cooperation with other safety mechanisms, which are required to operate when it is not possible to avoid violating a high level safety goal.

This is equivalent to defining the assumptions of use (AoU) for Linux (or any FOSS component or integration of components) as a Safety Element out of Context (SEooC).

Our purpose with this analysis is to describe and provide examples of a method for identifying and documenting the risks associated with using Linux in the given context, and how its existing features may be used to help to identify, control and/or mitigate these risks.

For example, certain Linux configurations may help to address some of the risks that we identify, while others - including default configurations - may introduce additional and possibly unacceptable risks that are associated with performance optimisations.
Some mitigations may be outside the scope of Linux, either involving AoUs on software using Linux or integrated with Linux as part of an operating system.

The outputs of this process are expected to be:
* A description of the system context that we are considering and the safety concept that we have assumed for the purpose of the analysis
* A list of the specific hazards and risks that are considered in the analysis, and any specific exclusions
* Safety requirements for both Linux and the other components of the system that we have identified in our system context
* Specific scenarios that might lead to these safety requirements being violated, which can be used as an input to test case and fault injection definition, and to identify where additional mitigations, safety mechanisms and/or additional safety requirements are needed to deal with these scenarios
* Documents capturing the results of Linux feature analysis (or analysis of other interacting components) that was undertaken as part of the investigation

Next steps:

* Which system contexts could we consider for an analysis?
* What safety concepts could we consider?
* What specific Linux features would we like to focus on?

We will start with the Automotive WG’s Telltale use case, and we would welcome other suggestions for future or parallel consideration.

