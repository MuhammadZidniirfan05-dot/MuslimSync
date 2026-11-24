# Panduan Implementer semi-manual

Satu issue → satu branch → satu PR → review maksimal tiga putaran → merge oleh manusia. Anda membuka sesi Implementer dengan model lebih murah, lalu menyerahkan URL PR kepada Planner/Reviewer. Tidak ada otomatisasi agent pada tahap ini.

Acuan wajib: [AGENTS.md](../AGENTS.md), [PRD](./PRD.md), [arsitektur](./ARCHITECTURE.md), [catatan API](./api-notes.md), [daftar issue M0](./issues/m0/README.md), dan [template PR](../.github/pull_request_template.md).

## 1. Siapkan issue pertama

Mulai dari [issue #1 — scaffold dan CI dasar](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/1). Pembaruan 24 November 2026: dokumen/template telah dipublikasikan ke branch `main`, folder lokal telah menjadi repositori Git, dan [izin CI dasar](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/1#issuecomment-7121152514) telah dicatat. Prasyarat berikut sudah dipenuhi untuk issue #1; periksa status GitHub terbaru sebelum menjalankan prompt. Implementasi belum dimulai.

Sebelum menyerahkan issue:

1. Publikasikan dokumen dan template yang telah diperiksa ke branch dasar repositori, dengan izin pemilik. Jika meminta agent menyiapkannya, izinkan secara eksplisit inisialisasi Git, commit dokumen/template, dan push awal; jangan menimpa riwayat remote bila sudah berubah. Periksa file yang akan masuk commit, lalu cantumkan tautan dokumen pada issue.
2. Berikan izin khusus untuk `.github/workflows/ci.yml` pada issue #1. Contoh komentar yang dapat Anda kirim sendiri: “Saya mengizinkan implementasi CI dasar dalam `.github/workflows/ci.yml` sesuai scope issue #1: install dari lockfile, lint, test, dan build. Izin ini tidak mencakup deploy, otomatisasi agent, atau perubahan secret.”
3. Pastikan acceptance criteria jelas dan dependensi selesai. Setelah hambatan terselesaikan, hapus `needs-human` dan pasang `agent:ready`. Jangan menandai issue siap hanya karena sudah dibuat.

Izin CI diperlukan oleh arsitektur §19 dan AGENTS.md. Untuk issue #1, izin telah diberikan melalui percakapan dan dicatat pada tautan di atas; tidak perlu diminta ulang untuk scope yang sama. Contoh komentar bukan izin umum untuk issue lain. Keputusan API/hosting yang masih terbuka tidak menghalangi scaffold statis sesuai batas issue #1.

## 2. Jalankan satu sesi Implementer

Buka folder checkout MuslimSync pada alat coding agent yang Anda gunakan. Mulai sesi baru, pilih model lebih murah yang tersedia melalui pemilih model alat tersebut, lalu kirim prompt berikut. Ganti nomor dan URL untuk issue berikutnya. Jangan menjalankan dua Implementer pada checkout yang sama secara bersamaan.

```text
Kamu adalah IMPLEMENTER untuk MuslimSync. Kerjakan hanya issue #1:
https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/1

Baca AGENTS.md, docs/PRD.md, docs/ARCHITECTURE.md, docs/api-notes.md,
issue terbaru beserta komentarnya, dan .github/pull_request_template.md.
Periksa kesiapan issue, dependensi, branch dasar, perubahan lokal,
dan bukti izin workflow sebelum mulai. Jangan menimpa perubahan pengguna.
Jika ada keputusan wajib yang belum jelas, tanyakan di issue dan hentikan
bagian yang bergantung padanya; lanjutkan pekerjaan independen yang diizinkan.

Buat branch chore/1-sveltekit-foundation dari branch dasar terbaru.
Ubah status issue dari agent:ready menjadi agent:in-progress.
Implementasikan acceptance criteria issue ini saja.

Gunakan SvelteKit 2.x, Svelte 5 runes, adapter-static, dan JavaScript.
Sebelum menulis komponen, baca dokumentasi resmi Svelte/SvelteKit.
Saat scaffold, verifikasi versi stabil terbaru dalam seri yang disetujui,
Node LTS dan kompatibilitas tooling; gunakan sv create, pin dependency
langsung dan commit lockfile. Jangan menimpa dokumen/template existing.
Tidak ada export let, $:, atau writable untuk state baru.
Ikuti service → normalizer → model → UI; fetch hanya pada core/http.js.
Jangan render data eksternal dengan innerHTML atau {@html}.
UI Indonesia; identifier dan komentar kode Inggris.

Jalankan npm run lint, npm test, npm run build, serta pemeriksaan issue
dan UI yang relevan. Catat perintah, hasil nyata, versi lingkungan,
dan keterbatasan; jangan mengklaim pemeriksaan yang belum dijalankan.
Untuk scaffold ini saja, 0 unit test harus dilaporkan sebagai 0 test,
bukan bukti pengujian perilaku, sesuai AC-2.

Commit perubahan relevan dan push branch pekerjaan, lalu buka PR ke
repositori tersebut memakai template PR dan Closes #1.
Isi bukti setiap AC, langkah uji manual, screenshot UI, sumber dokumentasi,
alasan dependency, dan tautan izin workflow. Jika pemeriksaan belum lulus,
gunakan draft dan laporkan blocker; jangan klaim review-ready.
Setelah pemeriksaan wajib dan CI commit terbaru lulus, ubah status issue
dan PR ke agent:in-review, hapus status agent:* sebelumnya.
Kembalikan URL PR, commit HEAD, ringkasan, hasil uji, dan blocker jika ada.
Jangan merge, deploy, menambah otomatisasi agent, atau mengambil issue lain.
```

Jika alat tidak mempunyai akses GitHub, minta Implementer menyerahkan diff, commit lokal, serta isi PR siap salin; Anda melakukan push/pembuatan PR sendiri. Jangan membagikan token lewat chat atau menaruhnya pada kode.

## 3. Serahkan PR kepada Reviewer

Kembali ke sesi Planner/Reviewer dengan model tier tinggi. Kirim:

```text
Review PR <URL_PR> untuk issue #<N>, Round 1/3.
Baca issue dan komentar, AGENTS.md, PRD, arsitektur, dan catatan API.
Periksa diff serta commit HEAD aktual, bukti tiap AC dan CI commit itu.
Gunakan delapan area rubrik AGENTS.md, bukan hanya ringkasan Implementer.
Berikan APPROVE atau REQUEST_CHANGES dan catat SHA yang direview.
Temuan blocker harus memuat file, baris, aturan/AC, bukti atau reproduksi,
dan koreksi yang diminta. Bedakan bukti belum tersedia dari cacat terbukti.
Catat hasil pada PR dan selaraskan label issue/PR; jangan merge.
Jika akun GitHub yang sama tidak dapat memberi approval formal pada PR
sendiri, catat putusan sebagai komentar review dan jelaskan batas tersebut;
jangan mengklaim persyaratan approval GitHub telah terpenuhi.
```

Reviewer memeriksa: acceptance criteria; arsitektur/runes; rendering aman; state loading/error/kosong; test dan CI; scope/dependensi; aksesibilitas; keterbacaan kode. Konten keagamaan dan logika salat juga memerlukan tinjauan manusia sebelum merge.

## 4. Revisi atau selesaikan

Jika `REQUEST_CHANGES`, salin tautan review ke sesi Implementer yang sama:

```text
Perbaiki blocker pada review <URL_REVIEW> untuk PR <URL_PR>.
Tetap pada branch dan PR yang sama, dalam scope issue.
Ubah status issue/PR menjadi agent:in-progress, hapus status sebelumnya.
Jalankan ulang pemeriksaan yang diperlukan, push revisi, dan jawab setiap
temuan dengan koreksi serta bukti. Setelah CI terbaru lulus, ubah status
ke agent:in-review. Laporkan SHA terbaru. Jangan merge atau membuka PR pengganti.
```

Kirim PR kembali kepada Reviewer sebagai `Round 2/3`, lalu `Round 3/3` bila diperlukan. Review awal dihitung putaran 1, sehingga maksimal dua kesempatan revisi dalam loop ini. Jika putaran 3 masih gagal, gunakan `needs-human`, pertahankan status `agent:changes-requested`, rangkum blocker dan upaya, lalu berhenti sampai Anda menentukan langkah berikutnya. Jangan mereset hitungan lewat PR baru.

Jika `APPROVE`, status issue/PR menjadi `agent:approved`. Anda memeriksa CI dan persyaratan repository pada commit terbaru, kemudian melakukan merge sendiri. Perubahan commit setelah approval memerlukan pemeriksaan ulang; jangan memakai approval lama untuk diff baru.

Untuk setiap item hanya satu label `agent:*` yang aktif. `needs-human` adalah penanda tambahan untuk hambatan/eskalasi, bukan pengganti laporan sebabnya. Label tidak menjalankan agent pada alur semi-manual ini.

## 5. Lanjutkan backlog

Setelah #1 merged, #2 (tema) dan #4 (HTTP/cache) dapat dijadwalkan; #3 (layout) menunggu #1 dan #2. #5 meninjau dokumentasi audit sesuai scope-nya. Periksa kembali kesiapan tiap issue sebelum memberi `agent:ready`.

Menu yang disepakati: Beranda, Bacaan, Ibadah, Kalender, Profil. Bacaan berisi Al-Qur’an dan Hadis; Ibadah berisi Jadwal Salat dan Arah Kiblat. Countdown tetap pada Beranda/Jadwal Salat di milestone fitur terkait. M0 tidak mengimplementasikan fitur data tersebut.
