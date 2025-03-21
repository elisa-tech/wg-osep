# OSEP Mission statement

This working group aims to examine safety-related claims that we might like to
make about Linux as part of a system, and to explore how we can gather and
present evidence to support such claims as part of a safety engineering process.

We are interested in two broad classes of claims and evidence:

a) Claims relating to *safety features*: specific properties or behaviour of the
kernel that we propose to rely upon in order to support a safety-related claim
("why bad things will not happen") for a system incorporating Linux.

b) Those relating to the *safety integrity* of the Linux implementation for the
features that we propose to rely upon, and its integration as part of our
target system.

For (a), this might include the (inferred) design characteristics of a
subsystem or driver, or behaviour that we can enable or disable in the kernel
config. In OSEP, we will focus on sources of evidence to support claims about a
feature, or techniques for creating and validating this evidence, rather than
technical details of design or implementation.

In both cases we may examine practices or processes that are undertaken by the
Linux kernel community, as well as communities or organisations that consume
Linux (e.g. AGL, LTP), or we may identify practices or processes from other
sources that might be used by organisations who want to use Linux to build
systems with safety requirements.

Examples might include: the peer review process for patches, the testing
performed on a kernel stable branch when preparing a release, or the testing
performed by a system integrator for a product that incorporates Linux.

We may also extend the scope of our examination for a given claim to include
other FOSS components of a Linux-based operating system, together with the
tools used to construct, integrate or verify them.

Planned activities
------------------

Our objectives are to:

* Define claims that we would like to make about Linux and/or the
  processes used to develop it or integrate it as part of a product
* Identify evidence that we might use to support these claims
* Identify strategies or tools that we can use to gather, generate or
  reformulate evidence (perhaps in collaboration with the Tools WG)
* Examine the evidence gathered and document our findings
* Share and peer-review our findings in an elisa.tech Github repo

Possible claims might be proposed by another WG, perhaps in relation to a
specific use case, technique or safety argument, or we may select our own.
Claims that we examine may not all be safety claims, per se; some might relate
to software quality, for example, or functionality that does not have an
explicit safety role, since both of these may be needed to support safety
arguments.

We will use contributors’ knowledge of specific safety standards to inform our
work where appropriate - for example to determine what criteria to apply for a
given class of evidence - but specifying how a given claim and its supporting
evidence may be used as part of a certification process should be considered
out of scope.

Where we do not have consensus on results or conclusions, we will document the
different perspectives and/or open questions. We will manage the material
produced in an elisa.tech Github repo and collectively review it in GitHub PRs,
inviting review or input from the rest of the ELISA community where appropriate.

Development Process Retrospective
---------------------------------

In parallel with the main work of the group, we also plan to coordinate the
creation of documentation to summarise the results and conclusions of the work
done by the Development Process WG, which will be made available in an
elisa.tech Github repo. This is intended to provide background for new
participants and other WGs, as well as a baseline / conceptual framework for
the work of the new group.

This work will be discussed on the existing development-process mailing list,
to allow all past contributors to be involved.

Collaboration
-------------

The group expects to collaborate with the LFSCS and other ELISA working groups,
both to identify or discuss claims and evidence for analysis, and to address
wider questions relating to safety engineering processes. Where we work with
another group on given topics, we may jointly present the results at a workshop.
