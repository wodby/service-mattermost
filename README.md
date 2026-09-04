# Mattermost service for Kubernetes on Wodby

Run Mattermost Team Edition on Kubernetes with Wodby.

This repository defines the reusable Mattermost service manifest. It uses the
official Mattermost Team Edition image and Wodby's generic stateful Helm chart.

- [Browse Wodby services](https://wodby.com/services)
- [Wodby service documentation](https://wodby.com/docs/2.0/services/)
- [Service manifest reference](https://wodby.com/docs/2.0/services/template/)

## Service overview

| Property | Manifest configuration |
| --- | --- |
| Service name | `mattermost` |
| Type | Application service |
| Versions | Mattermost `11.7` ESR |
| Database | Required PostgreSQL link |
| HTTP endpoint | Port `8065`; HTTPS redirect; WebSocket-safe route timeouts |
| Persistent storage | 20 GB by default for files, configuration, and plugins |
| Email | Optional SMTP service link |
| Scaling | Fixed to one replica |
| Calls | Disabled; Kubernetes Calls requires a separately managed RTCD service |

The official image is currently published for `linux/amd64`. The service must
therefore be scheduled on an x86-64 Kubernetes node.

## Storage and backups

The default volume uses local Kubernetes persistent storage and is intended for
simple, single-replica installations. Back up both the PostgreSQL database and
the Mattermost volume together.

Mattermost recommends S3-compatible object storage or shared network storage
for production and highly available installations. Configure an external file
store before enabling multiple application replicas; this service deliberately
does not expose ordinary horizontal scaling.

## Upload limits

Mattermost's file-size limit is set to 100 MB and the Wodby route accepts
requests up to 128 MiB. If the application limit is increased, update the
effective Wodby route `request_body_size` setting as well.

## Use this service

Use this service through the [Mattermost application stack](https://github.com/wodby/stack-mattermost),
or reference `mattermost` from a custom Wodby stack. The stack must provide a
compatible PostgreSQL service through the required `postgres` link.

A service is a reusable component and does not deploy by itself. The stack
defines its links and relationship to the rest of the application.

## Maintain a custom version

1. Fork this repository.
2. Edit `service.yml`.
3. Import the repository as a [Git-backed service](https://wodby.com/docs/2.0/services/create/#create-a-git-backed-service).
4. Reference the service from a stack manifest.

Keep service, workload, container, endpoint, link, and volume names stable
unless dependent stacks and app-level overrides are updated at the same time.

Validate the manifest with:

```bash
wodby service validate-manifest service.yml --org <org-id>
```

See the [service manifest reference](https://wodby.com/docs/2.0/services/template/)
and the [managed services index](https://github.com/wodby/services).
