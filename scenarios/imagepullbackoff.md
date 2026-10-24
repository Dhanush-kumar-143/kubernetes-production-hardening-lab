\# Scenario 3: ImagePullBackOff



\## Symptom

The `imagepull-demo` pod cannot start because Kubernetes cannot pull the requested container image.



\## Environment

\- Namespace: `scenario-imagepull`

\- Pod: `imagepull-demo`

\- Invalid image: `nginx:does-not-exist-lab`

\- Corrected image: `nginx:stable`



\## Root Cause

The requested image tag did not exist in the registry. Kubernetes reported `manifest unknown`, followed by `ErrImagePull` and `ImagePullBackOff`.



\## Investigation Commands



```cmd

kubectl get pods -n scenario-imagepull

kubectl describe pod imagepull-demo -n scenario-imagepull

kubectl get pod imagepull-demo -n scenario-imagepull -o jsonpath="{.spec.containers\[0].image}"

```



\## Evidence

\- Events reported `manifest unknown` for the invalid image tag.

\- Kubernetes reported `ErrImagePull` and `ImagePullBackOff`.

\- After correcting the image to `nginx:stable`, Events showed `Pulled`, `Created`, and `Started`.

\- The pod recovered to `1/1 Running`.



\## Resolution

Correct the image repository or tag to a valid image. For private registries, also verify authentication and image-pull credentials.



\## Key Learning

`ImagePullBackOff` is a retry/backoff state, not the root cause itself. Inspect pod Events to determine whether the image name, tag, registry availability, or authentication is responsible.

