# [LogForge 🛠️](https://log-forge.github.io/logforgeweb/)

Self-hosted service monitoring dashboard for Docker containers, with notifications.

Built for developers who want **simple** log visibility, service crash detection, and real-time alerts — without needing to stitch together multiple heavy tools.
<p align="center">
  <img src="https://raw.githubusercontent.com/log-forge/logforgeweb/main/assets/logforge-overview.gif" alt="LogForge Overview">
</p>

Speed up dev cycles, fix faster. Ship faster.

**Ditch the SSH+logs+grep cycle**


---

## 🚀 Quick Start

```bash
git clone https://github.com/log-forge/logforge.git
cd logforge
docker compose pull
docker compose up -d
```
Then open: http://localhost:3000

Default container names are logforge-frontend, logforge-backend and logforge-notifier.
Default ports are 3000 (frontend), 8000 (backend) and 8083/8085 (notifier).
If you want to change them, edit the .env file.

```
# Sets the name for the container that will run the backend service.
# This name is used to identify the container
BACKEND_SERVICE_CONTAINER_NAME="logforge-backend"
# Specifies the port number the backend application inside the container listens on for incoming connections.
BACKEND_SERVICE_PORT="8000"
# Defines the port number on your host machine (your computer) that will forward traffic to the BACKEND_SERVICE_PORT inside the container.
# In this case, accessing http://localhost:8000 on your machine will connect to port 8000 inside the backend container.
EXPOSED_BACKEND_PORT="8000"

# Sets the name for the container running the frontend service.
FRONTEND_SERVICE_CONTAINER_NAME="logforge-frontend"
# Specifies the port number that the frontend application (the web server) listens on inside its container.
FRONTEND_SERVICE_PORT="3000"
# Defines the port number on your host machine that maps to the FRONTEND_SERVICE_PORT inside the container.
# Accessing http://localhost:3000 on your machine will connect to port 3000 inside the frontend container.
EXPOSED_FRONTEND_PORT="3000"

# Sets the name for the container running the notifier service
NOTIFIER_SERVICE_CONTAINER="logforge-notifier"
# Specifies the port number the notficaiton service uses to send notfications
NOTIFIER_PLAIN_PORT="8083"
# Defines the port number the norfication service uses for the web UI
NOTIFIER_WEB_PORT="8085"

# Sets the name for rhe container running the auto-update service
AUTOUPDATE_SERVICE_NAME="logforge-autoupdate"
```

\* Then go to the correct port you set in .env for the frontend

## ✨ Features
- Service auto-detection (Docker containers)
- Service status (Running, Crashed, Stopped)
- Log streaming and filtering
- Customizable alert keywords — monitor Internal Docker services
- Notification support for Email, Discord, Telegram, Slack and Gotify
- Interactive terminal access per container
- File system viewer to browse container files
- Easy Docker-based deployment
- Self-hosted, lightweight — built with dev environments in mind

---
## Why LogForge?
LogForge was built for developers who need a simple, lightweight way to monitor services during development — without setting up huge production systems.
See how we [compare](https://log-forge.github.io/logforgeweb/#compare) with other popular tools.

LogForge is optimized for ***developer environments***:

🔹 Instantly see which services are running, crashed, or stopped

🔹 View and filter logs per service

🔹 Get alerts when internal services show errors or warnings based on keywords you set

🔹 Deploy everything in seconds with a single command

Built for dev environments, not production — fast, simple, and focused. No complex setup. No huge monitoring stacks.

Deploy in seconds with one simple command.

### Need LogForge for your team?
We got you  — see *[Premium.](https://log-forge.github.io/logforgeweb/#premium)*

## 🤝 Contributing

We welcome contributions that make LogForge better for fast-moving dev teams.

### What you can help with:
- Improving alerting logic or performance
- Enhancing container/service detection
- Adding support for non-Docker environments
- UI/UX polish
- Bug fixes, docs, or typo cleanups

We review all PRs quickly.  
If it’s useful to you, it’s likely useful to others too!

---

🙋‍♀️ **Have an idea or missing feature?**  
Open a GitHub issue and tell us what you’d love to see.

## License

AGPLv3

See the [LICENSE](./LICENSE) file for full details on usage and restrictions.
