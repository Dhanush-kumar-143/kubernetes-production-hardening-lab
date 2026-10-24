\# Liveness Probe Failure



\## Scenario



A Kubernetes pod repeatedly restarted because its liveness probe checked for a file that did not exist in the container.



\## Symptoms



\- Pod entered `CrashLoopBackOff`.

\- Restart count increased repeatedly.

\- Pod was not ready (`READY 0/1`).



\## Investigation



Inspected the pod configuration:



```cmd

kubectl describe pod liveness-demo -n scenario-liveness

```



The container command created `/tmp/healthy`:



```sh

touch /tmp/healthy; sleep 3600

```



However, the liveness probe checked `/tmp/healthz`:



```yaml

livenessProbe:

&#x20; exec:

&#x20;   command:

&#x20;     - cat

&#x20;     - /tmp/healthz

&#x20; initialDelaySeconds: 5

&#x20; periodSeconds: 5

&#x20; timeoutSeconds: 1

&#x20; failureThreshold: 3

```



The Kubernetes Events confirmed the failure:



```text

Liveness probe failed: cat: can't open '/tmp/healthz': No such file or directory

Container liveness-demo failed liveness probe, will be restarted

```



\## Root Cause



The liveness probe checked the wrong file path. The container created `/tmp/healthy`, but the probe expected `/tmp/healthz`.



After three consecutive probe failures, Kubernetes terminated the container and restarted it. Repeated failures caused the pod to enter `CrashLoopBackOff`.



\## Resolution



Updated the liveness probe to check the file that the container actually creates:



```yaml

livenessProbe:

&#x20; exec:

&#x20;   command:

&#x20;     - cat

&#x20;     - /tmp/healthy

&#x20; initialDelaySeconds: 5

&#x20; periodSeconds: 5

&#x20; timeoutSeconds: 1

&#x20; failureThreshold: 3

```



Deleted and recreated the pod using the corrected manifest.



\## Verification



```cmd

kubectl get pod liveness-demo -n scenario-liveness

```



Expected healthy output:



```text

NAME            READY   STATUS    RESTARTS   AGE

liveness-demo   1/1     Running   0          ...

```



\## Lessons Learned



\- A liveness probe determines whether Kubernetes should restart a container.

\- A failing liveness probe can cause repeated restarts and `CrashLoopBackOff`.

\- Kubernetes Events and `kubectl describe pod` are useful for identifying probe failures.

\- Probe commands must match the actual application environment and health-check path.

\- A readiness probe behaves differently: it controls whether a pod is considered ready to receive traffic, rather than triggering container restarts.

