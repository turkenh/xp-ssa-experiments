# Reproduction Steps

Follow the steps below to reproduce the [issue #4162](https://github.com/crossplane/crossplane/issues/4162).

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

3. Check the composed resource:

```bash
kubectl get nopresources.nop.crossplane.io -o yaml
```

```yaml
      ...
      fields:
        someField: someValue
        values:
          foo: bar
```

4. Edit the composition and replace `foo: bar` with `bar: foo`

```bash
kubectl edit composition xnopresources.nop.example.org
```

```diff
          ...
          fields:
            someField: someValue
            values:
-             foo: bar
+             bar: foo
```

5. Check the composed resource (after a minute or so) and observe both keys exist instead of latest:

```yaml
      fields:
        someField: someValue
        values:
          bar: foo
          foo: bar
```