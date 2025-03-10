# Make sure to start clean from last section.

## Review all the CRD's which is provided by the Custom operator E.g:

https://github.com/mongodb/helm-charts/tree/main/charts/community-operator => https://github.com/mongodb/mongodb-kubernetes-operator/tree/master/config/samples

```bash
helm list -A
helm uninstall mongodb -n mongodb
helm repo list
helm repo remove bitnami
helm search repo
helm repo add my-mongodb https://mongodb.github.io/helm-charts
helm repo update
helm search repo my-mongodb
helm install community-operator my-mongodb/community-operator --version 0.12.0 -n mongodb #installs mongodb operator, this will continuously watch for any CRD's and based upon whatever we define in that custom resource will deploy our MongoDB instance and create all the Kubernetes resources and ensure that they adhere to our desired application state.
kubectl get po -n mongodb
kubectl get all -n mongodb #list all mongodb operator resoirces
kubectl logs -f <mongodb-kubernetes-operator-PODname> -n mongodb

#used this one from official site:
https://github.com/mongodb/mongodb-kubernetes-operator/blob/master/config/samples/arbitrary_statefulset_configuration/mongodb.com_v1_custom_volume_cr.yaml

cd mongodb
kubectl apply -f mongodb-user-password.yaml
kubectl apply -f mongodb-grade-submission/yaml
kubectl get Mongodbcommunity -n mongodb


```
