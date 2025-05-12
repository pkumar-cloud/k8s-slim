# Kubernetes RBAC Tutorial

https://www.youtube.com/watch?v=hWj4y3Ok9Tg

## Resources

Kube RBAC Proxy Image: gcr.io/kubebuilder/kube-rbac-proxy:v0.14.0
Kube RBAC Proxy Example Configuration: [Link](https://github.com/brancz/kube-rbac-proxy/blob/af1a90b60796a6d6acb3673bd58336e1a6319c2b/examples/rewrites/README.md?plain=1#L106)
Cluster Role Binding Example: [Link](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#clusterrolebinding-example)
Cluster Role Example: [Link](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#clusterrole-example)

## API Calls

- Here are commands that you can use to add grades to the Grade Submission API. **Windows Users should use Git Bash**.

- No-Proxy: If applied without "kube-rbac-proxy" container, We can see that client is able to very freely without restriction access the POST and GET resources that are provided by the grade submission API.

```bash
cd no-proxy
kubectl apply -f .
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

curl http://localhost:31000/grades #get all grades without any restrictions
```

## API Calls with Authentication

**Configuration with rbac-proxy but no roles**:

```bash
cd ../with-proxy-sa
kubectl apply -f 01-grade-submission-api-deployment-with-proxy.yaml
#gets two containers, main great submission API and a sidecar container that is going to validate any requests that go to it.
kubectl apply -f 02-grade-submission-api-service-with-proxy.yaml
kubectl get pod -n grade-demo
curl -k https://localhost:31000/grades
```

- Get an unauthorized message becz kube-rbac-proxy acted as security guard and asked for creds or badge/role. Thus need to give this client/curl the access/the roles/the permissions that it needs in order to be authorized (or identify itself to rbac-proxy) to access the grade-submission-api resources.
- when making https requests, two things happen TLS encryption and certificate verification. TLS encryption is going to happen behind the scenes automatically but certificate verification, we don't really need to deal with so we'll say "-k" skip it.

```bash
kubectl logs -f <grade-submission-api-POD> -c kube-rbac-proxy -n grade-demo
#Can observe no authentication happening
```

**Create Service Account & Secret for Grade submission:**

- Any access control resource we define will be in rbac.yaml and define a Service account.
- A service account is a badge that something can use to identify itself within the kubernetes ecosystem.
- Create/apply rbac.yaml and secret.yaml files

```bash
kubectl apply -f .
kubectl get secret grade-sa-token -n grade-demo -o yaml #can see Kubertenes auto generated few fields e.g. token (BASE64 encoded) -> will be used by client, as a way to establish their identity as that service account.
kubectl get secret grade-sa-token -n grade-demo -o json #Output as JSON

#Extract token data/field using jsonpath tool & decode it:
kubectl get secret grade-sa-token -n grade-demo -o jsonpath="{.data.token}" | base64 -d
curl -k https://localhost:31000/grades -H "Authorization: Bearer <Above-TOKEN>" #Get unauthorized error, becz our service account isn't associated with any roles, so while we did provide our identity, we didn't really tell it what our role within the kubernetes ecosystem is.
```

**Create cluster role binding for Grade Service-account:**

- Updated grade service-account yaml file to create cluster role binding and bind to cluster-admin role.

```bash
kubectl delete 03-rbac-grade.yaml #clean from last step. Will delete Service account & "grade-sa secret" too because connected to SA.
cd ../rbac-grade
kubectl apply -f .
kubectl get secret grade-sa-token -n grade-demo -o jsonpath="{.data.token}" | base64 -d
curl -k https://localhost:31000/grades -H "Authorization: Bearer <Above-TOKEN>"
#Still get unauthorized, although cluster-admin role is given to Service account and thus curl/client because kube-rbac-proxy isn't allowing the client access to the grade submission API.
kubectl logs -f <grade-submission-api-POD> -c kube-rbac-proxy -n grade-demo
```

- Gets "forbidden" error because rbac-proxy is trying to validate our token and in order to do so, it is reaching k8s API server to create a resource "tokenreviews" but the kubernetes API is forbiding it from doing so. And therefore not allowing the kube-rbac-proxy to do its job to validate our token.
- Thus we need to assign it a service account that is bound to permissions that are sufficient for it to be able to create these "tokenreviews", hence create a Service account for rbac-proxy.

**Create Service account & cluster role binding for rbac-proxy**

```bash
kubectl delete -f . #cleanup from last step
cd ../rbac-proxy


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
