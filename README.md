<p align="center">
  <a href="https://www.logforge.dev/">
    <img src="https://www.logforge.dev/assets/logforge-Icon-transparent-512x512.png" alt="LogForge" width="120" />
  </a>
</p>

<p align="center">
  <a href="https://www.logforge.dev/"><strong>LogForge</strong></a>
</p>

# LogForge Unicron

Self-hosted Docker monitoring and control in one Docker Compose deployment.
Unicron is a Docker-native dashboard for local and agent-forwarded logs,
metrics, Docker events, alert rules, notifications, file access, and safe
remediation.

LogForge focuses on Docker alerts that fix themselves: rules catch operational
signals, notifiers route incidents, and guarded actions restart, stop, start,
kill, or run scripts with cooldowns, backoff, rate limits, and verification
delays. AI-assisted operations can turn live Docker context into reports,
incident investigation, and scoped remediation guidance while keeping the
control plane self-hosted.

**Source available:** Unicron source is available at
[log-forge/unicron-source](https://github.com/log-forge/unicron-source). It is
licensed separately under proprietary/source-available terms.

Learn more on the [LogForge website](https://www.logforge.dev/).

## Quick Start

### Recommended: installer

```sh
curl -fsSL https://www.logforge.dev/install.sh | sh
```

The installer clones or updates the deployment repository, checks Git, Docker,
and Docker Compose, selects usable host ports, validates that Compose resolves
to `logforge/unicron:latest`, pulls the image, updates `.env` if selected ports
need to change, and starts Unicron.

Open the URL printed by the installer:

```text
https://localhost:${UNICRON_APP_PORT}/unicron
```

The default URL is:

```text
https://localhost:8444/unicron
```

### Manual Docker

Use the manual path when you want explicit control over the checkout, `.env`,
or Compose commands:

```sh
git clone https://github.com/log-forge/logforge.git
cd logforge
docker compose pull
docker compose up -d
docker compose ps unicron
```

Then open:

```text
https://localhost:8444/unicron
```

## What You Get

The free self-hosted product includes:

- Docker container health, status, logs, metrics, events, and file access in
  one UI.
- Local Docker monitoring from the Unicron deployment host.
- Remote Docker monitoring through mTLS-protected Unicron-agent enrollment,
  traffic, and forwarded operational data.
- Log, metrics, and Docker event ingestion with storage for troubleshooting,
  review, trend checks, and alert rules.
- Custom rules for logs, metrics, and container events.
- Rule templates for stability, performance, logs, and security signals.
- Real-time alerts with history, acknowledgement, and delivery tracking.
- Notifications through Email, Slack, Teams, Discord, Telegram, Gotify, and
  webhooks.
- Safe remediation actions: restart, stop, start, kill, or run scripts with
  cooldowns, rate limits, backoff controls, and verification delays.
- Secure admin authentication with session protection.
- Encrypted sensitive configuration and credential redaction patterns where
  Unicron handles secrets.
- Self-hosted privacy so Docker monitoring data stays in your environment.

## Alert Engine

Configure rules, notifications, and automation in the browser. Alert Engine
turns logs, metrics, and container events into actions, while cooldowns,
backoff, rate limits, and verification delays keep remediation controlled.

## Cirdan

**Built on LogForge Unicron - Cirdan**

Cirdan is the AI-native observability and action layer built on top of LogForge Unicron. It is designed for a
world where AI agents, not human dashboards, become the primary readers and operators of infrastructure state.

As logs, metrics, traces, events, alerts, configs, and live state are consumed, Cirdan continuously updates graph-
based system knowledge across Docker, Kubernetes, Podman, Swarm, hosts, services, workloads, and enterprise
systems. It compresses noisy operational data into connected knowledge that the model can inspect, cite, explain,
report on, and use to act.

Cirdan can operate in advisory mode or, when explicitly allowed by a human operator, take scoped or full actions
on their behalf: setting up monitoring, investigating incidents, changing rules, running approved remediation, and
producing operational knowledge for teams and end users.

For teams building AI-operated infrastructure, see [Premium](https://www.logforge.dev/#premium).

## Operations

<details>
<summary>Configuration changes</summary>

Edit `.env` with the values you want:

```env
# Host ports exposed by Unicron
UNICRON_APP_PORT=8444
UNICRON_AGENT_MTLS_PORT=9443

# Local admin account
CENTRAL_ADMIN_USERNAME=admin
CENTRAL_ADMIN_PASSWORD=
CENTRAL_ADMIN_RECOVERY_OVERRIDE=false
```

Recreate Unicron so Docker Compose reads the updated values:

```sh
docker compose up -d
docker compose ps unicron
```

Open Unicron on the configured app port:

```text
https://localhost:${UNICRON_APP_PORT}/unicron
```

</details>

<details>
<summary>Admin account and recovery</summary>

`CENTRAL_ADMIN_PASSWORD=` is blank by default. On first boot, `central/auth`
generates a random admin password, logs it once, and requires a password change
after sign-in.

Set `CENTRAL_ADMIN_PASSWORD` only when you want a fixed first-boot password or
when using recovery mode. After the admin account exists, normal restarts keep
the durable auth database and preserve the existing credential when
`CENTRAL_ADMIN_RECOVERY_OVERRIDE=false`.

`CENTRAL_ADMIN_RECOVERY_OVERRIDE` accepts only `true` or `false`. Any other value
is invalid.

The admin username is part of the durable auth database. After the account
exists, changing `CENTRAL_ADMIN_USERNAME` without recovery mode is treated as a
configuration error. Restore the existing username or use the recovery flow.

Changing `CENTRAL_ADMIN_PASSWORD` while
`CENTRAL_ADMIN_RECOVERY_OVERRIDE=false` does not rotate the existing password.
This protects a running deployment from accidental credential replacement.

To recover or rotate the local admin credential:

1. Set `CENTRAL_ADMIN_USERNAME` to the desired local admin username.
2. Set `CENTRAL_ADMIN_PASSWORD` to the replacement password.
3. Set `CENTRAL_ADMIN_RECOVERY_OVERRIDE=true`.
4. Recreate Unicron with `docker compose up -d`.
5. Sign in and complete the required password-change flow.
6. Set `CENTRAL_ADMIN_RECOVERY_OVERRIDE=false`.
7. Recreate Unicron again with `docker compose up -d`.

Recovery mode marks the admin profile with `requiresPasswordChange=true`, so the
replacement password is temporary until the first successful sign-in.

</details>

<details>
<summary>Remote monitoring with Unicron-agent</summary>

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

</details>

<details>
<summary>Ports</summary>

Unicron publishes two public host ports:

- `UNICRON_APP_PORT`: browser and API access to `/unicron`.
- `UNICRON_AGENT_MTLS_PORT`: Unicron-agent mTLS enrollment and traffic.

Both values must be non-empty integer TCP ports from `1` through `65535`. Do not
include protocols, hostnames, paths, spaces, or port ranges.

To change the browser/API port, change `UNICRON_APP_PORT` in `.env`. To change
the Unicron-agent mTLS port, change `UNICRON_AGENT_MTLS_PORT` in `.env`.

</details>

<details>
<summary>Local Unicron-agent persistence</summary>

Unicron does not automatically create a local Unicron-agent container. It
generates an enrollment command, and you must run that command explicitly.

The generated local Unicron-agent command uses Docker directly. A standalone
`docker run` Unicron-agent is not managed by this Compose file, so
`docker compose ps`, `docker compose restart`, and `docker compose down` only
manage Unicron.

`docker compose down` manages the `logforge-unicron-network`, not standalone
Unicron-agent containers. If unmanaged Unicron-agent containers are still
attached, network removal can fail. If the network is removed and later
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

</details>

<details>
<summary>Health checks</summary>

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

</details>
