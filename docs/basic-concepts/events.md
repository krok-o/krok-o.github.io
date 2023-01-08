---
sidebar_position: 4
---

# Events

Events come from providers. For example, `push` event from GitHub or GitLab. These are passed on to commands as they are
named and received from the provider. For example, this would be `issue_comment` in case of GitHub but would be `note`
event type in case of GitLab. It's up to the command to differentiate between them and act accordingly.

## Subscribe to Events

When a Repository is created it can list what types of events it Subscribes to.

## How to find out what's going on

To track what events occurred for a Repository, inspect the Repository Kubernetes object's `Status` field.
It should contain a list of Events that occurred for it.

## Getting pod logs
