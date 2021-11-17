# STPA results for Stack Memory Protection

This directory contains the results of safety analysis using STPA relating to  
the stack memory protection features of Linux.

The goals of this analysis are:

* To specify the risks for safety-critical applications that may arise from
  faults affecting (or misuse of) stack memory by applications or the kernel
* To identify existing features or strategies that may be used to prevent or
  mitigate the effects of these faults
* To specify how these features or strategies can address the identified risks
  in the form of constraints (specific, verifiable requirements)
