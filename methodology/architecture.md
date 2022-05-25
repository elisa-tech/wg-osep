# Kernel Safety Architecture Methodology

## Goals

* Provide an architectural break down and description of the Kernel in supporting an allocated top level requirement
* Enable and execute an STPA safety analysis to verify Linux to be capable and fit to meet an allocated top level safety requirement

## Flow of Analysis

* Safety Requirement Definition: an initial safety requirement must be allocated to the Kernel as a whole from the safety concept of domain specific WGs.
* Scope Identification: given a safety requirement, the code functionally supporting such requirement must be comprehensively identified
* Initial partitioning into functional blocks: one or more functional blocks in the scope of the code above must be identified and for each of these blocks we should describe:
* Interfaces with the other blocks (NOTE: only interfaces that are meaningful in functionally supporting the allocated requirement)
* Functionalities allocated to each functional block (NOTE: only functionalities that are meaningful for fulfilling the allocated requirement)
* For each of the identified functionalities the role of the interfaces shall be described (under which conditions they are invoked)
* Safety Analysis: Do an STPA analysis and define derived safety requirements for each of the functional blocks as in 3)
* Iterate: Evaluate functional blocks complexity vs allocated safety requirements and eventually iterate back in 1) or stop here if not needed (complexity is acceptable)
