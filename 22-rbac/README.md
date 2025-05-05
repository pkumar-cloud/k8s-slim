# Kubernetes RBAC Tutorial

https://www.youtube.com/watch?v=hWj4y3Ok9Tg

## Resources

Kube RBAC Proxy Image: gcr.io/kubebuilder/kube-rbac-proxy:v0.14.0
Kube RBAC Proxy Example Configuration: [Link](https://github.com/brancz/kube-rbac-proxy/blob/af1a90b60796a6d6acb3673bd58336e1a6319c2b/examples/rewrites/README.md?plain=1#L106)
Cluster Role Binding Example: [Link](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#clusterrolebinding-example)
Cluster Role Example: [Link](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#clusterrole-example)

## API Calls

Here are commands that you can use to add grades to the Grade Submission API. **Windows Users should use Git Bash**.

```bash
#If applied without "kube-rbac-proxy" container, We can see that client is able to very freely without restriction access the POST and GET resources that are provided by the grade submission API.
kubectl apply -f 01-grade-submission-api-deployment-no-proxy.yaml
kubectl get pod -n grade-demo

curl -X POST http://localhost:31000/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Harry", "subject": "Defense Against Dark Arts", "score": 95}'

curl -X POST http://localhost:31000/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Ron", "subject": "Charms", "score": 82}'

curl -X POST http://localhost:31000/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Hermione", "subject": "Potions", "score": 98}'
```

To verify, you can get all grades with:

```bash
curl http://localhost:31000/grades
```

## API Calls with Authentication

Here are commands that you can use to add grades to the Grade Submission API. **Windows Users should use Git Bash**.

```bash
curl -k -X POST https://localhost:31000/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Harry", "subject": "Defense Against Dark Arts", "score": 95}' -H "Authorization: Bearer $TOKEN"

curl -k -X POST https://localhost:31000/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Ron", "subject": "Charms", "score": 82}' -H "Authorization: Bearer $TOKEN"

curl -k -X POST https://localhost:31000/grades \
  -H "Content-Type: application/json" \
  -d '{"name": "Hermione", "subject": "Potions", "score": 98}' -H "Authorization: Bearer $TOKEN"
```

To verify, you can get all grades with:

```bash
curl -k https://localhost:31000/grades -H "Authorization: Bearer $TOKEN"
```
