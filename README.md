# ink-web

Frontend for **Ink** — a real-time multiplayer drawing game inspired by Skribbl.io.

## What is Ink?

Ink is a fast, low-latency multiplayer drawing game with real-time canvas sync, chat, and rooms.

## Features

- Real-time lobby + room UI
- Canvas drawing + brush controls (WIP)
- Live chat + player list (WIP)
- Responsive layout (desktop-first)

## Tech

- SvelteKit / Svelte
- TypeScript
- Bun

## Getting started

### Prereqs

- Bun installed (`bun --version`)

### Install

```bash
bun install
```

### Configure

Create `.env`:

```env
PUBLIC_API_HTTP_URL=http://localhost:3000
PUBLIC_API_WS_URL=ws://localhost:3000/ws
```

> If your backend uses different paths, update these.

### Run dev

```bash
bun run dev
```

Open:

- http://localhost:5173

## Scripts

```bash
bun run dev       # dev server
bun run build     # production build
bun run preview   # preview production build locally
bun run lint      # (if configured)
bun run format    # (if configured)
```

## Repo structure (suggested)

```
src/
  routes/
  lib/
static/
```

## Deployment

This repo is designed to be deployed as a static site or SSR (depending on your SvelteKit adapter).

- Set `PUBLIC_API_HTTP_URL` and `PUBLIC_API_WS_URL` in your deployment environment
- Point the frontend at your backend domain (e.g. `api.ink.amirnaz.com`)

## Roadmap

- [ ] Smooth stroke rendering + interpolation
- [ ] Room creation / join flow
- [ ] Presence / ready state
- [ ] Mobile controls
- [ ] Better error and reconnect handling

## License

MIT
