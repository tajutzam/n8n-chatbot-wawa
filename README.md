# n8n Local

Run n8n locally with Docker Compose.

## Requirements

- Docker Desktop
- Docker Compose v2

AI provider:

- Google Gemini API
- Model: `gemini-2.0-flash`
- Embeddings: `text-embedding-004`
- Free tier depends on Google AI Studio limits

Create an API key at [Google AI Studio](https://aistudio.google.com/apikey), then configure it in n8n under **Credentials** as **Google Gemini(PaLM) API**.

Check installation:

```bash
docker --version
docker compose version
```

## Setup

Clone or open project directory, then optionally create `.env` from example:

```bash
cp .env.example .env
```

Default configuration:

- URL: `http://localhost:5678`
- Timezone: `Asia/Jakarta`
- Data volume: `n8n_data`

## Run

Start n8n in background:

```bash
docker compose up -d
```

Open browser:

```text
http://localhost:5678
```

Start and show logs:

```bash
docker compose up
```

## Stop

Stop containers without deleting data:

```bash
docker compose stop
```

Stop and remove containers and network:

```bash
docker compose down
```

Data volume remains safe after both commands.

## Logs And Status

Show container status:

```bash
docker compose ps
```

Follow n8n logs:

```bash
docker compose logs -f n8n
```

Show recent logs:

```bash
docker compose logs --tail=100 n8n
```

## Restart

Restart n8n:

```bash
docker compose restart n8n
```

Recreate container after configuration changes:

```bash
docker compose up -d --force-recreate
```

## Update n8n

Pull latest image and recreate container:

```bash
docker compose pull
docker compose up -d
```

Check running version in n8n UI under **Settings**.

## Reset All Data

Warning: removes workflows, credentials, users, and all n8n data.

```bash
docker compose down -v
docker compose up -d
```

Do not run this command unless full data reset is intended.

## Configuration

Copy `.env.example` to `.env`, then edit values:

```dotenv
N8N_HOST=localhost
GENERIC_TIMEZONE=Asia/Jakarta
TZ=Asia/Jakarta
```

Apply changes:

```bash
docker compose up -d --force-recreate
```

Do not commit `.env`. It is ignored by Git.

## WhatsApp Chatbot (WAHA)

`docker-compose.yml` includes WAHA (WhatsApp HTTP API) on port `3000`. The workflow `chatbot.json` has a WhatsApp flow: incoming WA message → Qdrant knowledge search → Gemini → auto reply via WA.

Setup:

1. Start all services:

   ```bash
   docker compose up -d
   ```

2. Open the WAHA dashboard:

   ```text
   http://localhost:3000/dashboard
   ```

3. Start session `default` with engine `WEBJS`, then scan the QR code from WhatsApp (**Settings > Linked devices > Link a device**).

4. Import `chatbot.json` into n8n and activate the workflow (the WhatsApp webhook is registered internally at `http://n8n:5678/webhook/waha-chatbot`, already configured in WAHA via `WHATSAPP_HOOK_URL`).

5. Send a message to the connected WhatsApp number. The chatbot replies based on the knowledge stored in Qdrant.

Notes:

- Session (login) is stored in the `waha_sessions` volume, so it survives restarts. Re-scan the QR only when logged out from the phone.
- Incoming messages that are self-sent, empty, group chats, or status broadcasts are ignored by the `Filter Pesan WA` node.
- Each sender has its own chat memory (`Window Buffer Memory WA`).

## Troubleshooting

Port `5678` already in use:

```bash
lsof -i :5678
```

Stop conflicting process or change the host port in `docker-compose.yml`:

```yaml
ports:
  - "5679:5678"
```

Then open `http://localhost:5679`.

Check Compose configuration:

```bash
docker compose config
```

Check container health and logs:

```bash
docker compose ps
docker compose logs --tail=100 n8n
```



Qdrant URL	http://qdrant:6333
API Key	kosongkan



# pilih WAHA
docker compose --profile waha up -d

# atau pilih Evolution API
docker compose --profile evolution up -d

# ganti dari satu ke yang lain
docker compose --profile waha down
docker compose --profile evolution up -d