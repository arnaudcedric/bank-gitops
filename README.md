
# bank-gitops
bank gitops for argo-cd

helm charts

https://chatgpt.com/c/6a4bf4fc-dbac-83ea-9609-d3b02fbe06fd

helm lint .

helm template lms .

helm template lms . > rendered.yaml

helm install lms .

# Check chart syntax
helm lint .

# Render manifests locally
helm template lms .

# Install for the first time
helm install lms .

# Verify resources
kubectl get all -n lms

# Modify values or templates
vim values.yaml

# Apply the changes
helm upgrade lms .

# Check revision history
helm history lms

# Roll back if needed
helm rollback lms 1

# Remove everything
helm uninstall lms

helm install lms .
NAME: lms
LAST DEPLOYED: Tue Jul  7 08:13:31 2026
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None

helm install <RELEASE_NAME> <CHART>

change backend replica from 3 to 4
theb run
helm upgrade lms .

let break the backend with an image that does not exist

tag: "does-not-exist"

helm upgrade lms .

I had 4 pods running observation after the change I thought all the pods would not run but 3 pods from the last replicaset are still running

by default the deployment strategy is rolling update

strategy:
  type: RollingUpdate
  rollingUpdate:
    maxSurge: 25%
    maxUnavailable: 25%

    helm does not know if the pods are failing or not

rollback to version that is working
    helm rollback lms 2

helm never delete history

kubectl get rs -n lms-helm

delete replicaset
kubectl delete rs replicasetName -n lms-helm

helm revisionHistoryLimit by default is 10

helm install bank-dev . -f values-dev.yaml

elm install bank-qa . -f values-qa.yaml

testing different images in different namespace

add this in usercontroller
@GetMapping("/welcome")
    public String welcome() {
        return "Welcome DEV";
    }

docker build -t backend:dev .

minikube image load backend:dev

helm install bank-dev . \
  -f values-dev.yaml \
  -n dev


helm upgrade bank-dev . -f values-dev.yaml -n dev

kubectl rollout restart deployment backend -n dev



docker build -t backend:qa .

minikube image load backend:qa

helm upgrade bank-qa .

kubectl port-forward service/backend-service 8080:8080 -n lms

➜  kubernetesHelm docker image inspect backend:dev --format='{{json .Config.Entrypoint}}'
["java","-jar","app.jar"]
➜  kubernetesHelm minikube ssh
docker@minikube:~$ docker image inspect backend:dev --format='{{json .Config.Entrypoint}}'
["app.jar"]
docker@minikube:~$ docker rmi backend:dev

➜  kubernetesHelm kubectl delete deployment backend -n dev
deployment.apps "backend" deleted from dev namespace
➜  kubernetesHelm helm uninstall bank-dev -n dev
release "bank-dev" uninstalled
➜  kubernetesHelm 

kubectl create namespace dev
kubectl create namespace qa


for all namespaces
helm list -A 


helm install bank-dev . -f values-dev.yaml -n dev

helm install bank-qa . -f values-qa.yaml -n qa

after restarting minikube you need to load the images back to minikube

minikube image load bank-frontend:v2

minikube image load bank-backend:dev



kubectl create namespace qa
helm install bank-qa . -f values-qa.yaml -n qa
minikube image load backend:qa
kubectl port-forward service/backend-service 8081:8080 -n qa
kubectl port-forward service/frontend-service 3001:80 -n dev

installing a chart
helm install bank-dev . -f values-dev.yaml -n dev

before installing to check syntax issue
helm lint .

uninstall
helm uninstall bank-dev -n dev

# Install Dev
helm install bank-dev . -f values-dev.yaml -n dev

# Install QA
helm install bank-qa . -f values-qa.yaml -n qa

# Make changes to the chart...

# Apply the changes
helm upgrade bank-dev . -f values-dev.yaml -n dev
helm upgrade bank-qa . -f values-qa.yaml -n qa

create namespce then install chart
helm install bank-dev . -f values-dev.yaml -n dev --create-namespace

helm install bank-dev . \
  -f values-dev.yaml \
  -n dev \
  --create-namespace

to know release names
helm list -A
revision
helm history <release-name> -n <namespace>

helm history <release-name> --namespace <namespace>
chatgpt
https://chatgpt.com/c/6a58cc02-3e84-83ea-a396-e17c1c0cf9e6

show release name in namespace dev
helm list -n dev

helm upgrade bank-dev . -f values-dev.yaml -n dev

helm rollback bank-dev 2 -n dev

helm rollback <release-name> <revision-number> -n <namespace>

builtin objects
.Release.Name

.Release.Namespace

.Chart.Name

.Chart.Version

.Values

.Files

.Capabilities

made changes in the remote github 
make change on my local
git pull --rebase origin main
git push origin main

kubectl create secret docker-registry ghcr-secret \
  --docker-server=ghcr.io \
  --docker-username=arnaudcedric \
  --docker-password=<YOUR_PAT> \
  --docker-email=<YOUR_GITHUB_EMAIL> \
  -n dev

  kubectl create secret docker-registry ghcr-secret \
  --docker-server=ghcr.io \
  --docker-username=arnaudcedric \
  --docker-password=<YOUR_PAT> \
  --docker-email=<YOUR_GITHUB_EMAIL> \
  -n qa

kubectl create secret docker-registry ghcr-secret \
  --docker-server=ghcr.io \
  --docker-username=arnaudcedric \
  --docker-password=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 \
  --docker-email=cedricarno@gmail.com \
  -n dev

kubectl create secret docker-registry ghcr-secret \
  --docker-server=ghcr.io \
  --docker-username=arnaudcedric \
  --docker-password=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 \
  --docker-email=cedricarno@gmail.com \
  -n qa

  kubectl get deployment backend -n dev -o yaml | grep -A5 imagePullSecret

  ImagePullBackOff mean k8s not able to pull image

  kubectl get pods -A

  kubectl get pods -n dev

  kubectl port-forward svc/frontend-service 3000:80 -n dev

  127.0.0.1:3000

get service for namespace dev
  kubectl get svc -n dev

  kubectl port-forward svc/backend-service 8080:8080 -n dev
  