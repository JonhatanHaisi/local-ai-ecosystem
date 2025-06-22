# Local AI Environment

The purpose of this repository is to provide a ready-to-use infrastructure for running and experimenting with generative AI models locally, using Docker Compose. The stack is based on [Ollama](https://ollama.com/) for LLM (Large Language Model) serving, and includes a web interface, API gateway, and supporting tools for a complete local AI environment.

---

## Containers Overview

This repository uses Docker Compose to orchestrate the following containers:

### 1. **ollama**
- **Purpose:** LLM backend. Runs the Ollama server, which allows you to download and run various large language models locally, with GPU acceleration if available.
- **Docker Hub:** [ollama/ollama](https://hub.docker.com/r/ollama/ollama)
- **Source:** [github.com/ollama/ollama](https://github.com/ollama/ollama)
- **Dependencies:** Requires NVIDIA GPU and drivers for best performance (CPU fallback is possible).
- **Data Persistence:** Model data is stored in a Docker volume (`ollama_data`).
- **Access:** Not exposed to the host by default; only accessible by other containers (e.g., open-webui).

### 2. **open-webui**
- **Purpose:** Web-based chat client and management UI for Ollama and other LLMs. Allows you to interact with models, manage knowledge bases, and more.
- **Source:** [github.com/open-webui/open-webui](https://github.com/open-webui/open-webui)
- **Docs:** [docs.openwebui.com](https://docs.openwebui.com/)
- **Open WebUI Course:** [YouTube Playlist](https://youtube.com/playlist?list=PL_rTgQnnMXsXAsEiid-tWhaj03SsP4U5Z&si=X8noEH6vw2wZgJBG)
- **Dependencies:** Connects to the `ollama` container via internal Docker network.
- **Access:** Exposed on port 3000 (http://localhost:3000).
- **Data Persistence:** Uses a Docker volume (`open_webui_data`) for persistent storage.

### 3. **mcp-multi**
- **Purpose:** Custom MCP (Model Context Protocol) server. Used to provide additional AI utilities or services (such as time, math, or other context-aware plugins) to the environment.
- **Smithery (MCP Hub):** [smithery.ai](https://smithery.ai/)
- **Dependencies:** Built from the `mcp-server` folder using the provided Dockerfile. Managed by Supervisor inside the container.
- **Access:** Exposed on port 8001.
- **Data Persistence:** Logs are stored in a Docker volume (`mcp_logs`).

### 4. **ngrok**
- **Purpose:** Exposes the `open-webui` service securely to the internet using [ngrok](https://ngrok.com/). Useful for remote access or sharing your local AI environment.
- **Site:** [ngrok.com](https://ngrok.com/)
- **Dependencies:** Requires an ngrok account and authtoken (set in the `.env` file).
- **Access:** Public URL is provided in the ngrok container logs.

### 5. **watchtower**
- **Purpose:** Automatically monitors and updates running containers when new images are available. Helps keep your environment up-to-date with minimal effort.
- **Dependencies:** Requires access to the Docker socket.

---

## Dependencies
- **Docker** and **Docker Compose** installed on your system. [Get Docker](https://www.docker.com/)
- **NVIDIA GPU and drivers** (for best performance with Ollama; CPU mode is possible but slower).
- **ngrok account** (for public access, optional).

---

## Installation & Usage

1. **Clone the repository:**
   ```powershell
   git clone <this-repo-url>
   cd <repo-folder>
   ```

2. **Configure environment variables:**
   - Create a `.env` file in the root folder.
   - Add your ngrok authtoken:
     ```env
     NGROK_AUTHTOKEN=your_ngrok_token_here
     ```

3. **Build and start the containers:**
   ```powershell
   docker compose up -d --build
   ```

4. **Access the services:**
   - **Open WebUI:** [http://localhost:3000](http://localhost:3000)
   - **ngrok public URL:** Check with `docker compose logs -f ngrok`
   - **MCP Server:** [http://localhost:8001](http://localhost:8001) (if needed)

5. **Stopping the environment:**
   ```powershell
   docker compose down
   ```

---

## Additional Notes
- **GPU Support:** Ensure your NVIDIA drivers and Docker NVIDIA runtime are properly installed for GPU acceleration.
- **Adding new MCPs:** See `mcp-server/README.md` for instructions on installing and configuring new MCP plugins.
- **Updating containers:** Watchtower will automatically update containers if new images are available.
- **Security:** By default, only the web UI and MCP server are exposed to the host. Ollama is only accessible internally for security.

---

For more details on each component, see their respective documentation and the `mcp-server/README.md` file.

