# Manifest to my current local minikube setup 

After some interesting and rather successful experiences running Kubernetes in Docker Desktop **kind** (though also sometimes a bit painful - lastly I spent hours without success to upgrade my Kubernetes-version), the time had finally come to switch to a **minikube** cluster running on my local ARM64 MacBook with M1-processor. 

So around June 12, 2023 I installed minikube, read some pages and had the analogous service cluster setup as in DD up and running in no time. Delightful! 

I started to deploy some simple toy Spring Boot apps - partially as native (GraalVM) image, partially classical ARM64 images on VM. Quickly I needed persistent volumes, as one of the apps uses a file H2-database.
Others ran on Postgres in Docker, so next step was to run a Postgres-Database inside the cluster with postgres-service exposing it internally to the Spring apps.
The app deployments are exposed by services of type LoadBalancer, which are easily made accesible in minikube using tunnels.

Next step was to externalize the Spring configurations via ConfigMaps and introducing a secret for the credentials to Postgres. Finally I wanted to be able to oberve and manage the database, so I installed a PGAdmin as StatefulSet, together with a PVC and a pgadmin-service that minikube can on-demand expose to my browser.

Ideas, to come up, I hope: 
- Oauth2 security with new Spring 3.1.x Oauth2-Authorization-Server
- Service registry with spring cloud
- Spring Cloud Gateway and most probably Spring Cloud Configuration Server
- Some toy use-cases for Spring Cloud Kubernetes (maybe)
- Migrate more apps - especially one(s) serving a Vue- or React-Frontend
- Experience with KNative (very useful with growing landscape for scaling to zero)
- Playground for ArgoCD, service mesh etc.

## Repository Contents

> The yaml files for orchestrating the services landscape described above. A subfolder `pvc` comprises all PersistentVolumeClaims, such that I can remove all deployments and services from the root-directory with the `kubectl delete -f .`command and restart it with `kubectl apply -f .` without losing the persistent data in the PVs.   

> Few remainders of the previous kind setup, mostly consisting of a ClusterRoleBinding and ServiceAccount in the `kind`-subfolder,


## Progress

12.04.23 Initial minikue setup with hello-world service.

19.04.22 Five services are running:
- a **quiz-engine** service with an extensible Web-Quiz as CRUD-Rest Spring Boot application on Json-Basis (no frontend).
- a **code-sharing** service - à la pastebin - to store and exchange code snippets (rudimentary Thymeleaf-rendered HTML frontend and Json-API).
- a **recipe** service - running as Native image on Spring Boot 3.1.x, where recipes can be stored, patched and queried
- a postgres-service using a Postgres-OCI on alpine basis, running multiple databases (two as of today)
- a pgadmin-service to manage the database via NodePort exposure from the browser.
