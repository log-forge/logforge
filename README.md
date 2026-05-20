# LogForge Unicron
Self-hosted monitoring, alerting and remediation for Docker containers. Live logs, rules-based alerts, and safe automation — no heavy stack to stitch together.

Visibility that acts: alerts and safe auto-fix.

**Ditch the SSH+logs+grep cycle**

Learn more on the [LogForge website](https://www.logforge.dev/).

## Quick Start - v3.0

Clone the repository, enter it, then start Unicron from the published Docker Hub
images:

```sh
git clone https://github.com/log-forge/logforge.git
cd logforge
docker compose pull
docker compose up -d
docker compose ps
```

Open:

```text
https://localhost:8444/unicron
```
## Applying Configuration Changes

If you want to change Unicron settings, edit the `.env` file with the values you
want:

```env
# Host ports exposed by Unicron
UNICRON_APP_PORT=8444
UNICRON_AGENT_MTLS_PORT=9443

# Local admin account
CENTRAL_ADMIN_USERNAME=admin
CENTRAL_ADMIN_PASSWORD=
CENTRAL_ADMIN_RECOVERY_OVERRIDE=false
```

Then recreate Unicron so Docker Compose reads the updated values:

```sh
docker compose up -d
docker compose ps
```

Then open Unicron on the port you set with `UNICRON_APP_PORT`:

```text
https://localhost:${UNICRON_APP_PORT}/unicron
```

## Features

- Free self-hosted Docker monitoring for local containers, with limited remote
  Docker monitoring through Unicron-agent.
- Unicron-agent enrollment for remote Docker hosts within free-version limits.
- Container health, status, logs, and metrics in one UI.
- Log and metrics ingestion with storage for review, troubleshooting, and alert
  rules within free-version limits.
- Custom rules for logs, metrics, and container events.
- Ready-made rule templates for stability, performance, logs, and security.
- Real-time alerts, alert history, acknowledgement, and delivery tracking.
- Notifications through Email, Slack, Teams, Discord, Telegram, Gotify, and
  webhooks.
- Safe automation and remediation: restart, stop, start, kill, or run scripts
  with cooldown, rate-limit, and backoff style controls.
- Secure admin access with local authentication and session protection.
- mTLS-protected agent enrollment and agent traffic.
- Encrypted sensitive configuration and redaction patterns where Unicron
  handles credentials.
- Self-hosted privacy, so Docker monitoring data stays in your environment.

# Alert Engine
Rules, notifications, and automation. All in the browser. Configure everything in the UI.

Alert Engine turns signals into action: define rules for logs, metrics, and container events, then notify your channels or remediate safely with restart, stop, kill, start, or scripts. Built‑in cooldowns, backoff, rate limits, and verification delays keep automation under control.
## Why LogForge Unicron?

LogForge Unicron is built for developers, small teams, and self-hosters who
want Docker monitoring without a heavy observability stack. Instead of jumping
between SSH sessions, ad hoc log searches, and manual container checks, you get
container monitoring, limited remote Docker monitoring, log ingestion, metrics
storage, alerting, safe automation, and guarded remediation in one self-hosted
product.

Stored logs and metrics make it easier to investigate incidents, review trends,
and tune custom rules after the moment has passed. Real-time alerts,
notifications, acknowledgement, and delivery tracking help teams detect issues
faster, while remediation actions help close the loop with less manual
follow-up.

Secure admin access, mTLS-protected Unicron-agent traffic, encrypted sensitive
configuration, and self-hosted deployment keep remote visibility practical
without giving up control of Docker monitoring data.

### Need LogForge for your team?

We got you — see [Premium](https://www.logforge.dev/#premium).

- Remove remote monitoring limits
- Enterprise-grade tooling
- Docker Swarm support
- Kubernetes support
- Role-based Access Control (RBAC)
- Centralized team management
- Remove/configure 7 day logs/metrics retention period
- Local AI, connect your AI models to LogForge Unicron and allow for it to fix containers, respond to alerts, create reports. Configurable scoped access to logs/metrics/alerts/containers/hosts and notfications

## Remote Monitoring with Unicron-agent

Unicron does not create access to remote devices by itself. To monitor a remote
Docker host, you must already have SSH/admin access or another working way to
run Docker commands on that host.

The remote host must run Docker and must be able to reach Unicron's published
mTLS endpoint on `UNICRON_AGENT_MTLS_PORT`. In Unicron, generate the
Unicron-agent enrollment command, then copy and run that command on the remote
host.

`localhost` only works for agents running on the same machine as Unicron. For
remote hosts, use a Unicron address that the remote host can reach in the
enrollment flow.

## Admin Account and Recovery

`CENTRAL_ADMIN_PASSWORD=` is blank by default. On first boot, `central/auth`
generates a random admin password, logs it once, and requires a password change
after sign-in.

Set `CENTRAL_ADMIN_PASSWORD` only when you want a fixed first-boot password or
when using recovery mode. After the admin account exists, normal restarts keep
the same durable auth database and preserve the existing credential when
`CENTRAL_ADMIN_RECOVERY_OVERRIDE=false`.

`CENTRAL_ADMIN_RECOVERY_OVERRIDE` accepts only `true` or `false`. Any other value
is invalid.

The admin username is part of the durable auth database. After the account
exists, changing `CENTRAL_ADMIN_USERNAME` without recovery mode is treated as a
configuration error. Restore the existing username or use the recovery flow
below.

Changing `CENTRAL_ADMIN_PASSWORD` while
`CENTRAL_ADMIN_RECOVERY_OVERRIDE=false` does not rotate the existing password.
This protects a running deployment from accidental credential replacement.

To recover or rotate the local admin credential:

1. Set `CENTRAL_ADMIN_USERNAME` to the desired local admin username.
2. Set `CENTRAL_ADMIN_PASSWORD` to the replacement password.
3. Set `CENTRAL_ADMIN_RECOVERY_OVERRIDE=true`.
4. Recreate the Unicron container with `docker compose --env-file .env up -d`.
5. Sign in and complete the required password-change flow.
6. Set `CENTRAL_ADMIN_RECOVERY_OVERRIDE=false` again.
7. Recreate the Unicron container again.

Recovery mode marks the admin profile with `requiresPasswordChange=true`, so the
replacement password is temporary until the first successful sign-in.

## Ports

Unicron publishes two public host ports:

- `UNICRON_APP_PORT`: browser and API access to `/unicron`
- `UNICRON_AGENT_MTLS_PORT`: Unicron-agent mTLS enrollment and traffic

Both values must be non-empty integer TCP ports from `1` through `65535`. Do not
include protocols, hostnames, paths, spaces, or port ranges.

To change the browser/API port, change `UNICRON_APP_PORT` in `.env` to the
value you want.

To change the Unicron-agent mTLS port, change `UNICRON_AGENT_MTLS_PORT` in
`.env` to the value you want.

## Local Unicron-agent Containers and Persistence

Unicron does not automatically create a local Unicron-agent container. It
generates an enrollment command, and you must run that command explicitly.

The generated local Unicron-agent command uses Docker directly. A standalone
`docker run` Unicron-agent is not managed by this Compose file, so
`docker compose ps`, `docker compose restart`, and `docker compose down` only
manage Unicron.

`docker compose down` manages the `logforge-unicron-network`, not standalone
Unicron-agent containers. If unmanaged Unicron-agent containers are still
attached, network removal can fail; if the network is removed and later
recreated, those containers may need to be restarted, reconnected, or
re-enrolled.

The `unicron-data` volume stores the auth database, internal generated secrets,
CA material, and Unicron state. Deleting it resets Unicron. Existing
Unicron-agent instances enrolled against the old CA or Unicron identity will
fail and must be re-enrolled with a newly generated command.

If a Unicron-agent container or its Docker volume is deleted while
`unicron-data` is kept, re-run the current enrollment command from Unicron to
repair that Unicron-agent instance.

Container state and Unicron-agent state are separate. A Unicron-agent container
can be running while Unicron reports that Unicron-agent as offline or unhealthy.

## Health Checks

Verify Unicron container health, UI/API login, and Unicron-agent status
separately:

```sh
docker compose ps
docker compose ps unicron
docker compose logs unicron
```

Docker health reports whether the Unicron container is ready. It should fail
when required internal services cannot bootstrap, including `central-auth`.

UI reachability only confirms that the browser/API endpoint is available:

```text
https://localhost:${UNICRON_APP_PORT}/unicron
```

It does not prove that local or remote Unicron-agent instances are enrolled,
connected, or healthy. Check Unicron-agent status in the product UI/API after
confirming that login works.

For unrecoverable configuration errors, expect either a non-zero container exit
or an `unhealthy` status with an actionable log message. Fix the `.env` value,
then recreate the Unicron container.
