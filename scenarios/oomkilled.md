\# Scenario 2: OOMKilled



\## Symptom

The `oom-demo` pod terminates with status `OOMKilled`.



\## Environment

\- Namespace: `scenario-oom`

\- Image: `busybox:1.36`

\- Memory request: `16Mi`

\- Memory limit: `24Mi`

\- Restart policy: `Never`



\## Reproduction

The container attempts to write 64 MiB of data to `/dev/shm/fill`, a typically memory-backed filesystem. This workload is designed to exceed the configured container memory limit.



\## Investigation Commands



```cmd

kubectl get pod oom-demo -n scenario-oom

kubectl describe pod oom-demo -n scenario-oom

kubectl get pod oom-demo -n scenario-oom -o jsonpath="{.status.containerStatuses\[0].state.terminated.reason}"

kubectl logs oom-demo -n scenario-oom

```



\## Evidence

\- Pod status: `OOMKilled`

\- Termination reason: `OOMKilled`

\- Container log: `Killed`

\- Configured memory limit: `24Mi`



\## Root Cause

The workload exceeded the container's enforced memory limit, resulting in an out-of-memory termination.



\## Remediation

\- Investigate application memory consumption.

\- Reduce unnecessary memory allocations or fix memory leaks.

\- Set memory requests and limits based on measured workload needs.

\- Monitor memory usage and OOM termination events.



Increasing the memory limit alone may not resolve a memory leak or unbounded allocation.



\## Key Learning

A memory request helps Kubernetes schedule a pod; a memory limit constrains the container's memory usage. Use termination details, logs, and pod Events to diagnose OOM failures.

