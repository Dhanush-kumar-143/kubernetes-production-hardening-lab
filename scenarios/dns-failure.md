

\# Kubernetes DNS Failure Troubleshooting



\## Scenario



A client Pod cannot resolve a Kubernetes Service because it uses an incorrect DNS name.



\## Symptoms



\- DNS lookup fails with NXDOMAIN.

\- Client cannot resolve the requested Service.

\- The actual Service exists under a different name.



\## Investigation



\### 1. Tested incorrect DNS name



kubectl exec dns-demo-client -n scenario-dns -- nslookup dns-demo-servce



Result: NXDOMAIN



\### 2. Checked available Services



kubectl get services -n scenario-dns



Confirmed the actual Service name is dns-demo-service.



\### 3. Inspected Service configuration



kubectl describe service dns-demo-service -n scenario-dns



Confirmed:

\- Selector: app=dns-demo-server

\- Endpoint: 10.244.0.48:80



\### 4. Inspected client DNS configuration



kubectl exec dns-demo-client -n scenario-dns -- cat /etc/resolv.conf



Confirmed Kubernetes DNS server: 10.96.0.10.



\## Root Cause



The client used an incorrect Service name: dns-demo-servce.



The correct name is dns-demo-service.



\## Resolution



Used the correct Service DNS name:



dns-demo-service



Also verified the fully qualified domain name:



dns-demo-service.scenario-dns.svc.cluster.local



\## Verification



DNS lookup successfully returned:



10.106.79.65



Tested HTTP connectivity:



kubectl exec dns-demo-client -n scenario-dns -- wget -qO- http://dns-demo-service



Received the Nginx welcome page.



\## Lessons Learned



\- Kubernetes Services receive DNS records.

\- Pods can access Services using their DNS names.

\- NXDOMAIN means the requested DNS name does not exist.

\- DNS resolution and application connectivity are separate checks.

\- Inspect Service names, endpoints and Pod DNS configuration when troubleshooting.

