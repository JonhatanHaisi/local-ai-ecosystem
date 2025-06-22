# MCP Server Documentation

## What is MCP?
MCP stands for **Model Context Protocol**. It is a protocol and server implementation designed to manage, orchestrate, and serve AI models or related services in a modular and scalable way. Each MCP can provide a specific capability (such as time, math, or other utilities) and can be plugged into the server as needed.

## Folder Structure

- `Dockerfile`: Builds the MCP server container, installing Python, Supervisor, and required MCP packages.
- `supervisord.conf`: Supervisor configuration file that manages the lifecycle of MCP processes inside the container.

## How to Install New MCPs in the Container

1. **Edit the Dockerfile**
   - Add the desired MCP package to the `pip install` command. For example, to add `mcp-math`:
     ```dockerfile
     RUN pip install --no-cache-dir \
         uv \
         mcpo \
         mcp-server-time \
         mcp-math
     ```
   - Rebuild the Docker image:
     ```powershell
     docker compose build mcp-multi
     ```

2. **(Optional) Install MCPs at Runtime**
   - You can also install MCPs by running `pip install` inside the running container, but this is not recommended for production.

## How to Setup MCPs in Supervisor

- MCPs are managed by **Supervisor**, which is configured via `supervisord.conf`.
- To add a new MCP process:
  1. Edit `supervisord.conf` and add a new `[program:...]` section for your MCP, specifying the command to start it.
  2. Example:
     ```ini
     [program:mcp-server-time]
     command=mcpo --port 8001 -- uvx mcp-server-time --local-timezone=America/Sao_Paulo
     autostart=true
     autorestart=true
     stdout_logfile=/var/log/supervisor/mcp-server-time.log
     stderr_logfile=/var/log/supervisor/mcp-server-time.err.log
     ```
  3. Repeat for each new MCP you want to run.

## Summary
- **MCP** is a modular protocol for serving AI model utilities.
- **To add new MCPs:**
  - Install the package in the Dockerfile.
  - Add a supervisor entry in `supervisord.conf`.
- **Supervisor** ensures all MCPs are started and monitored inside the container.

For more details, refer to the documentation of each MCP package and Supervisor.
