# CrosswardBackEnd

FastAPI backend for Crossword Arena. It is the source of truth for game state and publishes a read-only snapshot to Firestore for the frontend.

## Run locally

```
pip install -r requirements.txt
uvicorn app.main:app --host 127.0.0.1 --port 8001
```

The local dev flow expects a Firestore emulator on `127.0.0.1:8080` (see the main repo's `scripts/start-emulators.ps1`).

## Deploy to Render

1. Push this repo to GitHub and connect it to a new Render **Web Service**.
2. Render auto-detects `render.yaml` (Blueprint) or `requirements.txt`.
3. Set these environment variables in the Render dashboard:

| Variable | Purpose |
| --- | --- |
| `GEMINI_API_KEY` | Google Gemini key for the puzzle image parser (required to create rooms). |
| `FIRESTORE_PROJECT_ID` | Firebase project id used for the state mirror. |
| `ALLOWED_ORIGINS` | Comma-separated list of allowed CORS origins (your frontend URL). |

`GEMINI_API_KEY` and `ALLOWED_ORIGINS` are declared with `sync: false` in `render.yaml`, so you set them in the dashboard after the first deploy.

## Endpoints

- `GET /health` — liveness probe.
- `GET /rooms` — room summaries.
- `GET /rooms/{roomId}/state` — public room state.
- `POST /rooms/{roomId}/join` — join a room (sets a session cookie).
- `POST /rooms/{roomId}/clues/{clueId}/submissions` — submit an answer.
- `GET /rooms/{roomId}/statistics` — per-room admin vs players statistics.
- Admin routes under `/admin` (password `1234` by default — change it before production).

## Notes

- Firestore access currently targets the local emulator via `FIRESTORE_EMULATOR_HOST` / `FIRESTORE_PROJECT_ID`. Production Firestore wiring is pending.
- Game state is in-memory and restored from Firestore on startup (best-effort).
