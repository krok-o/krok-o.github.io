---
sidebar_position: 2
---

# Repositories

Repositories are the first, main building block of Krok.

## What are Repositories

Repositories are the repositories on any supported [provider](./providers.md). For example, a repository on GitHub.
Once a user configures one in Krok, more about that in [creating a repository](#creating-a-repository), Krok
will go ahead and create a WebHook for the requested events such as `push`, `pull` etc.

Once a hook is successfully set up, Krok will listen for those events under a custom URL and run the configured
[commands](./commands.md). For example, let's suppose the user has a requirement that every repository must send a Slack message
after certain events into a specific channel of a company Slack space.

The user would configure the repositories to run this specific command on each subscribed event. But let's see some
concrete configuration files.

## Creating a repository

A sample repository setting for [this](https://github.com/Skarlso/test) GitHub repository could look something like this:

```yaml
apiVersion: delivery.krok.app/v1alpha1
kind: KrokRepository
metadata:
  name: repository-sample
  namespace: krok-system
spec:
  authSecretRef:
    name: repository-sample-secret
    namespace: krok-system
  providerTokenSecretRef:
    name: github-secret
    namespace: krok-system
  platform: github
  url: "https://github.com/Skarlso/test"
  events:
    - push
  commands:
    - namespace: krok-system
      name: input-command
    - namespace: krok-system
      name: output-command
```

Let's break this down what each of these values do.

```yaml
  authSecretRef:
    name: repository-sample-secret
    namespace: krok-system
```

Authentication secret contains auth information on how to check out the code. This could a private key, or a token or a
password. Anything that the respective platform requires. For example, something like this:


```yaml
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: repository-sample-secret
  namespace: krok-system
data:
  secret: c2VjcmV0
  username: dXNlcm5hbWU=
  password: dG9rZW4=

```

Username and password are a GitHub token and a user. The `secret` value is used to create the hook.

```yaml
  providerTokenSecretRef:
    name: github-secret
    namespace: krok-system
```

The provider token allows Krok to access the provider's API to create the hook.

```yaml
  platform: github
  url: "https://github.com/Skarlso/test"
  events:
    - push
```

These are the configured platform of this repository, the url where it is located at and the subscribed events the hook
should listen on.

And last but not least, a list of command object references:

```yaml
  commands:
    - namespace: krok-system
      name: input-command
    - namespace: krok-system
      name: output-command
```

These are commands that already exist in the cluster. _Continue_ to read more about commands.
