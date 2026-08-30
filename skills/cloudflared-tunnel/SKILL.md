---
name: cloudflared-tunnel
description: >
  Create a quick tunnel with `cloudflared tunnel --url` to expose a local web
  server/port to the internet through Cloudflare. Use when the user wants to
  share a locally running web app, VSCode, Jupyter, webhook, or demo over a
  temporary public URL without a Cloudflare account. Trigger with "tunnel",
  "public url", "expose port", "trycloudflare", "cloudflared", "share local server".
version: 1.0.0
license: MIT
author: elahw
compatibility:
  os: [macos, linux, windows]
  cloudflared: ">= 2022.0.0"
metadata:
  category: devops
  requires-auth: false
---

# Quick Tunnel with cloudflared

Create a public `*.trycloudflare.com` URL pointing to a local server — no
Cloudflare account login, no DNS, no prior configuration needed.

## When to use

- The user wants to expose a local web server/port (VSCode, Jupyter, dev app, webhook) to the internet temporarily.
- The user says "tunnel", "public url", "expose port", "trycloudflare", "cloudflared".
- A temporary URL is enough; no custom or long-lived domain required.

> Use **`cloudflared tunnel --url`** (quick tunnel) rather than setting up a
> named tunnel. Switch to a *named tunnel* (needs `cloudflared tunnel login`)
> when you need a stable domain, a custom domain, or a load balancer.

## Prerequisites

- `cloudflared` installed. Check: `cloudflared --version`.
- If missing, install it:
  - **macOS (Homebrew):** `brew install cloudflared`
  - **Linux (deb):** `wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb && sudo dpkg -i cloudflared-linux-amd64.deb`
  - **Windows (winget):** `winget install Cloudflare.cloudflared`

## How to run

First make sure a local server is running on a port. For example:

```bash
python -m http.server 8080
# or
uvicorn app:app --port 8000
modal_launch.py jupyter --account xxx   # an app running on a local port
```

Then open a tunnel pointing to that port:

```bash
cloudflared tunnel --url http://localhost:8080
```

The output will contain a line like:

```
Your quick Tunnel has been created! Visit it at (it may take some time to be reachable):
https://<random-string>.trycloudflare.com
```

Use that URL to access / share. Great for VSCode/Jupyter since those apps already
have their own auth (token/password) — the tunnel is just the public transport layer.

## Common options

```bash
cloudflared tunnel --url http://localhost:8080            # HTTP
cloudflared tunnel --url http://localhost:8080 --no-autoupdate
cloudflared tunnel --url http://localhost:8080 --logfile tunnel.log   # write logs to file
cloudflared tunnel --url http://localhost:8080 --loglevel debug       # verbose logging
```

## Notes / troubleshooting

- The tunnel runs in the **foreground** and stops on Ctrl+C. To run it in the
  background you can add `&` or use `--logfile`, but for most tasks keep it in
  the foreground in a separate terminal.
- The `trycloudflare.com` URL is **temporary** — it disappears when the process
  stops. Do not use it for production.
- No `cloudflared tunnel login` is needed for this kind of quick tunnel; login is
  only required for named tunnels.
- If the local server is not running or the port is wrong, the tunnel still
  creates a URL but returns a 502 error when accessed — confirm the local server
  is actually listening on the right port first.
- When combined with Jupyter/VSCode on Modal: once you have the app URL
  (`*.modal.run`), prefer that URL (it already has auth) if you want a more
  stable public endpoint; use `cloudflared` only for local servers that lack
  built-in auth (e.g. `python -m http.server`).
- Security: a public URL means anyone can access it. Only expose servers with
  their own auth, or restrict access when possible (Cloudflare Access) — never
  expose sensitive things unconditionally.
