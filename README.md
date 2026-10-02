# 9Router (Docker Compose)

Setup Docker Compose untuk menjalankan [9Router](https://github.com/decolua/9router) — AI router/proxy yang menghubungkan tools seperti Claude Code, Codex, Cursor, Cline, dll ke 40+ provider AI (subscription, cheap, dan free tier) dengan auto-fallback.

## Menjalankan

```bash
docker compose up -d
```

Buka dashboard di browser:

```
http://localhost:20128
```

Login pertama kali pakai password default `123456` (ubah lewat env `INITIAL_PASSWORD`, lihat bagian [Konfigurasi](#konfigurasi)).

## Setup Provider (Quick Start)

1. Buka dashboard → **Providers** → **Connect**.
2. Pilih provider gratis untuk mulai cepat tanpa biaya, contoh:
   - **Kiro AI** — Claude 4.5 + GLM-5 + MiniMax, ~50 credits/bulan gratis (login via AWS Builder ID / Google / GitHub)
   - **OpenCode Free** — tanpa login, langsung pakai
3. Setelah provider terhubung, salin **API Key** dari dashboard.
4. Arahkan CLI tool kamu ke 9Router:

```
Endpoint: http://localhost:20128/v1
API Key : [dari dashboard]
Model   : kr/claude-sonnet-4.5
```

### Contoh integrasi Claude Code

Edit `~/.claude/config.json`:

```json
{
  "anthropic_api_base": "http://localhost:20128/v1",
  "anthropic_api_key": "your-9router-api-key"
}
```

### Contoh integrasi Codex CLI

```bash
export OPENAI_BASE_URL="http://localhost:20128"
export OPENAI_API_KEY="your-9router-api-key"
```

Integrasi tool lain (Cursor, Cline, OpenClaw, dll) mengikuti pola yang sama: base URL `http://localhost:20128/v1` + API key dari dashboard.

## Konfigurasi

Environment variable yang sudah di-set di `docker-compose.yml`:

| Variable   | Nilai                | Keterangan                          |
| ---------- | --------------------- | ------------------------------------ |
| `DATA_DIR` | `/app/data`           | Lokasi data (SQLite) di dalam container |
| `PORT`     | `20128`               | Port service                         |
| `HOSTNAME` | `0.0.0.0`             | Bind host                            |
| `NODE_ENV` | `production`          | Mode produksi                        |

Variable opsional (ada di `docker-compose.yml`, tinggal uncomment dan isi):

| Variable           | Default    | Fungsi                                                             |
| ------------------ | ---------- | ------------------------------------------------------------------- |
| `JWT_SECRET`        | auto-generate | Secret untuk signing cookie auth dashboard                       |
| `INITIAL_PASSWORD`  | `123456`   | Password login pertama kali                                        |
| `REQUIRE_API_KEY`   | `false`    | Wajibkan Bearer API key di `/v1/*` — aktifkan jika di-expose ke internet |
| `NEXT_PUBLIC_BASE_URL` | `http://localhost:3000` | Base URL publik (set sesuai domain/port kamu)          |

Daftar lengkap environment variable ada di [README 9Router](https://github.com/decolua/9router#environment-variables).

## Data Persistence

Data (provider, combo, API key, history usage) disimpan sebagai SQLite di folder `./data` (bind mount ke `/app/data` di container). Hapus container tidak akan menghapus data ini.

```
./data/db/data.sqlite
```

## Perintah Berguna

```bash
# Lihat log
docker compose logs -f

# Restart
docker compose restart

# Update ke image terbaru
docker compose pull
docker compose up -d

# Stop & hapus container
docker compose down
```

## Referensi

- Repo asli: https://github.com/decolua/9router
- Website: https://9router.com
