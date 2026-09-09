<p align="center">
  <a href="https://www.logforge.dev/">
    <img src="https://www.logforge.dev/assets/logforge-Icon-transparent-512x512.png" alt="LogForge" width="120" />
  </a>
</p>

<p align="center">
  <a href="https://www.logforge.dev/"><strong>LogForge</strong></a>
</p>

# LogForge Unicron

### Access from another device

Before first startup, set `UNICRON_CENTRAL_FQDN` in `.env` to the server's
reachable DNS hostname (for example `logs.example.com`). Configure your DNS
to resolve that name to the Docker server, then open
`https://logs.example.com:8444/unicron/` (use your configured app port).
Compose also maps this name to loopback inside the appliance for internal CA
communication; this does not restrict the published host ports.

The appliance issues its own certificate. Trust its root CA on client devices
to validate HTTPS. Merely adding a name to `TRAEFIK_ROUTER_HOSTS` does not
update the certificate. Existing installations require an appliance release
supporting hostname-change certificate reissuance before changing this setting.
Do not delete the data volume or certificate authority to change the hostname.

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

**Open Source:** Unicron source is available at
[log-forge/unicron-source](https://github.com/log-forge/unicron-source).

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

### Proxmox: Linux VM quick start

This walkthrough runs Docker **inside a Linux VM** to keep application services
separate from the Proxmox host. Use a Linux distribution supported by Docker
Engine and an `amd64` or `arm64` environment supported by the LogForge image.
Allow enough disk space for the logs you plan to retain.
Connect the VM to a network reachable from your browser (for example your LAN
bridge), give it a stable IP, and install Git plus
[Docker Engine and the Compose plugin](https://docs.docker.com/engine/install/)
using the instructions for your distribution.

**1. Get the deployment files.** Run these commands in the Linux VM's terminal:

```sh
git clone https://github.com/log-forge/logforge.git
cd logforge
```

**2. Set the address.** Open the existing `.env` file in a text editor and change
these entries before starting LogForge. Keep the other settings unchanged:

```dotenv
UNICRON_CENTRAL_FQDN=logs.example.com
UNICRON_APP_PORT=8444
UNICRON_AGENT_MTLS_PORT=9443
```

`logs.example.com` is a placeholder, not a working address. Replace it with a
name configured in your LAN DNS to point to the **Linux VM's IP**, not the
Proxmox host's IP. That name must resolve from every device accessing LogForge.
Use only the hostname in
the setting, without a scheme, port or path. No Compose edit is needed.
Allow TCP `8444` from trusted browser clients through any Proxmox/network
firewalls; allow TCP `9443` from remote agents if used. Use your configured
ports if different, and prefer LAN/VPN access over public exposure.

**3. Start LogForge.** Run these commands from the cloned `logforge` directory:

```sh
sudo docker compose pull
sudo docker compose up -d
sudo docker compose ps unicron
```

Wait for the appliance to report `healthy`. On first startup, find the generated
admin password in `sudo docker compose logs unicron`. Keep those logs private;
the default username is `admin`, and first login requires a password change.

**4. Open it from your browser.** Use `https://logs.example.com:8444/unicron/`,
replacing the example name with the one you configured. `localhost` on your
other device refers to itself, not the VM.

A certificate warning is expected until your client trusts the appliance's CA.
After startup, run this in the VM to export its **public** root certificate:

```sh
sudo docker cp unicron-appliance:/var/lib/unicron/pki/trust/root_ca.crt ./logforge-root-ca.crt
```

Copy that certificate to your browser device through a trusted connection and
import it using your OS/browser's trusted root CA procedure. Only trust a CA
from an appliance you control; never copy or share its private keys.

For an existing install, pull the updated image and recreate the service after
editing `.env`; **keep the data volume**. The appliance updates its server
certificate while preserving its CA and database. A 404 can indicate that the
browser hostname does not match the configured name; a timeout calls for checking
the VM address, published port and firewall path.

Tested configuration: Proxmox → Debian 13 VM → Docker, with 2 vCPUs and 4 GiB
RAM (a tested setup, not a sizing guarantee or a Debian requirement). Fresh
startup, HTTPS/login from outside the VM, hostname change and restart passed.
Other distributions were not tested. An agent in another VM is a
**remote agent**: use a reachable Central address and remote enrollment, not the
same-host Docker network alias. Cross-VM agent enrollment and raw-IP certificate
access were not part of this test.

#### Running directly on the Proxmox host

Direct-host deployment also passed our local browser/API test with Proxmox VE
9.2.18 (kernel 7.0.14-16-pve) and Docker Engine 29.8.0 from Docker's official
Debian repository. Follow the same deployment steps on the host, but point your
DNS name to the **Proxmox host's IP** and select unused application ports.
Keep Proxmox's own management port separate.

On this host, the older Debian-packaged Docker 26.1.5 failed because AppArmor
blocked PostgreSQL Unix sockets. Updating Docker resolved the failure with the
default AppArmor profile still enabled; do not disable AppArmor as a shortcut.
Review Docker's installation instructions and existing workloads before changing
packages on a production host. Docker also changes host firewall rules.

The appliance mounts the Docker socket, giving it control over the host's Docker
workloads. A separate Linux VM provides stronger separation from your hypervisor. Our
test confirmed direct-host login and API access while an existing VM remained
reachable; it is not a guarantee for every Proxmox version or firewall setup.

### Unraid Community Apps

Install **LogForge Unicron** from the Unraid Apps tab. The template stores
durable state under `/mnt/user/appdata/logforge-unicron`, publishes the browser
UI on HTTPS port `8444`, and publishes the agent mTLS endpoint on port `9443`.
If either host port changes during installation, update its matching advanced
`Public` port variable to the same value.

The container uses an internally generated certificate authority. Your browser
may show a certificate warning until you trust that authority or configure a
trusted reverse proxy.

The Community Apps template starts the appliance on Unraid's normal bridge
network. Before enrolling an agent for the same Unraid host, create the shared
network and connect the running appliance with its expected DNS alias:

```sh
docker network inspect logforge-unicron-network >/dev/null 2>&1 || \
  docker network create logforge-unicron-network
docker network inspect logforge-unicron-network \
  --format '{{range .Containers}}{{println .Name}}{{end}}' | \
  grep -Fxq LogForge-Unicron || \
  docker network connect \
    --alias unicron.central \
    logforge-unicron-network \
    LogForge-Unicron
```

Then choose the local-host enrollment option in Unicron. Its generated agent
command joins `logforge-unicron-network` and reaches the appliance through
`https://unicron.central/unicron`.

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
