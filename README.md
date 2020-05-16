# kubernetes-project
[![Build Status](https://www.travis-ci.com/LGX9/kubernetes-project.svg?branch=master)](https://www.travis-ci.com/LGX9/kubernetes-project)
## Docker
Docker images are uploaded to dockerhub:
- backend-user: cyronix/udacity-restapi-user
- backend-feed: cyronix/udacity-restapi-feed
- frontend: cyronix/udacity-frontend

To start the app via docker-compose change into the directory udacity-c3-deployment/docker and use the command `docker-compose up` and use your browser to connect to http://localhost:8100.

## Kubernetes

### Minikube as local Kubernetes cluster
I used minikube on linux to start the kubernetes cluster on my local pc and connect to RDS on AWS.
The standard minikube start up resources were not sufficient though, I had to change the resources for cpu/memory, i.e.
`minikube start --cpus 2 --memory 2048` for 2 CPU's and 2 GB memory.

### Setting up the environment
Change into the directory udacity-c3-deployment/k8s.
The project requires AWS credentials and DB credentials as secrets as well as Environment Variables.
You have to customize the secret files *aws-secret.yaml* and *env-secret.yaml* using Base64 encoding for the credentials and you have to change the configmap file *env-configmap.yaml* for  the database url.
Thereafter the secrets and configmap have to be loaded into the kubernetes cluster via the commands:
- `kubectl apply -f aws-secret.yaml`
- `kubectl apply -f env-secret.yam`
- `kubectl apply -f env-configmap.yaml`

### Setting up the deployments of pods
For creating the pods kubernetes deployments are being used, which can recreate them if they fail and also can be specified to run multiple replicas of a servce:
- `kubectl apply -f backend-feed-deployment.yaml`
- `kubectl apply -f backend-user-deployment.yaml`
- `kubectl apply -f frontend-deployment.yaml`
- `kubectl apply -f reverseproxy-deployment.yaml`

### Setting up the services
For discovering the different pods kubernetes services are being used to discover them by the label *service*, which is useful since deployments append unique ids to the pod name, but using a selector they can still be found.
The services can be set up via the commands:
- `kubectl apply -f backend-feed-service.yaml`
- `kubectl apply -f backend-user-service.yaml`
- `kubectl apply -f frontend-service.yaml`
- `kubectl apply -f reverseproxy-service.yaml`

### Port forwarding to the services
The services are now running, but there is no communication outside the kubernetes cluster so a port the host machine needs to be mapped to a host of a service in the kubernetes cluster.
The following port-forwarding needs to be enabled:
- backend services: `kubectl port-forward svc/reverseproxy 8080:8080`
- frontend-service: `kubectl port-forward svc/frontend 8100:8100`

The application should now be accessible via a browser on http://localhost:8100.

### CI/CD
CI/CD is in place via an integration of travis ci which automatically builds docker images after a commit.

### Deployment images
Please find deployment images here:

- [Travis CI Build output](images/travis-ci-build.PNG "Travis CI")
- [Docker Compose running Containers](images/docker-compose-up.PNG "Docker Compose")
- [Kubernetes Services/Pods and Port Forwarding to reverse proxy](show-pods-service-portforward.png "Kubernetes Pods/Services")
- [Kubernetes Port forwarding to frontend](kubernetes-front-end-forward.png "Kubernetes Frontend Port forwarding")
- [View in Browser of application](browser.PNG "Browserview")







