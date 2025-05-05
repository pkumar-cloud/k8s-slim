```bash
eksctl create cluster --name my-cluster --region us-west-1 --node-type t3.medium --nodes 2
kubectl config current-context

eksctl create cluster \
 --name my-cluster \
 --region us-west-2 \
 --node-type t3.medium \
 --nodes 3

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/cloud/deploy.yaml
# deploy Nginx ingress controller on our cluster, and thereby orchestrate the creation of a load balancer that allows us to make public requests to our cluster.

kubectl get ns
kubectl get svc -n ingress-nginx
cd 17-helm-charts/grade-submission-portal
kubectl create ns grade-submission
helm install grade-submission-portal . -n grade-submission

eksctl delete cluster --name my-cluster

#cleanup
remove helm repos:
helm repo list
helm repo remove mongodb
docker images
docker system prune -a #removes unused Docker objects & images
disable k8s from docker desktop & delete all images
```
