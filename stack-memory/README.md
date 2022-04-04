# STPA results for Stack Memory Protection

This directory contains the results of safety analysis using STPA relating to  
the stack memory protection features of Linux.

## Objectives

* To specify the risks for safety-critical applications that may arise from
  faults affecting the use of stack memory by applications or the kernel
* To identify existing features or strategies that may be used to prevent or
  mitigate the effects of these faults
* To specify how these features or strategies can address the identified risks
  in the form of constraints (specific, verifiable requirements)

## Assumptions

* A ‘safety-critical function’ is something that may lead to a hazard in the
  event of a malfunction
* A 'malfunction' means that the execution or computation of the safety function
  leads to an unintended result
* The function is implemented in software
* The implemented software
    - Is developed in C or C++
    - Is running in a userspace process in a Linux-based system
    - Does not deliberately modify the stack with malicious intent
* The OS is managing memory as intended
    - Translation from virtual to physical memory addresses is correct
    - Registers are managed as intended
* The compiler used to build the software
    - Allocates memory on the stack in accordance with the language features
      employed by the developer
    - Outputs errors or warnings when the associated language features are used 
      incorrectly
