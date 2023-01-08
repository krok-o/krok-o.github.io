---
sidebar_position: 3
---

# Commands

Commands are the second most important building block in Krok. They come right after repositories. Commands define the
actual commands that can be executed on an event. They have a lot of configuration so let's go through each.

## What are commands

Commands define executable actions which can be taken for an event. For example, a Slack-Notification commands can send
any kind of configured slack message based on an event. Let's look at how that looks like.

### Command configuration

This is the anatomy of a command configuration:

```yaml
apiVersion: delivery.krok.app/v1alpha1
kind: KrokCommand
metadata:
  name: slack-command
  namespace: krok-system
spec:
  enabled: true
  image: localhost:5001/krok-o/slack-notification:v0.0.2
  platforms:
    - github
  readInputFromSecrets:
    - name: slack-secret
      namespace: krok-system
```

What's happening here then? `enabled` is self explanatory. If the command is disabled, it won't be executed.

_Note_: This might change to `suspended` in the future to not have to define this attribute on every command.

`platforms` is a list of platforms that this command can support. This means, that this command understands each
platform's payload and can call the right method to get the right data it would need to execute on. It's also possible
that the command just simply doesn't care because what it does is not dependent on a platform. Such as, just sending the
name and the event type to a slack channel.

`readInputFromSecrets` defines a list of secrets that the user can configure ( or other commands might create secrets
as output which depending commands can read input from ) from which key-value pairs are then passed over to the command
as arguments.

For example:

```yaml
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: wait-command-args
  namespace: krok-system
data:
  channel: MzBz
  # Slack token, not provider token
  token: MzBz
  message: MzBz
```

The command will read these and pass them as arguments. The command might also declare dependencies.

```yaml
apiVersion: delivery.krok.app/v1alpha1
kind: KrokCommand
metadata:
  name: slack-command
  namespace: krok-system
spec:
  enabled: true
  image: localhost:5001/krok-o/slack-notification:v0.0.2
  platforms:
    - github
  readInputFromSecrets:
    - name: slack-secret
      namespace: krok-system
  dependencies:
    - setup-command
    - generate-artifact-command
```

These dependencies then need to be added to the repository object so they can execute and pass over any information they
declare to the next command.

## How to write a command

So, what does a command look like? You can find all commands written by Krok [here](https://github.com/krok-o/commands).
_Note_: At the time of this writing there are only a handful of commands available to choose from.

### SDK

For Go commands, there is a convenience SDK provided by Krok to extract information from various payload types. This SDK
can be found [here](https://github.com/krok-o/command-sdk). Krok will expand this as it matures.

### What language to use

Virtually any language can be used that can produce an executable and get understand basic command line options.

There are some [required arguments](#required-arguments) that the application must be able to take, otherwise, anything
can be used. The [Commands](https://github.com/krok-o/commands) repo contains a few examples.

### Required arguments

The following arguments are required by every command to be set:

- `payload` -> this contains the payload received from the platform
- `artifact-url` -> if the event involves the need for the source code ( like a push or a merge ) this url will point
to a unique, in-cluster server from which the source can be downloaded
- `event-type` -> the involved event ( `push`, `pull`, etc )
- `platform` -> the configured platform on which the event happened ( `github`, `gitlab`, `gitea`, etc )

The command sdk for Go commands contains a convenience method for adding these flags called `AddRequiredFlagsToCommand`.

### Platform Support

The command has to declare what platform it supports. If the command declares that it support `github` and it is run on
`gitea` it will report a failure and then skip this command.