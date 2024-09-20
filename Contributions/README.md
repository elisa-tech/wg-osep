# Contributions

This directory contains both work originating from 3rd-party, released under
compatible licenses, and peer-reviewed derived work, produced under the
ELISA umbrella.

## Index
These markdown documents are actively extended and reviewed, as part of safety-related work done within ELISA.

* [Linux Memory Management Essentials](Linux_Memory_Management_Essentials.md)
  (3rd party) Summary of Memory Management features of the Linux Kernel that are also relevant for safety.
  
* [Using Linux in a Safe System](Using_Linux_in_a_Safe_System.md)
  (3rd party) Non-exhaustive list of engineering considerations and practices, that can help with designing a Safe System containing Linux.
  It can be seen as a companion to the Checklist below.

* [CheckList for Safety Claims on a Generic Linux System](CheckList_for_Safety_Claims_on_a_Generic_Linux_System.md)
  (3rd party) Non-exhaustive list of safety woes that can affect one's system qualification and should be explicitly addressed, in preparation to the qualification process.
  The rationale for including these, both from a FuSa and an architectural perspective, can be found in the two documents below.

* [Safety Requirements for a Generic Linux System](Safety_Requirements_for_a_Generic_Linux_System.md)
  (3rd party) Collection of requirements meant to address Functional Safety, with focus on both Freedom From Interference and Availability.
  They take as reference a generic ARM64 system, running Linux. 
  
* [Interference Scenarios for an ARM64 Linux System](Interference_Scenarios_for_an_ARM64_Linux_System.md)
  (3rd party) Analysis of a reference system based on ARM64, running Linux. It provides architectural elements, both SW and HW, which are then used
  as reference for describing examples of interference that the system is subject to.
  
## Seminal Documents
These pdf documents are provided as both reference and baseline. They represent the starting point for the work done within ELISA.
They are not expected to evolve further.

* [Safety Requirements for a Generic Linux System](Safety_Requirements_for_a_Generic_Linux_System.pdf)
  (3rd party) Collection of requirements meant to address Functional Safety, with focus on both Freedom From Interference and Availability.
  They take as reference a generic ARM64 system, running Linux. Original release.
  
* [Interference Scenarios for an ARM64 Linux System](Interference_Scenarios_for_an_ARM64_Linux_System.pdf)
  (3rd party) Analysis of a reference system based on ARM64, running Linux. It provides architectural elements, both SW and HW, which are then used
  as reference for describing examples of interference that the system is subject to. Original relese.
