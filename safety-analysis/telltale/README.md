# Telltale Safety Analysis

## Purpose and scope

This section of the repository hosts the OSEP working group's analysis of the Automotive Working Group's [Instrument Cluster use case](https://github.com/elisa-tech/wg-automotive/tree/master/Cluster_Display_Use_Case_v2).

There are two distinct parts to this:

* General instrument cluster / dashboard display (non-safety)
* Specific telltale notifications as part of this display

Our focus is on the specific mechanism that verifies that the telltales are displayed correctly, and the roles and responsibilities that Linux has in supporting this. 'Displayed correctly' in this context means that the rendering of the telltale portions of the display have resulted in a specified result on the screen, within an specified timeframe.

Looking at [this diagram](https://raw.githubusercontent.com/elisa-tech/wg-automotive/master/Cluster_Display_Use_Case_v2/stpa/Telltale-control-structure-minimal.svg) from the Automotive WG:

* The system boundary is an ECU running an operating system (involving Linux) that is responsible for executing the cluster display and associated processing
* The checking control and safety manager are the safety-relevant components that implement the safety mechanism
* The dashboard manager may also be safety relevant, because it provides the input data to the checking control
* The watchdog may be internal to the ECU or external (future design decision)

The latter is an additional safety mechanism that has been proactively added in the system concept, but we may want to omit it for our initial stage of analysis, to better understand the context that may make it necessary, and explore other options.

The question of how the Display component might be involved in ensuring that the telltales are correctly displayed, or notifying the user in the even of lost or degraded safety functionality, is part of a future iteration of analysis, and belongs to another potential system/analysis boundary.
