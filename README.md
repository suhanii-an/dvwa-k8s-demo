DVWA on Kubernetes — Attack Surface Demo



Local Kubernetes deployment of the Damn Vulnerable Web Application (DVWA),

with three exploited attack surfaces. Built for the AccuKnox Solution

Engineer Trainee technical assignment.



Environment

\- Windows 11, Docker Desktop

\- minikube v1.39.0 (single-node cluster, Docker driver)

\- kubectl v1.34.1

\- DVWA image: vulnerables/web-dvwa, security level: Low



Cluster setup

DVWA is deployed in its own `dvwa` namespace via a Deployment (1 replica)

and exposed with a NodePort Service on port 30080.



Attack surfaces demonstrated



1\. Command Injection

\- Input: `127.0.0.1; whoami` in the ping utility.

\- Result: executed arbitrary OS commands as `www-data`.

\- Escalation: read the pod's Kubernetes service-account token at

&#x20; `/var/run/secrets/kubernetes.io/serviceaccount/token` — a web flaw

&#x20; exposing cluster credentials usable against the Kubernetes API.

\- Root cause: user input concatenated into a shell command with no validation.

\- Fix: validate/whitelist input; avoid passing user data to shell calls.



2\. SQL Injection

\- Input: `1' OR '1'='1` returned all users; `UNION SELECT` dumped

&#x20; username/password-hash pairs from the users table.

\- Root cause: input concatenated directly into the SQL query.

\- Fix: parameterised queries / prepared statements.



3\. Reflected XSS

\- Input: `<script>alert(document.cookie)</script>`.

\- Result: injected script executed in the browser, exposing the session cookie.

\- Root cause: user input reflected into HTML without output encoding.

\- Fix: escape output with `htmlspecialchars()`; set HttpOnly on cookies.



Key takeaway

A single application vulnerability (command injection) was enough to reach

Kubernetes-level credentials, showing why runtime/container security matters

beyond fixing the app code alone.



Repository contents

\- `dvwa.yaml` — Kubernetes manifests (Namespace, Deployment, Service)

\- `screenshots/` — cluster setup and attack execution evidence



Author

Suhani A N

