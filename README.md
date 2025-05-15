# [LogForge 🛠️](https://log-forge.github.io/logforgeweb/)

Self-hosted service monitoring dashboard for Docker containers.

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
docker compose up -d --build
```
Then open: http://localhost:3000

Default container names are logforge-frontend and logforge-backend.
Default ports are 3000 (frontend) and 8000 (backend).
If you want to change them, edit the .env file.

```
# Sets the name for the container that will run the backend service.
# This name is used to identify the container
BACKEND_SERVICE_CONTAINER_NAME=logforge-backend
# Specifies the port number the backend application inside the container listens on for incoming connections.
BACKEND_SERVICE_PORT=8000
# Defines the port number on your host machine (your computer) that will forward traffic to the BACKEND_SERVICE_PORT inside the container.
# In this case, accessing http://localhost:8000 on your machine will connect to port 8000 inside the backend container.
EXPOSED_BACKEND_PORT=8000

# Sets the name for the container running the frontend service.
FRONTEND_SERVICE_CONTAINER_NAME=logforge-frontend
# Specifies the port number that the frontend application (the web server) listens on inside its container.
FRONTEND_SERVICE_PORT=3000
# Defines the port number on your host machine that maps to the FRONTEND_SERVICE_PORT inside the container.
# Accessing http://localhost:3000 on your machine will connect to port 3000 inside the frontend container.
EXPOSED_FRONTEND_PORT=3000
```

\* Then go to the correct port you set in .env for the frontend

## ✨ Features
- Service auto-detection (Docker containers)
- Service status (Running, Crashed, Stopped)
- Log streaming and filtering
- Customizable alert keywords — monitor Internal Docker services
- Easy Docker-based deployment
- Self-hosted, lightweight — built with dev environments in mind

---
## Why LogForge?
LogForge was built for developers who need a simple, lightweight way to monitor services during development — without setting up huge production systems.

LogForge is optimized for ***developer environments***:

🔹 Instantly see which services are running, crashed, or stopped

🔹 View and filter logs per service

🔹 Get alerts when internal services show errors or warnings based on keywords you set

🔹 Deploy everything in seconds with a single command

Built for dev environments, not production — fast, simple, and focused. No complex setup. No huge monitoring stacks.

Deploy in seconds with one simple command.
## 🤝 Contributing

We welcome contributions that make LogForge better for fast-moving dev teams.

### What you can help with:
- Improving alerting logic or performance
- Enhancing container/service detection
- Adding support for non-Docker environments
- UI/UX polish
- Bug fixes, docs, or typo cleanups

### How to contribute:
1. Fork the repo
2. Create a new branch: `git checkout -b my-feature`
3. Make your changes
4. Commit and push: `git push origin my-feature`
5. Open a pull request

We review all PRs quickly.  
If it’s useful to you, it’s likely useful to others too!

---

🙋‍♀️ **Have an idea or missing feature?**  
Open a GitHub issue and tell us what you’d love to see.

## License

AGPLv3

See the [LICENSE](./LICENSE) file for full details on usage and restrictions.
