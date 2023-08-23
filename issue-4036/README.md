# Reproduction Steps

Follow the steps below to reproduce the [issue #4036](https://github.com/crossplane/crossplane/issues/4036).

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

3. Verify that cidrBlock is present in the composed resource:

```bash
kubectl get nopresources.nop.crossplane.io -o yaml
```

```yaml
      ...
      fields:
        masterAuthorizedNetworksConfig:
        - cidrBlocks:
          - cidrBlock: 10.2.16.0/28
            displayName: bastion
          - cidrBlock: 1.2.3.4
```

4. Edit the claim as follows:

```bash
kubectl edit -f claim.yaml
```

```yaml
spec:
  authorizedNetworks: []
```

5. Observe that the cidrBlock is still present in the composed resource:

```yaml
      ...
      fields:
        masterAuthorizedNetworksConfig:
        - cidrBlocks:
          - cidrBlock: 10.2.16.0/28
            displayName: bastion
          - cidrBlock: 1.2.3.4
```


