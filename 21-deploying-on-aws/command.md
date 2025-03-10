```bash
eksctl create cluster --name my-cluster --region us-west-1 --node-type t3.medium --nodes 2

kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.8.2/deploy/static/provider/cloud/deploy.yaml

```
