# **Linux Kernel Safety - First Principles**

## Index

[Terms and Abbreviations](#Terms-and-Abbreviations)

[References](#References)

[Disclaimer](#Disclaimer)

[Purpose of the document](#Purpose-of-the-document)

[Structure of the document](#Structure-of-the-document)

[Philosophy of the document](#Philosophy-of-the-document)

[First Principles - Integrity](#First-Principles---Integrity)

[First Principles - Availability](#First-Principles---Availability)

[Considerations - Toward the safety goals](#Considerations---Toward-the-safety-goals)

[License: CC BY-SA 4.0](#License-CC-BY-SA-40)

## **Terms and Abbreviations**

   See the matching section in *Interference Scenarios for an ARM64 Linux System*

## **References**

1. ***Interference Scenarios for an ARM64 Linux System***
2. ***Safety Requirements for a Generic Linux System***
3. ***CheckList for Safety Claims on a Generic Linux System***
4. ***Using Linux in a Safe System***
5. ***Linux Memory Management Essentials***
6. [CC BY-SA 4.0 Deed | Attribution-ShareAlike 4.0 International | Creative Commons](https://creativecommons.org/licenses/by-sa/4.0/) - <https://creativecommons.org/licenses/by-sa/4.0/> License


## **Disclaimer**
This is not intended to be a complete list of facts, however it attempts
to summarise the most relevant ones from the documents listed in the
References section.

## **Purpose of the document**
Summarise the concepts from the documents in the Reference sections.
Justifications for each statement can be found throughout those documents.

## **Structure of the document**
The next section illustrates the philosohpy behind the approach used here to
deal with safety requirements vs a system using Linux.
The further two sections about "First Principles" address two aspects of safety:
-  integrity: reliably detecting interference to components with safety requirements
-  availability: protracted integrity of the safey-relevant functionality
The last section lists considerations that can help with raching the safety goals.


## **Philosophy of the Document**
*  A system subject to safety goals might employ the Linux Kernel.
*  Safety goals are specific to the overall system and the related use cases.
*  Similarly, one must expect that the solution will be tailored to both system and goals.
*  However, it is possible to describe the Linux Kernel from a safety point of view.
*  Not all the issues listed will be necessarily relevant to each system/application.
*  A Safety Analysis should, however, clearly state which issues are relevant, which are not, and why.


## **First Principles - Integrity**
This section provides touchstone concepts about interference of QM components with
safety-qualified ones.

1. The vanilla Linux Kernel, is not sufficient to support, alone, safey goals.
2. No internal kernel barriers/protections against interference to variable data, including safety-relevant data.
3. Any component within the kernel context can generate (cascaded) interference.
4. Internal interference can cascade also through components that have been qualified for safety at unit-level.
5. The Linux Kernel is able to interfere with both itself and any part of user-space processes (linear map).
6. No generic solution for very complex systematic corruption of any writable memory.
7. Stress testing alone is not sufficient for supporting safety claims.
8. Where safety claims involving the Linux Kernel are supported by testing, both positive, requirements-based testing and focused, analysis-led negative testing are required.
9. Those security features which are based on randomisation decrease repeatability of testing (e.g. structure layout randomisation).
10. If safety claims relating to one component are based on support by another, then the supporting component must have the same or better level of safety integrity (e.g. a safety-qualified watchdog).
11. Any other process could potentially interfere with a safety-qualified process, indirectly, through the kernel (e.g. a memory management bug could trigger interference between such processes).
12. Using cgroups/containers/SELinux removes only certain simple types of user-space-induced interference.
13. In a mixed-criticality scenario, non safety-qualified code represents a safety liability that grows with the frequency of execution (cgroups/containers, LSM/SELinux, etc.).
14. HW enhancements are not a catch-all solution (e.g. ECC Memory doesn't prevent interference from non-safety-qualified SW).
15. Detection of interference depends more on the recipient than the origin of the interference: too many sources, too difficult to model reliably.
16. Difficult to say when all necessary resources are allocated and if they will be retained (e.g. process memory pages).


## **First Principles - Availability**
This section provides touchstone concepts about (lack of) system availability, in presence of non-safety-qualified components.

1. Detecting interference alone doesn't help with controlling/managing availability.
2. The nature of the very complex systematic interference makes estimating probability of a failure unrealistic.
3. Stress testing as a means of safety qualification might be realistic at most for very simple cases.
4. Qualitatively, the likelihood of a failure grows with invocativation of non-safety-qualified components. For example:
   1. invocation of device drivers, especially if not related to safety operations
   2. syscalls
   3. memory allocation/release operations
   4. I/Os, e.g. storage, network
   5. evaluation of Linux Security Module hooks
   6. evaluation of cgroups tests
   7. presence of non-safety-relevant triggering many of the above points


## **Considerations - Toward the safety goals**
Considerations based on the two previous sections.

1. Devise negative testing simulating interference toward safety-relevant components.
2. Rely only on external components/mechanisms that already qualified at same-o-better safety level.
3. Analyse effects of interference based on recipient (e.g. corrupted data harder to notice)
4. When possible, leverage system configuration (e.g. external safety island / external monitor)
5. Pick adequate granularity for verification (e.g. fine grained component validation vs end-to-end)
6. In case of end-to-end verification, choose strategy: periodic test injection, or on demand.


## **License: CC BY-SA 4.0**

### **DEED**
### **Attribution-ShareAlike 4.0 International**

Full License text: <https://creativecommons.org/licenses/by-sa/4.0/>

**You are free to:**

* **Share** — copy and redistribute the material in any medium or format for any purpose, even commercially.

* **Adapt** — remix, transform, and build upon the material for any purpose, even commercially.

The licensor cannot revoke these freedoms as long as you follow the license terms.

**Under the following terms:**

* **Attribution** — You must give appropriate credit , provide a link to the license, and indicate if changes were made . You may do so in any reasonable manner, but not in any way that suggests the licensor endorses you or your use.

* **ShareAlike** — If you remix, transform, or build upon the material, you must distribute your contributions under the same license as the original.

* **No additional restrictions** — You may not apply legal terms or technological measures that legally restrict others from doing anything the license permits.

**Notices:**

You do not have to comply with the license for elements of the material in the public domain or where your use is permitted by an applicable exception or limitation .
No warranties are given. The license may not give you all of the permissions necessary for your intended use. For example, other rights such as publicity, privacy, or moral rights may limit how you use the material.
