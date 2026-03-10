# nanobot-docker

## Image

The container uses the upstream `HKUDS/nanobot` Dockerfile and exposes port `18790`.

## Quick Start

1. Create a persistent data directory:

```bash
mkdir -p ./data
```

2. Initialize Nanobot config:

```bash
docker run --rm -it \
  -v "$(pwd)/data:/root/.nanobot" \
  ghcr.io/aiqinxuancai/nanobot:latest onboard
```

3. Edit the generated config file:

```text
./data/config.json
```

Minimal example:

```json
{
  "providers": {
    "openrouter": {
      "apiKey": "sk-or-v1-xxx"
    }
  },
  "agents": {
    "defaults": {
      "provider": "openrouter",
      "model": "anthropic/claude-opus-4-5"
    }
  }
}
```

4. Start the gateway:

```bash
docker run -d \
  --name nanobot \
  --restart unless-stopped \
  -p 18790:18790 \
  -v "$(pwd)/data:/root/.nanobot" \
  ghcr.io/<github-owner>/nanobot:latest gateway
```

5. View logs:

```bash
docker logs -f nanobot
```

## Docker Compose

Example `docker-compose.yml`:

```yaml
services:
  nanobot:
    image: ghcr.io/aiqinxuancai/nanobot:latest
    container_name: nanobot
    restart: unless-stopped
    ports:
      - "18790:18790"
    volumes:
      - ./data:/root/.nanobot
    command: ["gateway"]
```

Start it:

```bash
docker compose up -d
docker compose logs -f nanobot
```

Run the one-time onboarding flow with Compose:

```bash
docker compose run --rm nanobot onboard
```

## Notes

- Persistent state is stored in `/root/.nanobot` inside the container.
- If you change `config.json`, restart the container to apply the new settings.
- The image tags are produced by GitHub Actions and typically include `latest`, an upstream version tag, a date tag, and a commit-based tag.
