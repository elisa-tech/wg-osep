# Safety analysis approach

Our objective is to specify a system context and an example set of safety goals for a safety-related system involving the Linux kernel, in order to enable the safety analysis and specification of a set of safety responsibilities that we may assign to the kernel in that context (and possibly other contexts), at a useful level of detail.

* By *system context* we mean either a concrete system design, or an abstraction representing a class of system designs.
* By *safety goals*, we mean a set of system-level criteria that must be satisfied in order to avoid specific negative outcomes or consequences.
* By *safety responsibilities*, we mean the behaviour or properties that are required to avoid violating the safety goals for the given system context. This may involve cooperation with other safety mechanisms, which are required to operate when it is not possible to avoid violating a high level safety goal.

This is equivalent to defining the assumptions of use (AoU) for Linux (or any FOSS component or integration of components) as a Safety Element out of Context (SEooC).

Our purpose with this analysis is to describe and provide examples of a method for identifying and documenting the risks associated with using Linux in the given context, and how its existing features may be used to help to identify, control and/or mitigate these risks.

TODO: What artifacts do we expect to generate from this process?
