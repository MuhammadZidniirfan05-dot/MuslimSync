# ARCHITECTURE — MuslimSync

Dokumen ini menjelaskan arsitektur teknis **dan** alur kerja pengembangan berbasis agent di GitHub.

---

## 1. Prinsip

1. **Static-first:** frontend statis berbasis Svelte 5 yang dikompilasi menjadi HTML, CSS, dan JS murni; tanpa server aplikasi saat runtime.
2. **API sebagai sumber kebenaran:** UI tidak pernah memanggil `fetch` langsung; selalu lewat lapisan service.
3. **Adapter + normalizer:** bentuk respons tiap API dinormalisasi ke model internal sehingga API bisa diganti tanpa menyentuh UI.
4. **Modular per fitur:** tiap fitur berdiri sendiri (satu folder, satu tanggung jawab).
5. **Tahan gangguan:** cache, fallback, dan state error di setiap titik.
6. **Kecil dan cepat:** dependensi seminimal mungkin.

## 2. Tech Stack

| Lapisan | Pilihan | Alasan |
|---|---|---|
| Bahasa | HTML5, CSS3, JavaScript (ES Modules); tipe via JSDoc atau TypeScript | Sesuai kebutuhan; komponen `.svelte` berisi HTML, CSS, JS biasa |
| Framework | **Svelte 5** (runes: `$state`, `$derived`, `$effect`, `$props`) | Bundle kecil, performa tinggi, animasi dan transisi bawaan |
| Meta-framework | **SvelteKit 2.x** dengan **`@sveltejs/adapter-static`** | Routing, lazy loading, prerender; hasil build tetap situs statis |
| Build/dev | **Vite** (bawaan SvelteKit) | HMR cepat |
| Styling | CSS native scoped per komponen + design tokens global (custom properties, `@layer`, container queries) | Tanpa framework CSS, mudah di-theme |
| Ikon | **Lucide** (versi Svelte, SVG tree-shakeable) | Garis tipis, cocok gaya minimalis |
| Tanggal/waktu | **Day.js** + plugin timezone | Ringan; hitung mundur salat |
| Animasi | Bawaan Svelte (`transition:`, `animate:`, `svelte/motion`) | Halus tanpa library tambahan |
| PWA | **@vite-pwa/sveltekit** (Workbox) | Service worker dan manifest otomatis |
| Lint/format | ESLint (+ eslint-plugin-svelte) + Prettier (+ prettier-plugin-svelte) + Stylelint | Konsistensi untuk agent |
| Test | **Vitest** (unit), **Playwright** (e2e smoke) | Cepat; Playwright untuk verifikasi UI |
| Proxy (opsional) | **Cloudflare Workers** | CORS, cache, menyembunyikan key bila perlu |
| Hosting | Cloudflare Pages atau GitHub Pages | Gratis, statis |

**Versi:** gunakan rilis stabil terbaru seri Svelte 5.x dan SvelteKit 2.x (cek svelte.dev saat scaffolding, gunakan `sv create`). Jangan memakai SvelteKit 3 pra-rilis. Kunci versi di `package.json` dan Node LTS terbaru.

**Mode render:** `adapter-static` dengan prerender untuk halaman statis dan `fallback: 'index.html'` untuk rute dinamis (mis. `/quran/[surah]`). Semua pemanggilan API dilakukan di sisi klien lewat lapisan service.

### Alternatif yang sudah dipertimbangkan

| Opsi | Catatan |
|---|---|
| React + Vite | Ekosistem terbesar dan paling dikuasai agent, tetapi bundle lebih besar. Pilih bila keandalan agent murah lebih penting daripada ukuran dan animasi |
| SolidJS | Sangat cepat, ekosistem lebih kecil |
| Alpine.js | Terlalu ringan untuk aplikasi multi-halaman dengan state bersama |

Library tambahan fase lanjut: **Chart.js** atau **uPlot** (grafik Tracker), **Fuse.js** (pencarian fuzzy surah/kota).

### Aturan khusus Svelte untuk agent
- Hanya sintaks Svelte 5 runes. Dilarang `export let`, `$:`, dan store lama (`writable`) untuk state baru; gunakan `$state` di file `.svelte.js`.
- Baca dokumentasi resmi (svelte.dev/docs, termasuk `llms.txt` bila tersedia) sebelum menulis komponen baru.
- Satu komponen satu tanggung jawab; logika data ada di `lib/services`, bukan di komponen.

## 3. Struktur Folder

```
muslimsync/
├─ .github/
│  ├─ ISSUE_TEMPLATE/ (feature.yml, bug.yml)
│  ├─ workflows/ (ci.yml, agent-implement.yml, agent-review.yml)
│  └─ pull_request_template.md
├─ docs/
│  ├─ PRD.md
│  ├─ ARCHITECTURE.md
│  ├─ api-notes.md             # hasil verifikasi endpoint nyata
│  └─ decisions/               # ADR singkat
├─ static/                     # ikon, manifest, font lokal
├─ src/
│  ├─ app.html
│  ├─ app.css                  # import tokens + base
│  ├─ lib/
│  │  ├─ styles/ (tokens.css, base.css)
│  │  ├─ core/
│  │  │  ├─ http.js            # fetch wrapper (timeout, retry)
│  │  │  ├─ cache.js           # SWR di localStorage/IndexedDB
│  │  │  ├─ settings.svelte.js # state global (lokasi, tema, pengaturan)
│  │  │  └─ errors.js
│  │  ├─ services/             # satu adapter per sumber API
│  │  │  ├─ quran.service.js
│  │  │  ├─ hadith.service.js
│  │  │  ├─ prayer.service.js
│  │  │  ├─ hijri.service.js
│  │  │  ├─ weather.service.js
│  │  │  └─ geo.service.js
│  │  ├─ components/           # Card, Skeleton, BottomNav, AudioPlayer, ErrorState
│  │  ├─ features/             # komponen khusus fitur (prayer/, quran/, ...)
│  │  └─ utils/                # format waktu, qibla math, sanitasi
│  └─ routes/
│     ├─ +layout.svelte        # header, navigasi bawah, tema
│     ├─ +layout.js            # prerender = true, ssr sesuai kebutuhan
│     ├─ +page.svelte          # Beranda
│     ├─ bacaan/
│     ├─ ibadah/
│     ├─ quran/ (+page.svelte, [surah]/+page.svelte)
│     ├─ hadith/
│     ├─ prayer/
│     ├─ calendar/
│     ├─ weather/
│     ├─ qibla/
│     └─ profile/
├─ tests/ (unit/, e2e/)
├─ AGENTS.md                   # aturan untuk semua agent
├─ svelte.config.js
├─ vite.config.js
└─ package.json
```

## 4. Alur Data

```
Komponen Svelte ──► feature/page ──► service (adapter)
                                      │
                                      ├─► cache.js (hit & segar? → kembalikan)
                                      │
                                      ├─► http.js ──► API eksternal / proxy
                                      │
                                      └─► normalizer ──► model internal ──► UI
```

Aturan:
- UI hanya mengenal **model internal**, bukan respons mentah API.
- Service mengembalikan `{ data, source, fetchedAt, stale }` atau melempar error terkelola.
- Semua string dari API di-escape atau dirender via `textContent` (tanpa `{@html}`/`innerHTML` mentah).

### Model internal (contoh)

```js
// PrayerDay
{ date: '2026-11-24', hijri: { day: 13, month: 'Jumadil Akhir', year: 1448 },
  times: { imsak, fajr, sunrise, dhuhr, asr, maghrib, isha }, // 'HH:mm'
  method: 'KEMENAG', timezone: 'Asia/Jakarta' }

// Surah
{ number, nameArabic, nameLatin, meaning, ayahCount, revelation }

// Ayah
{ number, arabic, translation, audioUrl }

// WeatherSlot
{ time, tempC, condition, icon, humidity, windKph }
```

## 5. Integrasi API

Detail endpoint dan skema **tidak ditulis di sini** karena harus diverifikasi langsung oleh agent perencana dan dicatat di `docs/api-notes.md`.

| Service | Sumber kandidat | Cache TTL | Fallback |
|---|---|---|---|
| quran | EQuran.id v2 | Daftar surah: 30 hari; isi surah: 30 hari | Al-Quran Cloud |
| hadith | api.hadith.gading.dev | 7 hari | HadithAPI atau tampil "tidak tersedia" |
| prayer | Aladhan (Kemenag) | 24 jam per lokasi/tanggal | MyQuran v2 |
| hijri | Aladhan konversi | 24 jam | `Intl` `islamic-umalqura` (lokal) |
| weather | API prakiraan BMKG | 30–60 menit | Tampilkan data terakhir + label "terakhir diperbarui" |
| qibla | Hitung lokal | tak perlu | — |

### Kebijakan HTTP
- Timeout 8 dtk; retry 2× dengan exponential backoff untuk GET.
- Pola **stale-while-revalidate**: tampilkan cache dulu, perbarui di latar.
- Satu fungsi `request()` di `lib/core/http.js`; dilarang `fetch` di luar file itu.

### Proxy opsional
Dipakai hanya bila ada API yang menolak CORS atau butuh key. Cloudflare Worker dengan: allowlist domain tujuan, cache edge, header CORS, tanpa log data pribadi.

## 6. State dan Penyimpanan

| Data | Tempat |
|---|---|
| Lokasi, metode salat, tema, ukuran huruf | `localStorage` (`ms:settings`) |
| Terakhir dibaca Qur'an | `localStorage` (`ms:lastRead`) |
| Cache respons API | `localStorage` atau IndexedDB (data besar seperti surah) |
| Cache aset | Service Worker (Workbox) |

Semua key berprefiks `ms:` dan memiliki versi skema untuk migrasi.

## 7. Routing

Routing berbasis file SvelteKit: `/`, `/bacaan`, `/quran`, `/quran/[surah]`, `/ibadah`, `/hadith`, `/prayer`, `/calendar`, `/weather`, `/qibla`, `/profile`. Menu utama adalah Beranda, Bacaan, Ibadah, Kalender, dan Profil. Pengguna menyetujui `/bacaan` sebagai halaman kumpulan fitur Al-Qur’an (`/quran`) dan Hadis (`/hadith`), serta `/ibadah` sebagai halaman kumpulan fitur Jadwal Salat (`/prayer`) dan Arah Kiblat (`/qibla`). Semua tautan tersebut internal. Kode tiap rute otomatis dipecah (code-splitting) dan dimuat lazy. Rute dinamis ditangani `fallback: 'index.html'` pada adapter-static; konfigurasi fallback harus diverifikasi terhadap host dan hasil prerender sebelum implementasi deployment.

## 8. Desain Sistem

- Token di `tokens.css`: warna brand, skala spasi, radius, bayangan, tipografi, durasi animasi.
- Tema gelap via `[data-theme="dark"]` dan `prefers-color-scheme`.
- Font: Plus Jakarta Sans, Inter, Amiri Quran. Self-host agar cepat dan tidak bergantung CDN.
- Teks Arab: `lang="ar" dir="rtl"`, `line-height` longgar (≥ 2).

## 9. Kualitas

- **CI wajib hijau** untuk merge: lint, unit test, build, (e2e smoke pada main).
- Unit test untuk normalizer, hitung mundur salat, rumus kiblat, cache.
- Playwright: dashboard termuat, navigasi, state error (mock API gagal).
- Budget performa dicek di CI (ukuran bundle).

## 10. Deployment

- Branch `main` = produksi; otomatis deploy ke Cloudflare Pages/GitHub Pages.
- Preview deploy per PR (Cloudflare Pages) agar reviewer bisa melihat hasil.
- Environment: tidak ada secret di klien. Secret proxy disimpan di GitHub/Cloudflare secrets.

---

# Alur Kerja Pengembangan Agent di GitHub

## 11. Peran

| Peran | Model | Tugas |
|---|---|---|
| **Planner / Reviewer** | Model tier tinggi (mis. Claude Opus) | Menulis issue terstruktur, memverifikasi API, mereview PR, memutuskan lulus/revisi |
| **Implementer** | Model tier lebih murah (mis. Claude Sonnet atau Haiku) | Membuat branch, menulis kode, test, dan membuka PR |
| **Manusia (Anda)** | — | Menentukan prioritas, merge akhir, menangani eskalasi |

## 12. Siklus

```
 ┌─────────┐  1. buat issue   ┌──────────────┐
 │ Planner │ ───────────────► │ Issue        │ label: agent:ready
 └─────────┘                  └──────┬───────┘
      ▲                              │ 2. diambil
      │                              ▼
      │                       ┌──────────────┐  3. branch + PR
      │                       │ Implementer  │ ─────────────┐
      │                       └──────────────┘              ▼
      │                              ▲                ┌──────────┐
      │ 5. approve → merge           │ 4b. revisi     │ Pull     │
      │                              │                │ Request  │
      │                       ┌──────┴───────┐        └────┬─────┘
      └─────────────────────── │ Reviewer     │ ◄──────────┘
                              └──────────────┘  4. review + CI
```

1. **Plan:** Planner memecah milestone menjadi issue kecil (≤ 1 PR, idealnya < 300 baris perubahan) berisi konteks, scope, acceptance criteria, file yang disentuh, dan batasan. Label `agent:ready`.
2. **Implement:** Implementer mengambil issue, membuat branch `feat/<issue>-<slug>`, menulis kode dan test, membuka PR dengan `Closes #<n>`. Label menjadi `agent:in-review`.
3. **Verify:** CI berjalan otomatis. Reviewer memeriksa diff terhadap acceptance criteria, `AGENTS.md`, dan arsitektur ini.
4. **Putusan Reviewer:**
   - Lulus → label `agent:approved`, merge (atau diserahkan ke manusia).
   - Tidak lulus → komentar review yang spesifik (file, baris, alasan, perbaikan yang diminta), label `agent:changes-requested`; Implementer merevisi di PR yang sama.
5. **Loop** maksimal **3 putaran** per PR. Setelah itu label `needs-human`, dan Anda yang memutuskan (rewrite issue, naikkan model, atau kerjakan manual).

## 13. Label

| Label | Arti |
|---|---|
| `agent:ready` | Issue siap dikerjakan Implementer |
| `agent:in-progress` | Sedang dikerjakan |
| `agent:in-review` | PR menunggu Reviewer |
| `agent:changes-requested` | Perlu revisi |
| `agent:approved` | Lolos review |
| `needs-human` | Eskalasi ke manusia |
| `type:feature` / `type:bug` / `type:chore` | Jenis |
| `area:quran` / `area:prayer` / `area:core` ... | Area kode |
| `milestone:M0` ... `M3` | Tahap |

## 14. Template Issue (`.github/ISSUE_TEMPLATE/feature.yml`)

Bidang wajib:
- **Konteks:** mengapa issue ini ada, tautan ke bagian PRD.
- **Scope:** apa yang dikerjakan.
- **Di luar scope:** apa yang TIDAK boleh disentuh.
- **File/folder terkait:** daftar jalur.
- **Acceptance criteria:** daftar centang yang dapat diuji.
- **Catatan teknis:** endpoint terverifikasi, model data, edge case.
- **Definition of Done:** CI hijau, test ditambah, tanpa `console.log`, aksesibilitas dicek.

## 15. Template PR

- Ringkasan perubahan
- `Closes #<n>`
- Cara menguji (langkah manual)
- Checklist: lint ✓, test ✓, build ✓, tanpa dependensi baru tanpa alasan, state loading/error ada
- Screenshot (untuk perubahan UI)

## 16. Rubrik Review (untuk Reviewer)

1. Semua acceptance criteria terpenuhi?
2. Sesuai arsitektur (service → model → UI, tanpa `fetch` langsung, sintaks Svelte 5 runes)?
3. Input eksternal aman dirender (tanpa `{@html}`/`innerHTML` mentah)?
4. State loading, error, kosong tertangani?
5. Test bermakna dan CI hijau?
6. Tidak ada scope creep atau dependensi tak perlu?
7. Aksesibilitas dasar (label, kontras, fokus, `lang="ar"`)?
8. Kode mudah dibaca (nama jelas, fungsi kecil)?

Format keluaran Reviewer: `APPROVE` atau `REQUEST_CHANGES` + daftar temuan berurutan prioritas (blocker / saran).

## 17. Implementasi Otomatisasi

Dua opsi:

- **A. GitHub Actions + Claude Code Action** (`anthropics/claude-code-action`): workflow `agent-implement.yml` terpicu saat issue berlabel `agent:ready` (model murah); `agent-review.yml` terpicu pada `pull_request` (model mahal). Model dipilih lewat parameter model pada action. Verifikasi sintaks terbaru di dokumentasi action sebelum menulis workflow.
- **B. Semi-manual:** jalankan Claude Code di lokal dengan model berbeda per peran (`/model`), tetap memakai issue dan PR sebagai kanal komunikasi. Cocok untuk memulai.

Rekomendasi: mulai dari B untuk M0 sambil menyempurnakan template, lalu pindah ke A.

## 18. `AGENTS.md` (aturan wajib semua agent)

Isi minimal:
- Baca `docs/PRD.md` dan `docs/ARCHITECTURE.md` sebelum bekerja.
- Hanya kerjakan scope issue; jangan refactor di luar scope.
- Dilarang mengarang endpoint API; verifikasi dan catat di `docs/api-notes.md`.
- Dilarang `fetch` di luar `lib/core/http.js`; dilarang `{@html}` dengan data eksternal.
- Hanya sintaks Svelte 5 runes; baca dokumentasi resmi Svelte sebelum menulis komponen.
- Dependensi baru harus dijustifikasi di PR.
- Jalankan `npm run lint && npm test && npm run build` sebelum membuka PR.
- Bila acceptance criteria ambigu, tanyakan di komentar issue, jangan menebak.
- Bahasa UI: Indonesia; kode dan komentar: Inggris.

## 19. Pengaman

- Branch protection `main`: wajib PR, wajib CI hijau, wajib approval (agent reviewer atau manusia).
- Batas biaya: maksimal 3 putaran revisi, batas token per run, timeout workflow.
- Agent tidak boleh mengubah `.github/workflows`, `AGENTS.md`, atau secret tanpa persetujuan manusia (CODEOWNERS).
- Tinjau manusia untuk perubahan yang menyentuh konten keagamaan atau logika waktu salat.
