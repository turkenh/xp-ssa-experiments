# Reproduction Steps

Follow the steps below to reproduce the [issue #4315](https://github.com/crossplane/crossplane/issues/4315).

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

3. Annote the claim as follows:

```bash
kubectl annotate -f claim.yaml foo=bar
```

4. Verify that the annotation is present in the composed resource:

```bash
❯ kubectl get composite apiextensions-composition-pandt-sc5sp -o jsonpath='{.metadata.annotations}'
{"foo":"bar"}
```

5. Remove the annotation from the claim:

```bash
kubectl annotate -f claim.yaml foo-
```

6. Observe that the annotation is still present in the composed resource:

```bash
❯ kubectl get composite apiextensions-composition-pandt-sc5sp -o jsonpath='{.metadata.annotations}'
{"foo":"bar"}
```