# M0-01 — Inisialisasi SvelteKit 2, Svelte 5, dan CI dasar

Status: **diterbitkan ke GitHub** — [Issue #1](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/1). ID M0 adalah ID perencanaan.
Milestone tujuan: **M0 — Fondasi**.
Label: `type:chore`, `area:core`, `area:ci`, `milestone:M0`. Prasyarat publikasi dokumen dan izin CI telah dipenuhi; siap diberi `agent:ready` setelah sinkronisasi issue.

## Konteks

PRD §9 M0 #1; ARCHITECTURE §2, §3, §9. Fondasi harus menghasilkan situs statis dan menyediakan alat pemeriksaan untuk PR selanjutnya.

Acuan: [PRD](../../PRD.md), [arsitektur](../../ARCHITECTURE.md), [aturan agent](../../../AGENTS.md), [catatan API](../../api-notes.md).

## Scope

Scaffold SvelteKit 2.x + Svelte 5 runes dengan adapter-static, satu halaman awal Indonesia yang diprerender, struktur dasar, npm scripts, ESLint/Prettier/Stylelint, Vitest, dan CI dasar. Tetapkan Node LTS dan pin dependency langsung beserta lockfile setelah verifikasi kompatibilitas resmi.

## Di luar scope

Fitur ibadah, adapter API, tema lengkap, navigasi lengkap, PWA, proxy, deploy, branch protection, dan otomatisasi agent. Tidak menetapkan host atau fallback rute dinamis. Jangan menimpa dokumen/template yang sudah ada.

## File terkait

`package.json`, `package-lock.json`, `svelte.config.js`, `vite.config.js`, `src/app.html`, `src/app.css`, `src/routes/+page.svelte`, `src/routes/+layout.js`, konfigurasi ESLint/Prettier/Stylelint, `.gitignore`, berkas versi Node, `.github/workflows/ci.yml`, `README.md`. Lokasi konfigurasi disesuaikan dengan versi stabil SvelteKit 2 yang diverifikasi; jelaskan perbedaannya dari contoh arsitektur.

Path di atas adalah target perubahan, bukan klaim bahwa file sudah ada.

## Dependensi dan keputusan

Tidak bergantung pada issue implementasi lain. Dokumen sumber telah dipublikasikan ke branch `main`. Pemilik telah mengizinkan CI dasar sesuai scope issue ini; lihat [catatan persetujuan](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/1#issuecomment-7121152514). Izin tidak mencakup deploy, otomatisasi agent, perubahan secret, atau merge otomatis. Implementer wajib merujuk persetujuan tersebut dalam PR.

## Acceptance criteria

- [ ] AC-1 — Catat versi stabil Svelte 5.x, SvelteKit 2.x, adapter-static, Node LTS, dan sumber resmi tanggal pemeriksaan di PR. Tidak memakai prerelease atau SvelteKit 3; dependency langsung dipin dan lockfile tersedia.
- [ ] AC-2 — Dari checkout bersih, `npm ci`, `npm run lint`, `npm test`, dan `npm run build` dapat dijalankan dengan versi Node yang dicatat. Jika belum ada unit test, laporkan eksplisit '0 test'; izin sementara tanpa test hanya untuk scaffold ini, bukan bukti pengujian perilaku.
- [ ] AC-3 — Build menghasilkan berkas statis yang dapat dilayani tanpa server aplikasi. Halaman `/` tampil berbahasa Indonesia; tidak mengakses API atau geolocation saat build maupun saat dibuka.
- [ ] AC-4 — Konfigurasi lint/format mencakup JS dan Svelte serta CSS. PR membuktikan pelanggaran lint contoh terdeteksi lalu dihapus sebelum commit; tidak menambahkan sintaks Svelte 4.
- [ ] AC-5 — CI dasar menjalankan install dari lockfile, lint, unit runner, dan build pada pull_request serta push ke main. CI commit terbaru hijau sebelum review-ready; tanpa secret, deploy, atau trigger implementasi agent.
- [ ] AC-6 — README menjelaskan prasyarat, install, dev, pemeriksaan, build, serta batas konfigurasi hosting yang belum dipilih. Semua dokumen dan template existing tetap terjaga.

## Catatan teknis

Baca dokumentasi resmi Svelte/SvelteKit sebelum halaman awal. Gunakan direktori sementara bila CLI scaffold tidak menerima folder berisi dokumen; gabungkan secara selektif tanpa penghapusan massal. Konfigurasi static untuk rute `/` saja tidak memerlukan keputusan fallback dinamis. File tooling dapat melebihi target ideal 300 baris; jelaskan file generated/config di PR.

## Verifikasi

Checkout bersih/install dari lockfile; lint probe sementara; build statis; buka hasil build pada server statis dan periksa console serta network. Tidak membuat unit test yang hanya menegaskan konstanta.

## Definition of Done

- [ ] Semua AC dipetakan ke bukti di PR; tidak ada keputusan wajib yang masih ditebak.
- [ ] Pemeriksaan relevan lulus; hasil yang tidak dijalankan/tidak berlaku diberi alasan eksplisit.
- [ ] Kode dan komentar Inggris, UI Indonesia; tidak ada debugging console.log atau secret.
- [ ] Tidak ada perubahan di luar scope; dependensi baru dijustifikasi; dokumentasi terkait diperbarui.
- [ ] Reviewer memberi APPROVE pada commit terbaru; maksimal tiga putaran, lalu needs-human.
- [ ] Persetujuan manusia untuk perubahan yang dilindungi tersedia bila relevan; merge akhir oleh manusia.
