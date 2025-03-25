# jMultiPlanif: An educational Multilevel Feedback Queue Scheduling Simulator

The goal of this application is to illustrate the main concepts of
process scheduling in an introductory course of
Operating Systems.

The application simulates the evolution of several tasks in a monoprocessor multilevel queue scheduler,
showing the results in a Gantt chart together with some performance statistics.

<p align="center">
<!-- ![image](https://github.com/elnv/jMultiPlanif/assets/49199999/eeb6a5f0-ecea-4d74-8876-9db3028470ce) -->
<img src="https://github.com/elnv/jMultiPlanif/assets/49199999/eeb6a5f0-ecea-4d74-8876-9db3028470ce" width="90%">
    
<!-- ![image](https://github.com/elnv/jMultiPlanif/assets/49199999/2a2fb9af-08c1-431b-974c-f57d895039f0) -->
<img src="https://github.com/elnv/jMultiPlanif/assets/49199999/2a2fb9af-08c1-431b-974c-f57d895039f0" width="47%">
</p>

## Download
The current version is [jMultiPlanif3.2](./jMultiPlanif3.2-2025032400.jar) which works with java 11.

In linux you can run it in command line by doing ```java -jar jMultiplanif.jar```.

The user interface language can be changed in the help menu entry. Languages currently supported are Spanish (es) and English (en).

A previous version, jMultiPlanif3.0, working with jvm older than java 11 is also available but the user interface is only in Spanish.

## Features

The simulator implements the following features:

-   Number of queues (levels): configurable from 1 to 4 queues (the
    first queue has the highest priority).
-   Maximum number of processes: 100.
-   Maximum number of simulation cycles: 9999.
-   Cycle-by-cycle simulation; processes are defined by their CPU-I/O
    bursts, with durations multiple of one cycle.
-   The process bursts can be read from file, or can be added and
    deleted manually from the application.
-   Process state: *Running*, *Ready*, *Blocked*. In case there is no
    Ready process to dispatch, the system accounts those cycles as
    *Idle* cycles. \
    A colour legend is provided for easy visual interpretation of the
    results.
-   Scheduling policies:
    -   FIFO/FCFS (First-in First-out/First-come First-served).
    -   Round-Robin (with per-queue configurable quantum).
    -   SJF (Shortest Job First).
    -   SRTF (Shortest Remaining Time First).
    -   Non-preemptive Priority.
    -   Preemptive Priority (equivalent to Priority Round-Robin).
-   Preemption: Inter-queue preemption can be switched on and off, i.e.,
    a process arriving at a higher priority queue may or may not preempt
    the Running process, as long as the Running process comes from a
    lower priority queue.
    
    With preemption enabled, two different characteristics can be
    configured for Round-Robin queues:
    -   Renewed Quantum: On preemption, in case of not consuming its
        quantum, the process goes to the end of the queue with a full
        new quantum for the next time it is allocated the CPU.
    -   Not-renewed Quantum: On preemption, in case of not consuming its
        quantum, the process is assigned the remaining time of the
        quantum for the next time it is allocated the CPU.
-   Feedback: Inter-queue process feedback can be switched on and off.
    Feedback enabled makes sense only for FIFO, Round-Robin and
    Preemptive Priority policies.
    
    If feedback is enabled, the following rule of thumb applies:
    - When a
    process consumes its quantum it is penalized and goes down to the
    queue below (if it already is in the lowest priority queue, it stays
    there).

    - However, if the process blocks (sleeps), when it wakes up it
    is upgraded to the queue above (if it already is in the highest
    priority queue, it stays there).

    - If the process gets preempted it remains in the same queue.
    
-   In case several events happen at the same moment (for example,
    several processes arriving to the same queue at the same time, or a
    quantum is exhausted just when a CPU burst ends) the following order
    applies:
    
    1. New process
    2. Process ending
    3. Process blocking
    4. Quantum time-out
    5. Process waking up and transitioning to Ready (if more than
        one, the order is the blocking order)
    6. Running process preempted by another process arriving at an
        empty higher priority queue

Input file format
-----------------

The most effective way to simulate a set of processes is by reading
their bursts from a file. The processes read from file are added to the
already loaded in the application. The user may add/delete processes on
the fly as well. The option Reset, in the Processes menu, deletes all
processes.

**Notice** that the simulator sorts the
processes by order of arrival before simulation begins.
This must be taken into account when understanding the results.
The processes with the same admission time are sorted randomly, although
the initial order is that of the input file. If they are manually
modified by the user with the GUI, the processes with the same admission
time are sorted randomly.

The input file must be ASCII encoded. Each line describes one
process. Process format is the following:

    P={Tstart cpu,io,cpu,... prio queue} 

Note that the process must be described by 4 fields separated by
spaces:

```
+-----------------------------------+-----------------------------------+
|     Tstart                        | Start (or admission) time for the |
|                                   | process: An integer greater or    |
|                                   | equal than 0.                     |
+-----------------------------------+-----------------------------------+
|     cpu,io,cpu,...                | Process bursts: Burst lengths in  |
|                                   | time cycles separated by commas,  |
|                                   | without spaces; first and last    |
|                                   | burst must be CPU bursts (an even |
|                                   | number of bursts must be          |
|                                   | supplied).                        |
+-----------------------------------+-----------------------------------+
|     prio                          | Process (intra-queue) priority:   |
|                                   | An integer value greater or equal |
|                                   | to 1, only for the Priority       |
|                                   | policies. The lower the value,    |
|                                   | the lower the priority.           |
+-----------------------------------+-----------------------------------+
|     queue                         | Initial queue for process         |
|                                   | admission: An integer from 1 to   |
|                                   | 4; queue number 1 has the highest |
|                                   | priority; if this field is        |
|                                   | greater than the number of        |
|                                   | queues, the process will be       |
|                                   | assigned the last queue (lowest   |
|                                   | priority queue); do not confuse   |
|                                   | this field with 'prio' field.     |
+-----------------------------------+-----------------------------------+
```

Next, an input file example is shown. It defines three processes with
admission times of 0, 1 and 1, into queues 1, 3 and 2, respectively:
```
        P = {0 2,2,9,1,1 5 1}
        P = {1 4,3,4     2 3}
        P = {1 10        1 2}
```        

Note: To enter a comment line use the \# character. Example:
```
        P = {1 10      1 2} #This is a comment: this process is CPU-bound
```       

Note: If the file has blank lines or lines with errors, file reading
could fail. Lines with errors are usually ignored.

Additionally, the user may optionally include the queue system
description into the input file. If so, the existing queue configuration
will be deleted.

Each queue must be described in one line with the following format:

        Q={algorithm quantum} 
        

The definition comprises two mandatory fields separated by spaces:\

```
+-----------------------------------+-----------------------------------+
|     algorithm                     | An integer code for the           |
|                                   | algorithm:\                       |
|                                   | 0=FIFO, 1=RR, 2=SJF, 3=SRT,       |
|                                   | 4=Non-Preemptive Priority,        |
|                                   | 5=Preemptive Priority.            |
+-----------------------------------+-----------------------------------+
|     quantum                       | An integer greater than 0 for the |
|                                   | quantum; although it is           |
|                                   | mandatory, it only applies to     |
|                                   | quantum-based algorithms such as  |
|                                   | Round Robin.                      |
+-----------------------------------+-----------------------------------+
```

When parsing the lines describing the queues, the application does the
following:

-   ```Q={...}``` lines are read in order of appearance. The first of them is
    assigned the highest priority.
-   The number of queues is derived from the number of Q={\...} lines.
-   As the maximum number of queues is 4, lines from the 5th onwards are
    ignored.

## Results

The following results are shows after the simulation: scheduling
statistics, a process _Gantt_ chart, and a textual description, cycle by
cycle, with the contents of each queue.

## About jMultiPlanif

This simulator was originally developed by José Musco
as part of his final degree project under the supervision of Prof. Eladio D. Gutiérrez, at the department of [Computer Architecture](http://www.ac.uma.es) of the [University of Málaga](http://www.uma.es) (Spain).

The current version includes many improvements and adaptations by Prof. Gutiérrez.
The application was translated to English by Prof. Ricardo Quislant.

The name of the application derives from _Planificador Multinivel_ which is the Spanish translation of _Multilevel Scheduler_.
The initial _j_ stands for java.
