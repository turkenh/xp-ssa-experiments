# Reproduction Steps

Follow the steps below to reproduce the [issue #3992](https://github.com/crossplane/crossplane/issues/3992).

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

3. Edit the claim to remove the `semverFull` label and add the `semverPatch` label:

```bash
kubectl edit -f claim.yaml
```

```diff
  compositionRevisionSelector:
    matchLabels:
-     semverFull: v1.22.6
+     semverPatch: v1.22.x
```

4. Observe the `semverFull` label is added back to the claim:

```bash
kubectl get -f claim.yaml
```

```yaml
  compositionRevisionSelector:
    matchLabels:
      semverFull: v1.22.6
      semverPatch: v1.22.x
```