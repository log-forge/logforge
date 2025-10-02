# [LogForge 🛠️](https://log-forge.github.io/logforgeweb/)

Self-hosted monitoring, alerting and remediation for Docker containers. Live logs, rules-based alerts, and safe automation — no heavy stack to stitch together.

Built for developers who want clear visibility, fast incident detection, and guardrailed remediation.
<p align="center">
  <img src="https://raw.githubusercontent.com/log-forge/logforgeweb/main/assets/logforge-overview.gif" alt="LogForge Overview">
</p>

Visibility that acts: alerts and safe auto-fix.

**Ditch the SSH+logs+grep cycle**

---

## 🚀 Quick Start - v2.0.0

```bash
git clone https://github.com/log-forge/logforge.git
cd logforge
docker compose up -d --build
```
Then open: http://localhost:3008

Default container names are logforge-frontend, logforge-backend, logforge-alert-backend, logforge-alert-frontend, logforge-autoupdate and logforge-notifier.
Default ports are 3008 (logforge-core frontend), 8087 (logforge-notifier) and 3033 (logforge-alertengine).
If you want to change them, edit the `.env` file.

```
# Container names (change names here)
LOGFORGE_BACKEND_CONTAINER_NAME=logforge-backend
LOGFORGE_FRONTEND_CONTAINER_NAME=logforge-frontend
ALERT_ENGINE_BACKEND_CONTAINER_NAME=logforge-alert-backend
ALERT_ENGINE_FRONTEND_CONTAINER_NAME=logforge-alert-frontend
AUTOUPDATE_SERVICE_NAME=logforge-autoupdate
NOTIFIER_SERVICE_CONTAINER_NAME=logforge-notifier

# Host ports exposed by default (Select what ports the UI open to on your machine)
LOGFORGE_FRONTEND_PORT=3008
NOTIFIER_WEB_PORT=8087
ALERT_ENGINE_FRONTEND_PORT=3033

#auto-update, if you want to disable auto-updates, change to "false"
AUTO_UPDATE=true
```

Then go to the correct port you set in .env for the frontend (`LOGFORGE_FRONTEND_PORT`)

\* Note: We **strongly** suggest you leave `AUTO_UPDATE` as `true`

## ✨ Features
- Service auto-detection (Docker containers)
- Service status (Running, Crashed, Stopped)
- Grouping — Organize containers into logical groups for easy monitoring
- Per-container monitoring toggle — Choose which containers to monitor or ignore
- Log streaming and filtering
- Customizable alert keywords — monitor Internal Docker services
- Notification support for Email, Discord, Telegram, Slack and Gotify + more
- Interactive terminal access per container
- File system viewer to browse container files
- Easy Docker-based deployment
- One‑click Rule Templates: Ready‑made rules for stability, performance, logs, and security — customize in seconds.
- Real‑Time Alerts: Live updates flow into the UI instantly (no refresh needed).
- Safe Auto‑Remediation: Restart/stop/kill/start/run scripts with built‑in cooldowns, backoff, and rate limits.
- Multi‑Step Actions: Chain actions with optional delays (e.g., notify, then restart).
- Scoped Rules: Target all containers, specific ones, or groups for precise control.
- Alert History & Acknowledgement: Track past alerts and hide acknowledged ones until new activity.
- Noise Controls: Case sensitivity, AND/OR keyword matching, and ignore lists to reduce false alarms.
- Duplicate Rule Protection: Warns when a new rule would overlap an existing one.
- Script Actions (Validated): Run container scripts for fixes; built‑in checks help prevent misfires.
- Test Notifications: Send a test alert from the UI to verify delivery in seconds.
- Health & Self‑Checks: Built‑in liveness/health endpoints keep the system trustworthy.
- Privacy by Design: Fully self‑hosted; your logs and alerts stay in your environment.

# Alert Engine
Rules, notifications, and automation. All in the browser. Configure everything in the UI.

Alert Engine turns signals into action: define rules for logs, metrics, and container events, then notify your channels or remediate safely with restart, stop, kill, start, or scripts. Built‑in cooldowns, backoff, rate limits, and verification delays keep automation under control.

### ✨ Features
- Fully UI-driven. No complex YAML. No scripting.
- One‑click Rule Templates: Stability, performance, logs, and security — customize in seconds.
- Triggers you control: Keywords, container events (start/stop/crash), and performance thresholds over time.
- Timelines: “If N times in M minutes” or “sustained for X minutes” — reduce noise, catch real problems.
- Safe auto‑remediation: Restart/stop/kill/start/run script with verification delays and guardrails.
- Scoped rules: Target all containers, specific ones, or groups for precise control.
- Real‑time updates: Alerts and container changes stream live into the UI.
- Acknowledge & history: Track what happened; hide acknowledged alerts until new activity.
- Test notifications: Verify delivery from the UI in seconds.
- Duplicate protection: Warns when a new rule would overlap an existing one.
- Alert History & Stats, Get graphs and timeline events
  
## Why LogForge?
LogForge is built for modern teams running Docker — from local development to production environments. Start with simple, self‑hosted visibility and grow into rules‑based alerting and safe automation. Real‑time updates stream into the UI, notifications go to your channels, and guardrails that prevent runaway loops in production.



From local dev to production, without heavy tooling.

🔹 See at a glance which services are running, crashed, or stopped.

🔹 View and filter logs per container to spot issues fast.

🔹 Get real‑time alerts based on rules you set and ready‑made rule templates.

🔹 Deploy in seconds with Docker Compose and keep everything self‑hosted.

🔹 See how we [compare](https://log-forge.github.io/logforgeweb/#compare) with other tools

Built for devs/teams — fast, simple, and focused. No complex setup. No huge monitoring stacks.

Deploy in seconds with one simple command.

### Need LogForge for your team?
We got you  — see *[Premium.](https://log-forge.github.io/logforgeweb/#premium)*

- Remote server monitoring
  
- Enterprise Grade tooling

- Docker Swarm support

- Kubernetes support

- Role-based Access Control (RBAC)

- Centralized team management

## Source‑Available (Core)
The [LogForge Core](https://github.com/log-forge/logforge-core) backend interfaces directly with the Docker socket (`/var/run/docker.sock`). For transparency and safety, Core is source‑available so you can review exactly what runs with that level of access.

\* Note: `logforge-autoupdate` also uses the socket, it is just [watchtower](https://github.com/containrrr/watchtower) with custom scheduling settings.

Non‑Core components (Alert Engine, Notifier, and other tools) are proprietary/restricted. See LICENSING.md for details.
## 🤝 Contributing

We welcome contributions that make LogForge better.

### What you can help with:
- Enhancing UI experience
- UI/UX polish
- Bug fixes, docs, or typo cleanups

---

🙋‍♀️ **Have an idea or missing feature?**  
Open a GitHub issue and tell us what you’d love to see.

If it’s useful to you, it’s likely useful to others too!
## License

See the [LICENSE](./LICENSE) file for full details on usage and restrictions.
