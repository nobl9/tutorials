# Enforcing policies with Confest

As an organization, you may have specific polices that you want to enforce for
your Nobl9 configuration files.  An easy way of doing this is with
[conftest](https://www.conftest.dev/).  Conftest allows you apply tests to
structured data, allowing you to test and enforce polices in CI/CD pipelines.
It uses Rego, and utilizes the [Open Policy Agent](https://www.conftest.dev/)
to test the structured data against the polices that you write.

## Example

Let's assume that you have an SLO like this:

```yaml
- apiVersion: n9/v1alpha
  kind: SLO
  metadata:
    displayName: newrelic-rolling-occurrences-ratio
    labels:
      env:
        - main
    name: newrelic-rolling-occurrences-ratio
    project: newrelic-month
  spec:
    ...(the rest hidden for brevity)
```

You could write a policy file like such that checks for labels and if you
have `env` tags:

`policy/base.rego`

```rego
package main

deny[msg] {
  input.kind == "SLO"
  not input.metadata.labels

  msg := "SLO must have labels"
}

deny[msg] {
  input.kind == "SLO"
  not input.metadata.labels.env

  msg := "SLO must have environment labels"
}
```

Running the policy against your SLO, you can see that it passes, since we
have that tag present:

```bash
➜ conftest test slos.yaml

2 tests, 2 passed, 0 warnings, 0 failures, 0 exceptions
```

Great! It passes.  Next we can update our policy to make sure that there is
an owner in the labels:

```rego
package main

deny[msg] {
  input.kind == "SLO"
  not input.metadata.labels

  msg := "SLO must have labels"
}

deny[msg] {
  input.kind == "SLO"
  not input.metadata.labels.env

  msg := "SLO must have environment labels"
}

deny[msg] {
  input.kind == "SLO"
  not input.metadata.labels.owner

  msg := "SLOs must have an owner label"
}
```

Check the policy again:

```bash
➜ conftest test slos.yaml
FAIL - slos.yaml - main - SLOs must have an owner label

3 tests, 2 passed, 0 warnings, 1 failure, 0 exceptions
```

It fails as we expected! Yay!
