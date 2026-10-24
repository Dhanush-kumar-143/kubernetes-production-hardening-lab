\# Readiness Probe Failure



\## Scenario



A Kubernetes pod remains running but is not ready because its readiness probe checks a file that does not exist.



\## Symptoms



\- Pod status is `Running`.

\- Pod readiness is `0/1`.

\- Readiness probe failure messages appear in Kubernetes Events.

\- The Service has no ready endpoints while this is the only selected pod.



\## Investigation



Inspect the pod:



```cmd

kubectl describe pod readiness-demo -n scenario-readiness

```



Inspect Events:



```cmd

kubectl get events -n scenario-readiness --sort-by=.metadata.creationTimestamp

```



The container creates `/tmp/healthy`, but the readiness probe checks `/tmp/healthz`.



\## Root Cause



The readiness probe checks the wrong file path. Because the file does not exist, the readiness check fails repeatedly.



Unlike a liveness probe failure, a readiness probe failure does not itself restart the container. Instead, Kubernetes marks the pod as not ready.



\## Resolution



Updated the readiness probe to check `/tmp/healthy` and recreated the pod using the corrected manifest.



\## Verification



```cmd

kubectl get pod readiness-demo -n scenario-readiness

kubectl get endpoints readiness-demo-service -n scenario-readiness

```



Expected results:



\- Pod is `Running` and `1/1` Ready.

\- The Service has a ready endpoint for the pod.



\## Lessons Learned



\- Readiness probes indicate whether a container is ready to serve requests.

\- Failed readiness checks remove the pod from ready Service endpoints.

\- Liveness probes can trigger container restarts; readiness probes do not.

\- `kubectl describe pod` and Kubernetes Events help identify probe failures.

\- A production readiness probe should check application readiness, not merely whether a process exists.

