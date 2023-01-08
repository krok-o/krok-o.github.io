---
sidebar_position: 5
---

# Providers

Providers are the platform that Krok supports such as GitHub, GitLab, Gitea.

## Providers in Commands

To be truly provider agnostic, the commands that Krok runs also must support multiple providers. After all, all
providers have different ways of doing things. A command can choose to only support a single provider.

The platform is passed in as a parameter so the command can decide what to do based on that. For example, call a
different implementation.

## Providers in Repositories

Repositories need to define which provider they are locate on like this:

```yaml
platform: github
```

This is needed in order for the operator to know how to create a Hook on the platform. What endpoint to call, etc.
For this to work, a single Secret must be defined which contains a token for said provider such as:

```yaml
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: github-token
  namespace: krok-system
data:
  token: c2VjcmV0
```

This must be defined in the Repository settings like this:

```yaml
spec:
  providerTokenSecretRef:
    name: github-token
    namespace: krok-system
```
