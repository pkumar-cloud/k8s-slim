# Make sure that grade-submission is applicaiton is deployed. grade-submission-api containers should not be ready until mongodb comes up.

```bash
#Add Helm Repository & Setup Mongo first:
helm repo add bitnami https://charts.bitnami.com/bitnami # This adds the Bitnami repository, which hosts many popular software charts.
https://github.com/bitnami/charts/tree/main/bitnami
#Update Helm Repositories:
helm repo update # Ensures you have the latest chart versions available.
#Search for Available Charts:
helm search repo #List all the avaliale charts. Can see Github repo for Bitnami
helm search repo bitnami/mongodb --versions
cd mongodb
helm show values bitnami/mongodb > default_values.yaml #export the repo values.yaml to review locally
helm install mongodb bitnami/mongodb --version 15.6.13 -f values.yaml -n mongodb #install mongodb release name, with mongodb version 15.6.13 and overrides the default values with our values.yaml file

#when the service exists in a different namespace and you want a pod to communicate with that service => <ServiceName>.<Service-Namespace>.svc.cluster.local:<portNumber>
```

```bash
helm install grade-submission-api . -n grade-submission
helm install grade-submission-portal . -n grade-submission
localhost:80
helm list -A

```
