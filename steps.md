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

## Create account rbac


"steps.md" 61L, 1487B                                                                                                                                                        61,0-1        Bot

