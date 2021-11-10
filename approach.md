# OSEP proposed approach

a) Document scope of analysis using STPA

*In collaboration with other WGs e.g. Automotive?*

* Assumed system context
    - Hardware features, role of OS, boundaries of analysis, etc
    - Doesn't have to be concrete / complex
      - Specific to topic: start simple and elaborate later!
* Losses
    - OS-related outcomes that *may* violate a system's safety goals
      - i.e. lead to harm in a safety-related system
* Hazards
    - OS-level system conditions that *may* lead to these losses
* System-level constraints
    - Criteria that must be satisfied to *prevent* or *mitigate* hazards
      - May be a simple inversion of the hazard

b) Identify and document system measures and mitigations for Linux

*Out of scope for OSEP - LFSCS group?*

* Provided by kernel features, compiler, hardware, etc
* Find supporting evidence (design, tests, processes) for these
* Identify responsibilities of components (kernel, compiler, etc)

c) Perform hazard and risk analysis using STPA

 *In collaboration with LFSCS and Safety Architecture WGs?*

* Based on inputs from (a) and (b)
* Document control structure(s) for topic
    - Identify controllers and responsibilities
      - Functional abstraction of system elements
      - e.g. Kernel or subsystems, complier, other tools, etc
    - Define interactions as control actions and feedback
    - Identify Unsafe Control Actions and Loss Scenarios
    - Define Controller Constraints

d) Identify and document process measures and mitigations

* Identify engineering practices and tools to:
    - Implement constraints from (a) and (c)
    - Verify constraints from (a) and (c)
    - Evaluate and/or increase confidence in (b)
    - Identify or provide other evidence to support claims
      - e.g. Quality criteria
* Find supporting evidence from FOSS communities
    - Formal, verifiable process or inconsistent practice?

e) Identify and document claims and use cases

* To illustrate how a+b+c+d might support an in-context safety argument
* Evidence needed to support claims
    - How can other organisations use (d) to provide confidence?
    - What criteria does evidence need to satisfy?
* Document use cases
    - In collaboration with Domain WGs?
    - With kernel config(s) and hardware / system dependencies?
