# Debugging Learning: Virtual Thread Pinning in Java 25

## Context
During the migration to Java 25 virtual threads, we observed performance degradation in the `RecallOps-Orchestrator` service.

## Discovery
Using JDK Flight Recorder (JFR), we identified that virtual threads were becoming "pinned" to the carrier threads. 

## Root Cause
The `synchronized` blocks in our legacy logging utility were causing virtual threads to pin when performing I/O operations. Since virtual threads cannot unmount while inside a synchronized block, they were blocking the underlying carrier threads.

## Resolution
Replaced the `synchronized` blocks with `java.util.concurrent.locks.ReentrantLock`.

## Key Learnings for the Team
- **Never use `synchronized`** for long-running or I/O-bound operations when using Virtual Threads.
- Use `ReentrantLock` as a modern alternative that supports virtual thread unmounting.
- Always run the `-Djdk.tracePinnedThreads=full` flag during migration testing.

Tags: #java25, #virtual-threads, #performance, #debugging
