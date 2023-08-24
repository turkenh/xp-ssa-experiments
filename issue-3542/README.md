# Reproduction Steps

Follow the steps below to reproduce the [issue #3542](https://github.com/crossplane/crossplane/issues/3542).

1. Apply manifests under setup:

```bash
kubectl apply -f setup
# Wait until provider-kubernetes is healthy
kubectl wait provider.pkg provider-kubernetes --for condition=healthy --timeout 2m
# Wait until XRD definition is offered
kubectl wait xrd xnopresources.nop.example.org  --for condition=offered --timeout 2m
```

2. Give the kubernetes provider permissions to create resources in cluster:

```bash
SA=$(kubectl -n crossplane-system get sa -o name | grep provider-kubernetes | sed -e 's|serviceaccount\/|crossplane-system:|g')
kubectl create clusterrolebinding provider-kubernetes-admin-binding --clusterrole cluster-admin --serviceaccount="${SA}"
kubectl create -f providerconfig.yaml
```

3. Create the claim:

```bash
kubectl apply -f claim.yaml
```

4. Wait for a couple of minutes and check the status of the claim and notice that `unavailableReplicas` is set to 1 
despite the corresponding deployment and wrapping k8s object does not have it:

```bash
kubectl get -f claim.yaml -o yaml
```

```yaml
  nginx:
    availableReplicas: 1
    observedGeneration: 1
    readyReplicas: 1
    replicas: 1
    unavailableReplicas: 1
    updatedReplicas: 1
    ...
```