# M0-04 — HTTP client, timeout, retry, dan cache SWR

Status: **diterbitkan ke GitHub** — [Issue #4](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/4). ID M0 adalah ID perencanaan.
Milestone tujuan: **M0 — Fondasi**.
Label rencana: `type:feature`, `area:core`, `milestone:M0`. Belum diberi `agent:ready`.

## Konteks

PRD §6 dan §9 M0 #4; ARCHITECTURE §4–6. Semua service berikutnya memerlukan satu jalur request dengan error terkelola dan cache tahan gangguan.

Acuan: [PRD](../../PRD.md), [arsitektur](../../ARCHITECTURE.md), [aturan agent](../../../AGENTS.md), [catatan API](../../api-notes.md).

## Scope

`request()` JSON terpusat, error terstruktur, timeout 8 detik per percobaan, maksimal dua retry GET, backoff, cancellation, serta helper cache SWR dengan TTL dan storage berversi. Cache metadata mempertahankan source/fetchedAt/stale dan memberi mekanisme hasil revalidasi ke pemanggil.

## Di luar scope

Adapter penyedia, pemanggilan API live dari UI, data agama, lokasi pengguna, pemilihan provider, proxy, service worker, UI loading/error, serta penyimpanan audio/offline corpus.

## File terkait

`src/lib/core/http.js`, `src/lib/core/errors.js`, `src/lib/core/cache.js`, `tests/unit/http.test.js`, `tests/unit/cache.test.js`; catatan kontrak helper di JSDoc atau README teknis.

Path di atas adalah target perubahan, bukan klaim bahwa file sudah ada.

## Dependensi dan keputusan

M0-01 merged. Tidak memerlukan keputusan API feature atau lokasi karena seluruh unit test memakai data sintetis dan mocked transport.

## Acceptance criteria

- [ ] AC-1 — Semua application fetch berada di http.js. `request()` menerima URL dan opsi terkontrol, mengembalikan JSON valid atau managed error yang membedakan HTTP, timeout, network, parsing, dan cancellation.
- [ ] AC-2 — Setiap percobaan dibatalkan setelah 8 detik; timer/listener dibersihkan. Cancellation pemanggil menghentikan request dan backoff tanpa retry baru.
- [ ] AC-3 — GET mengalami paling banyak tiga percobaan total untuk network/timeout, 429, dan 5xx. 400/401/403/404 serta kesalahan parsing tidak diretry; metode selain GET tidak diretry otomatis.
- [ ] AC-4 — Backoff meningkat (baseline 500ms lalu 1000ms); hormati Retry-After valid untuk 429/503, baik delta detik maupun HTTP date. Cancellation tetap bekerja ketika menunggu. Uji dengan fake timers.
- [ ] AC-5 — Cache key eksplisit memisahkan sumber, versi, resource, dan parameter pembeda; TTL dikirim pemanggil. Storage memakai prefix ms: dan versi skema; payload rusak/versi tidak cocok dianggap miss.
- [ ] AC-6 — Cache kosong menunggu loader; cache fresh dikembalikan tanpa request; cache expired dikembalikan segera sebagai stale sambil menjalankan satu revalidasi per key. Kontrak menyediakan promise/callback untuk hasil baru tanpa bergantung UI framework.
- [ ] AC-7 — Revalidasi sukses memperbarui data serta fetchedAt; gagal mempertahankan data lama dengan stale true dan menyediakan error terkelola tanpa unhandled rejection. Request bersamaan pada key sama tidak menggandakan loader.
- [ ] AC-8 — Storage tidak tersedia/kuota penuh tidak mematikan request; fallback memori dalam sesi tetap berfungsi. Modul aman diimpor saat build tanpa akses browser di tingkat modul.
- [ ] AC-9 — Unit test deterministik membuktikan timeout, batas retry, Retry-After, cancellation, parse failure, fresh/stale/miss, deduplikasi, isolasi key, dan storage gagal. Tidak ada HTTP live dalam unit tests.

## Catatan teknis

Helper cache menerima loader dan parameter; normalizer tetap milik service. Jangan cache data error HTTP sebagai sukses. Cache untuk provider dengan batas penyimpanan khusus harus mendukung kebijakan pemanggil; TTL default fitur bukan izin menyimpan selamanya. Batasi perubahan pada fondasi dan tests; jika ukuran melampaui satu PR yang reviewable, Planner memecah HTTP dan cache sebelum agent:ready.

## Verifikasi

Vitest fake timers, transport mock, fake storage; verifikasi cleanup dan tidak ada unhandled rejection. Jalankan lint/test/build. Tidak perlu membandingkan jadwal atau merender UI pada issue ini.

## Definition of Done

- [ ] Semua AC dipetakan ke bukti di PR; tidak ada keputusan wajib yang masih ditebak.
- [ ] Pemeriksaan relevan lulus; hasil yang tidak dijalankan/tidak berlaku diberi alasan eksplisit.
- [ ] Kode dan komentar Inggris, UI Indonesia; tidak ada debugging console.log atau secret.
- [ ] Tidak ada perubahan di luar scope; dependensi baru dijustifikasi; dokumentasi terkait diperbarui.
- [ ] Reviewer memberi APPROVE pada commit terbaru; maksimal tiga putaran, lalu needs-human.
- [ ] Persetujuan manusia untuk perubahan yang dilindungi tersedia bila relevan; merge akhir oleh manusia.
