# 03 - Profiling

**Pertubation** - the degree to which the performance of a system changes when it is analyses.

Pertubation negatively affects accuracy if it is non-deterministic.

It's often inversely proportional to **fidelity**.

## Sampling

Sampling is a way to reduce pertubation. The idea is not to collect all events, in order to reduce pertubation.

1. Sample in regular intervals
2. Sample in random intervals

This means that there is a chance you miss some events. However, there is less pertubation.

## Sampling Interval

The sampling interval is the distance between two samples being taken. Can either do

1. Time-based

   Idea: set a recurrent (hardware) timer and sample when it runs out. Use CPU reference cycles as a proxy metric. This is inaccurate and non-deterministic since computer clocks are noisy and poorly defined, but is easy to interpret since time is inversely proportional to performance.

2. Event-based

   We define an interval in terms of the occurrence of an event, e.g. every fifth function call. This is accurate, deterministic, and low-noise. However, this is tricky to interpret because it doesn't directly give us time - which is what we are primarily interested in.

## Indirect Tracing

Idea: trace events that dominate others. This is like intervals defined by the execution flow. This can be used to reduce overhead. The fidelity and accuracy depend on the event and indirection.

## Profile

A profile is an aggregate over the events of a specific metric. Examples:

- Total cache misses
- Cycles per instruction
- Cache misses by lines of code
- Number of branch mispredictions

## Event Sources

When choosing an event source, we have three things in mind

1. **Detailed** - as much information as we need
2. **Accurate** - should closely describe the real world
3. **Little pertubation**

We can get events from

- **Software**
  - Libraries - manual instrumentation/logging
  - Compiler - automatic instrumentation
  - OS - Kernel counters
- **Hardware**
  - Performance counter
- **Emulator**

## Instrumentation

This is augmenting a program with event logging code. 

- Advantages
  - No need for hardware support
  - Very flexibile
- Disadvantages
  - High overhead
  - High pertubation

There are three approaches to instrumentation

### Manual

This is basically logging (e.g. `printf`). 

#### Advantages

- Fine control over instrumentation
- Needs no support from hardware or compiler

### Disadvantages

- High overhead for implementation & runtime
- Usually disabled for build release, so needs recompilation for selective enabling

### Automatic Source-level

This is usually *compiler supported*, and also supports source-to-source rewriting. However, this means that there is less control and needs compiler reports.

### Automatic Binary

This can be **static** (compile-time) or **dynamic** (run-time).

#### Static

Simple, portable. Instrumentation overhead can be assessed from the binary.

#### Dynamic

No recompilation needed. Can be performed on a running process. Works with JiT compiled code.

## Hardware Performance Counters

These are *special registers* that can be configured to count low-level events. A fixed number can be active at runtime. However, these are

- often buggy and unmaintained
- sometimes poorly documented
- not accurate