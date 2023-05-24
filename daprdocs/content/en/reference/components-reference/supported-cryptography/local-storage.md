---
type: docs
title: "Local storage"
linkTitle: "Local storage"
description: Detailed information on the local storage cryptography component
---

## Component format

The purpose of this component is to load keys from a local directory. The component accepts as input the name of a folder, and loads keys from there. Each key is in its own file, and when users request a key with a given name, Dapr will load the file with that name.

{{% alert title="Note" color="primary" %}}
This component uses the **built-in cryptographic engine in Dapr** to perform operations. Although keys are never exposed to your application, Dapr has access to the raw key material.

{{% /alert %}}


A Dapr `crypto.yaml` component file has the following structure:

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: mycrypto
spec:
  type: crypto.localstorage
  metadata:
    version: v1
    - name: path
      value: fixtures/crypto/localstorage/
```

{{% alert title="Warning" color="warning" %}}
The above example uses secrets as plain strings. It is recommended to use a secret store for the secrets, as described [here]({{< ref component-secrets.md >}}).
{{% /alert %}}

## Spec metadata fields

| Field              | Required | Details | Example |
|--------------------|:--------:|---------|---------|
| `path`             | Y        | Folder containing the keys to be loaded. When loading a key, the name of the key will be used as name of the file in this folder.  | `/path/to/folder`

## Related links
[Cryptography building block]({{< ref cryptography >}})