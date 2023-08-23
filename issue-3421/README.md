# Reproduction Steps

Follow the steps below to reproduce the [issue #3421](https://github.com/crossplane/crossplane/issues/3421).

1. Apply manifests under setup:

```bash
kubectl apply -f setup
# Wait until provider-nop is healthy
kubectl wait provider.pkg provider-nop --for condition=healthy --timeout 2m
# Wait until XRD definition is offered
kubectl wait xrd xnopresources.nop.example.org  --for condition=offered --timeout 2m
```

2. Create the claim:

```bash
kubectl apply -f claim.yaml
```

3. Verify tags are present in the composed resource:

```bash
kubectl get nopresources.nop.crossplane.io -o yaml
```

4. Edit the claim and remove the tag with key `test` from `spec.tags`:

5. Observe that the removed tag is added back to the claim:

```bash
kubectl get -f claim.yaml -o yaml
```
