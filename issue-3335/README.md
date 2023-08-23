# Reproduction Steps

Follow the steps below to reproduce the [issue #3335](https://github.com/crossplane/crossplane/issues/3335).

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

3. Edit the composed resource as follows (mimicking reference resolution made by the provider):


```bash 
kubectl edit nopresources.nop.crossplane.io
```

```diff
    fields:
      bArr:
      - targetSelector:
          matchLabels:
            key2: foo
+       targetRefs:
+       - name: bar
      someField: someValue
```

4. Wait for a reconcile (or trigger one by editing the claim):

5. Observe that the XR reconciler reverted the change made in step 3:

```bash
kubectl get nopresources.nop.crossplane.io -o yaml
```

```yaml
      ...
      fields:
        bArr:
        - targetSelector:
            matchLabels:
              key2: foo
        someField: someValue
```
