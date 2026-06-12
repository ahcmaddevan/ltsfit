# FitTrack — Fitness Tracker

Full-stack fitness tracker: Next.js 14 (App Router) + TypeScript + Tailwind CSS + Neon (PostgreSQL) + Drizzle ORM. Siap deploy ke Vercel.

## Fitur

- **Dashboard** — sapaan dinamis + nama (disimpan di localStorage), streak 🔥, workout hari ini dengan checkbox selesai, total menit minggu ini
- **Log Workout** — form manual dengan auto-suggest nama latihan dari template
- **Templates** — 5 template siap pakai (Full Body, Cardio Blast, Strength Focus, Yoga Flow, Quick Morning) + dukungan custom template via API
- **Timer** — single mode (stopwatch/countdown) dan template mode dengan auto-advance antar gerakan, notifikasi browser, beep (Web Audio API), circular progress, tombol Next/Restart
- **Batch save** — setelah template selesai, semua gerakan tersimpan sekaligus via `POST /api/workouts/batch`
- **History & Statistik** — filter kategori & rentang waktu, hapus workout, grafik batang 7 hari, pie chart kategori, total kalori (5 kal/menit)
- **Lainnya** — dark mode toggle, responsive (bottom nav di mobile), toast notification, timer jalan di background tab via Web Worker

## Setup

1. **Install dependencies**
   ```bash
   npm install
   ```

2. **Buat database Neon**
   - Daftar di [neon.tech](https://neon.tech), buat project baru
   - Salin connection string dari Connection Details

3. **Environment variables**
   ```bash
   copy .env.example .env
   ```
   Isi `DATABASE_URL` dengan connection string Neon kamu.

4. **Push schema ke database**
   ```bash
   npm run db:push
   ```

5. **(Opsional) Seed 5 template default ke database**
   ```bash
   npm run db:seed
   ```
   > Catatan: 5 template default sudah hardcoded di `lib/default-templates.ts` dan selalu tampil meski tanpa seed. Seed hanya menyalin mereka ke tabel `templates`.

6. **Jalankan**
   ```bash
   npm run dev
   ```

## Deploy ke Vercel

1. Push repo ke GitHub
2. Import project di [vercel.com](https://vercel.com)
3. Tambahkan environment variable `DATABASE_URL` di Project Settings
4. Deploy

## API Routes

| Method | Route | Deskripsi |
|---|---|---|
| GET | `/api/workouts` | Semua workouts (terbaru dulu) |
| POST | `/api/workouts` | Simpan satu workout |
| POST | `/api/workouts/batch` | Simpan banyak workout sekaligus (dari template) |
| PATCH | `/api/workouts/[id]` | Toggle status selesai |
| DELETE | `/api/workouts/[id]` | Hapus workout |
| GET | `/api/templates` | Template default + custom |
| POST | `/api/templates` | Simpan custom template |

## Struktur

```
app/
  page.tsx               # Dashboard
  log/page.tsx           # Form log manual
  templates/page.tsx     # Daftar template
  timer/page.tsx         # Timer single (stopwatch/countdown)
  timer/[templateId]/    # Timer mode template (auto-advance)
  history/page.tsx       # History + statistik + grafik
  api/                   # API routes
components/              # WorkoutCard, TemplateCard, TimerCircle, charts, dll.
db/                      # Drizzle schema + client
lib/                     # Template default, timer hooks, utils
public/timer-worker.js   # Web Worker untuk timer background
scripts/seed.ts          # Seeder template
```

## Catatan Teknis

- **Template default pakai ID negatif** (-1 s/d -5) supaya tidak bentrok dengan custom template di database (serial, positif). URL timer: `/timer/-1` dst.
- **Timer pakai Web Worker** + perhitungan delta `Date.now()`, jadi tetap akurat saat tab di-background (setInterval di main thread di-throttle browser).
- **Notifikasi browser** diminta izinnya saat user menekan Start (wajib dari user gesture). Beep tetap bunyi meski notifikasi ditolak.
