\# Scenario 1: CrashLoopBackOff



\## Symptom

The `crash-demo` pod repeatedly restarts and enters `CrashLoopBackOff`.



\## Environment

\- Namespace: `scenario-crashloop`

\- Image: `busybox:1.36`

\- Restart policy: `Always`



\## Root Cause

The container command intentionally runs `exit 1`, terminating the application process with a non-zero exit code. Kubernetes restarts the container because its restart policy is `Always`. Repeated failures trigger exponential restart backoff.



\## Investigation Commands



```cmd

kubectl get pods -n scenario-crashloop

kubectl describe pod crash-demo -n scenario-crashloop

kubectl logs crash-demo -n scenario-crashloop

kubectl logs crash-demo -n scenario-crashloop --previous

kubectl get pod crash-demo -n scenario-crashloop -o jsonpath="{.status.containerStatuses\[0].lastState.terminated.exitCode}"

```



\## Evidence

\- Restart count increased to 8.

\- Container logs showed `Starting application`.

\- Last termination exit code was `1`.

\- Pod Events reported `BackOff` while restarting the failed container.

\- The image was pulled successfully and the pod was scheduled successfully.



\## Resolution

Replace the failing command with a process that remains running, then verify the pod becomes healthy.



\## Key Learning

`CrashLoopBackOff` describes Kubernetes backing off repeated container restarts; it is not itself the root cause. Use logs, termination details, restart counts, and Events to identify the underlying failure.



