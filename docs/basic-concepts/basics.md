---
sidebar_position: 1
---

# Basic Concepts

Here you find a description of Krok itself, what it does, and how it works in general terms.

## What is Krok?

Krok handles [events](./events.md) from various [providers](./providers.md) that the user can set up. What the hell does this mean?

Suppose the following scenario. You have several repositories across multiple services such as, _GitHub_, _Gitlab_,
_Gitea_, _Bitbucket_ etc. You would like to have a consistent way of handling events like `push`, `pull`,
`issue_comment` etc, similarly for all of those repositories. However, you don't want to go and set all the same things
up for each of them separately.

Even if you have only a single provider, like GitHub, if you have multiple repositories it can be a drag to go and
add the same GitHub action to each repository with slight modifications here and there.

Krok offers a way to abstract this burden. Let's see how it does that.

## How does it work?

Krok uses a Kubernetes [operator](https://github.com/krok-o/operator/). It has three main building blocks.
[repositories](./repositories.md), [events](./events.md) and [commands](./commands.md). You can read more about those in their respective guides.

### TL;DR

A repository has a one to one mapping with hooks. A hook is a webhook that Krok creates on these repositories using the
configured platforms API to create a webhook. It generates a unique URL based on it's id and name of the repository
which is then used as a call-back for the webhook. The secret is also configurable.

The user can configure a domain name for the base of this webhook. The operator runs a server which handles call-backs
for any configured event and creates something called a `KrokEvent` which then launches the configured Commands.

Commands can have dependencies and can use output from other commands.

Once a repository with commands like Slack-Commenter or Issue-Commenter are configured, the command will execute with
the necessary creds and perform its function.
