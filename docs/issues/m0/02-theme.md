# M0-02 — Design tokens, reset CSS, dan tema terang/gelap

Status: **diterbitkan ke GitHub** — [Issue #2](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/2). ID M0 adalah ID perencanaan.
Milestone tujuan: **M0 — Fondasi**.
Label rencana: `type:feature`, `area:ui`, `area:core`, `milestone:M0`. Belum diberi `agent:ready`.

## Konteks

PRD §7 dan FR-GEN-1/FR-GEN-3; ARCHITECTURE §6 dan §8. Komponen berikutnya memerlukan warna, tipografi, dan perilaku tema yang konsisten.

Acuan: [PRD](../../PRD.md), [arsitektur](../../ARCHITECTURE.md), [aturan agent](../../../AGENTS.md), [catatan API](../../api-notes.md).

## Scope

Tokens warna/spasi/radius/bayangan/tipografi/motion; reset dan base CSS; mode sistem, terang, gelap; preferensi tema lokal berversi; kontrol tema sederhana pada halaman fondasi. Font yang dipakai disimpan lokal beserta lisensinya.

## Di luar scope

Layout/nav lengkap, fitur ibadah, pengaturan lokasi/metode salat, cache API, desain dashboard final, PWA, dan framework CSS tambahan.

## File terkait

`src/lib/styles/tokens.css`, `src/lib/styles/base.css`, `src/app.css`, `src/lib/core/settings.svelte.js`, `src/lib/components/ThemeToggle.svelte`, `src/routes/+layout.svelte` untuk wiring minimal, `static/fonts/`, `tests/unit/` untuk logika preferensi jika dipisahkan.

Path di atas adalah target perubahan, bukan klaim bahwa file sudah ada.

## Dependensi dan keputusan

M0-01 selesai dan merged.

## Acceptance criteria

- [ ] AC-1 — Lima warna brand PRD tersedia sebagai CSS custom properties. Warna semantik foreground/background/border berbeda sesuai tema; komponen tidak menduplikasi nilai warna brand.
- [ ] AC-2 — Kunjungan pertama mengikuti prefers-color-scheme. Pengguna dapat memilih Terang, Gelap, atau Ikuti sistem melalui kontrol berlabel Indonesia yang dapat dioperasikan dengan keyboard.
- [ ] AC-3 — Pilihan eksplisit bertahan setelah reload dalam `ms:settings` dengan versi skema. Perubahan tema OS hanya memengaruhi pilihan Ikuti sistem.
- [ ] AC-4 — localStorage ditolak, JSON rusak, atau versi tidak dikenal tidak membuat halaman gagal; kembali ke default sistem. Pembaruan tema tidak menghapus pengaturan lain.
- [ ] AC-5 — Build/prerender tidak mengakses window/localStorage secara tidak aman. State baru memakai runes; cleanup listener tersedia.
- [ ] AC-6 — Font Plus Jakarta Sans, Inter, dan Amiri Quran yang dipasang berasal dari distribusi berlisensi dan dimuat lokal, tanpa request Google Fonts/CDN. Catat sumber/lisensi dan hindari unduhan bobot yang tidak digunakan.
- [ ] AC-7 — Verifikasi kedua tema pada 360px dan desktop: teks normal kontras minimal 4.5:1, teks besar 3:1, fokus terlihat; kontrol tidak menyebabkan overflow.

## Catatan teknis

Simpan hanya preferensi tema dalam scope ini. Data pengaturan lain belum diimplementasikan. Efek tampilan mengikuti `[data-theme]`; hormati preferensi reduced-motion bila menambahkan transisi.

## Verifikasi

Uji pilihan tersimpan/default sistem, perubahan OS, storage gagal/rusak, dan reload. Lampirkan screenshot kedua tema dan bukti pengukuran kontras; test tidak perlu mengunci semua nilai token.

## Definition of Done

- [ ] Semua AC dipetakan ke bukti di PR; tidak ada keputusan wajib yang masih ditebak.
- [ ] Pemeriksaan relevan lulus; hasil yang tidak dijalankan/tidak berlaku diberi alasan eksplisit.
- [ ] Kode dan komentar Inggris, UI Indonesia; tidak ada debugging console.log atau secret.
- [ ] Tidak ada perubahan di luar scope; dependensi baru dijustifikasi; dokumentasi terkait diperbarui.
- [ ] Reviewer memberi APPROVE pada commit terbaru; maksimal tiga putaran, lalu needs-human.
- [ ] Persetujuan manusia untuk perubahan yang dilindungi tersedia bila relevan; merge akhir oleh manusia.
