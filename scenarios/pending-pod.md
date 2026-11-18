

\# Pending Pod Troubleshooting



\## Scenario

A Kubernetes Pod remains in Pending status because it cannot be scheduled.



\## Symptoms

\- Pod remains in Pending status.

\- Container does not start.

\- Scheduler reports insufficient memory.



\## Investigation

Inspected Pod details:



kubectl describe pod pending-demo -n scenario-pending



Scheduler event:



FailedScheduling: 0/1 nodes are available: 1 Insufficient memory.



Inspected node resources:



kubectl describe node minikube



\## Root Cause

The Pod requested 100Gi of memory, exceeding the available allocatable memory on the Minikube node.



\## Resolution

Reduced the memory request from 100Gi to 32Mi.



Deleted and recreated the Pod because its resource request could not be updated in place.



\## Verification

kubectl get pods -n scenario-pending



Result:

\- READY: 1/1

\- STATUS: Running

\- RESTARTS: 0



\## Lessons Learned

\- Pending Pods may indicate scheduling problems.

\- Scheduler events help identify the root cause.

\- Resource requests determine whether a Pod can be scheduled.

\- Inspect node capacity and allocatable resources when troubleshooting.

