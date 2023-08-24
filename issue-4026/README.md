# Reproduction Steps

Follow the steps below to reproduce the [issue #4026](https://github.com/crossplane/crossplane/issues/4026).

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

3. Check the composed resource has all numbers aggregated from spec.oddNumbers and spec.evenNumbers:

```bash
kubectl get nopresources.nop.crossplane.io -o yaml
```

```yaml
      fields:
        allNumbers:
        - two
        - four
        - one
        - three
```

4. Edit the claim and remove one of the numbers from spec.oddNumbers:

```bash
kubectl edit -f claim.yaml
```

```diff
  spec:
    oddNumbers:
    - one
-   - three
```

5. Observe that the composed resource still has the all numbers including the removed one:

```bash
kubectl get nopresources.nop.crossplane.io -o yaml
```

```yaml
      fields:
        allNumbers:
        - two
        - four
        - one
        - three
```
