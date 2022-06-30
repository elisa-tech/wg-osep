# OSEP proposed approach

This document proposes an approach to the [framework](framework.md)) using STPA.

## 1) Document scope of analysis

*With inputs from other WGs*

a) Specify the topic that will be the focus of analysis

* What specific functionality are we going to focus on?
* What is the *system under analysis (SUA)*?
    - This could be a complete hardware-software stack or a specific subsystem
* What are our assumptions about the SUA's *environment*?
    - Environment means the context within which the SUA will operate
    - This is likely to include a larger system that includes the *SUA* as a component or element
    - These assumptions do *not* need to be comprehensive, as we will identify more during analysis
* What safety-related goals do we have for the *SUA*?
    - How might the *SUA* contribute to the safety goals of the larger system?
      - e.g. is it used to implement a safety mechanism?
    - What must the *SUA* avoid doing in order to support those goals?

b) Document the scope of analysis and top-level goals using STPA

* System boundaries and inputs/outputs
    - What boundary defines the *SUA*?
    - What does it interact with outside this boundary?
      - e.g. hardware components    
    - Does not have to be concrete or complex
      - Specific to topic: start simple and elaborate later!
* Losses
    - Outcomes that *may* violate the *SUA*'s goals
      - i.e. lead to harm in a safety-related system
    - Can also include outcomes that may violate non-safety goals
      - e.g. customer dissatisfaction
* Hazards
    - System conditions that *may* lead to these losses

* System-level constraints
    - Criteria that must be satisfied to *prevent* or *mitigate* hazards
      - May be a simple inversion of the hazard
    - Can include non-safety criteria (e.g. relating to performance or reliability)

## 2) Document system architecture

*In collaboration with LFSCS and Safety Architecture WGs*

Can include known measures and mitigations for Linux


* Identify responsibilities of components (kernel, compiler, kernel subsystem, etc)


## 3) Perform hazard and risk analysis

 *In collaboration with Safety Architecture WG*

* Based on inputs from (1) and (2)
* Document control structure(s) for topic
    - Identify controllers and responsibilities
      - Functional abstraction of system elements
      - e.g. Kernel or subsystems, complier, other tools, etc
    - Define interactions as control actions and feedback
    - Identify Unsafe Control Actions and Loss Scenarios
    - Define Controller Constraints

## 4) Identify and document process measures and mitigations

* Identify engineering practices and tools to:
    - Implement constraints from (a) and (c)
    - Verify constraints from (a) and (c)
    - Evaluate and/or increase confidence in (b)
    - Identify or provide other evidence to support claims
      - e.g. Quality criteria
* Find supporting evidence from FOSS communities
    - Formal, verifiable process or inconsistent practice?

## 5) Identify and document claims and use cases

 *In collaboration with other WGs*

* To illustrate how (1)+(2)+(3)+(4) might support an in-context safety argument
* Evidence needed to support claims
    - How can other organisations use (4) to provide confidence?
    - What criteria does evidence need to satisfy?
* Document use cases
    - In collaboration with Domain WGs?
    - With kernel config(s) and hardware / system dependencies?
