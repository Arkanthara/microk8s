## Check if all the pods are in a READY 1/1 state (don't hesitate to repeat this step)

microk8s.kubectl get pods -n kube-system

## If not, check the logs

microk8s.kubectl logs nameofthedefectpod -n kube-system

## If needed, enable ports on firewall

### Example of error message:

[WARNING] plugin/kubernetes: Kubernetes API connection failure: Get "https://10.152.183.1:443/version": dial tcp 10.152.183.1:443: i/o timeout

### Example of solve error with iptables

sudo iptables -A OUTPUT -p tcp -d 10.152.183.1 --dport 443 -j ACCEPT

## Kill the pod can resolve the problem (it will restart...)

microk8s kubectl delete pods "pod name"

## Enable the dashboard

microk8s enable dashboard

## Expose the dashboard to the exterior of the cluster

microk8s kubectl expose deployment.apps/kubernetes-dashboard --type=NodePort --name=dashboard-nodeport --namespace=kube-system

## Get the port where the dashboard is exposed

microk8s kubectl get service dashboard-nodeport --namespace=kube-system

## Get default token to connect to the dashboard

microk8s kubectl describe secrets -n kube-system microk8s-dashboard-token

## Access to the dashboard from your remote machine (from your browser)

https://pinfo1.unige.ch:30192

# Create account role based access control (rbac)
in file read_only_dashboard.yaml
microk8s kubectl apply -f read_only_dashboard.yaml

# Create token for this account
microk8s kubectl create token -n kube-system $USER
Now, you can access to the dashboard thanks to the token of the new account

# Gitlab runner
microk8s helm3 repo add gitlab https://charts.gitlab.io
microk8s helm3 repo update gitlab
microk8s helm3 search repo -l gitlab/gitlab-runner

## Create new namespace and user to test gitlab runner
in file user_gitlab.yaml
microk8s kubectl apply -f user_gitlab.yaml

## Config and create runner (configuration of runner is in values.yaml !)
microk8s helm3 install -n <NAMESPACE> gitlab-runner -f values.yaml gitlab/gitlab-runner

## Create Docker image in image.dockerfile
## Active container registry in project settings
## Add the docker image in container registry:
### login to container registry
docker login registry.gitlab.unige.ch
docker build -t registry.gitlab.unige.ch/p-info-2024/p-info-3/phony/test .
docker push registry.gitlab.unige.ch/p-info-2024/p-info-3/phony/test

## Configure .gitlab-ci.yml and run pipeline !

## Gitlab agent: install from gitlab
apply command given in microk8s:

helm repo add gitlab https://charts.gitlab.io
helm repo update
helm upgrade --install my-super-agent gitlab/gitlab-agent \
    --namespace gitlab-agent-my-super-agent \
    --create-namespace \
    --set image.tag=v16.9.2 \
    --set config.token=glagent-zdVZf7Kccxk5uvy9VZF85LGSzCEZnKmjBhZAyfvMG_B7TykSiA \
    --set config.kasAddress=wss://gitlab.unige.ch/-/kubernetes-agent/



