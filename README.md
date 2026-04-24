# Deploying Faceplate + OpenClaw (Docker Compose)

## Project Structure

After setup, your directory should look like this:

```
faceplate_stack/
├── docker-compose.yml
├── .env
├── faceplate/
│   ├── DB/
│   └── logs/
└── openclaw/
    ├── config/
    └── workspace/
```

---

##  Step 1. Clone OpenClaw

Create the directory and clone the repository:

```bash
mkdir -p ~/Documents/faceplate_stack/openclaw
cd ~/Documents/faceplate_stack/openclaw
```

Clone Openclaw configurations and skills

```bash
git clone https://github.com/faceplatekirill/fp_openclaw.git .
```

After cloning, you'll have the `openclaw` folder with all required contents (`config`, `workspace`, etc.).

---

## Step 2. Prepare Directories

Create the volume directory structure:

```bash
mkdir -p faceplate/DB
mkdir -p faceplate/logs
```


 **Note:**
> - `faceplate/DB` — SCADA database storage
> - `faceplate/logs` — application logs
> - `openclaw/config` — OpenClaw configuration files
> - `openclaw/workspace` — working environment (agents, memory, tools)

---

## Step 3. Docker Compose

The `docker-compose.yml` file is already configured to bring up two services:

### 🔹 Faceplate

- Main application (backend + UI + DB)
- Exposed ports:
  - `9000`
  - `8000`
  - `7000`
- Persistent volumes:
  - Database → `faceplate/DB`
  - Logs → `faceplate/logs`

### 🔹 OpenClaw

- AI / agent system
- Exposed port:
  - `18789`
- Persistent volumes:
  - Config → `openclaw/config`
  - Workspace → `openclaw/workspace`

---


File `.env` contains additional information for Docker compose

## Step 4. Start the Stack

**Start the full Stack in detached mode:**

```bash
docker compose up -d
```

**View logs:**

```bash
docker compose logs -f
```

**Stop the stack:**

```bash
docker compose down
```

---

## Verify the Services

Once running, the services are available at:

| Service    | URL                                                              |
|------------|------------------------------------------------------------------|
| Faceplate Studio | [http://localhost:9000/fp/studio](http://localhost:9000/fp/studio)                   |                 |
| Faceplate Runtime  | [http://localhost:9000/fp/runtime](http://localhost:9000/fp/runtime)                   |                 |
| OpenClaw   | [http://localhost:18789](http://localhost:18789)                 |

---

**Faceplate default credentials**

Username
```bash
system
```

Password
```bash
111111
```

---

## OpenClaw Workspace

The `openclaw/workspace` directory contains the stateful part of the system:

| Path / File        | Description                          |
|--------------------|--------------------------------------|
| `AGENTS.md`        | Agent definitions                    |
| `MEMORY.md`        | Memory configuration                 |
| `memory/`          | Persistent memory store              |
| `TOOLS.md`         | Tool definitions                     |
| `skills/`          | Custom skills                        |
| `extensions/`      | Extensions and plugins               |
| `PROJECT_KB`       | Project knowledge base               |
| `KNOWLEDGE_BASE`   | General knowledge base               |

> **Do not delete this folder on restart** — it holds the agent's persistent state.

---

## Rebuild and Restart

If images or configs have changed:

```bash
docker compose down
docker compose up -d --build
```

---

## Troubleshooting

**1. Port already in use**

```bash
sudo lsof -i :9000
```

**2. Permission denied on directories**

```bash
sudo chown -R $USER:$USER .
```

**3. Container fails to start**

```bash
docker compose logs faceplate
docker compose logs openclaw
```

**4. Openclaw UI requires Gateway Token**

Paste Token from file `openclaw/config/openclaw.json` into Openclaw UI ``Gateway Token`` require field. 

**5. Openclaw UI requires pairing**

When Openclaw UI Token require pairing, you need to approve your session. 

```bash
docker exec -it faceplate_openclaw_platform /bin/bash
```
You will be directed into Container. 

Into container run command:

```bash
openclaw devices list
```
You will get list of Pending requests. Copy the Request ID from this list.

Paste this Request ID into next command:

```bash
openclaw devices approve Request_ID
```

Now you can use Openclaw UI

**Faceplate. Openclaw UI requires pairing**

When Openclaw UI Token require pairing, you need to approve your session. 

---

## Summary

This stack brings up two interconnected services:

- **`faceplate`** — backend, UI, and database
- **`openclaw`** — AI agent system

They are linked through shared Docker volumes on the host filesystem.

## Faceplate Docs
> Faceplate docs and information you can get here:
>https://github.com/faceplate-docs/faceplate/tree/dev/docs/en