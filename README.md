# LogForge 🛠️

Self-hosted service monitoring dashboard for Docker containers.

Built for developers who want **simple** log visibility, service crash detection, and real-time alerts — without needing to stitch together multiple heavy tools.

Speed up dev cycles, fix faster. Ship faster.

**Ditch the SSH+logs+grep cycle**


---

## 🚀 Quick Start

```bash
git clone https://github.com/madanb13/log-forge.git
cd log-forge
docker compose up -d
```
Then open: http://localhost:3000

Default ports are 3000 (frontend) and 8000 (backend).
If you want to change them, edit the .env file.

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

## License

You may use the software for **personal, non-commercial purposes** only. You cannot modify, redistribute, or use it for any **commercial purposes** unless you obtain a **commercial license**.

See the [LICENSE](./LICENSE) file for full details on usage and restrictions.
