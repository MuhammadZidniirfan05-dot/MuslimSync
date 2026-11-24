# M0-03 — Layout dan navigasi internal dengan routing SvelteKit

Status: **diterbitkan ke GitHub** — [Issue #3](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/3). ID M0 adalah ID perencanaan.
Milestone tujuan: **M0 — Fondasi**.
Label rencana: `type:feature`, `area:ui`, `milestone:M0`. Belum diberi `agent:ready`.

## Konteks

PRD FR-GEN-2 dan §9 M0 #3; ARCHITECTURE §7. Pengguna menyetujui routing SvelteKit dan menegaskan navigasi tetap di website MuslimSync.

Acuan: [PRD](../../PRD.md), [arsitektur](../../ARCHITECTURE.md), [aturan agent](../../../AGENTS.md), [catatan API](../../api-notes.md).

## Scope

Header bersama, konten utama, navigasi bawah mobile, active state, skip link, hub Bacaan dan Ibadah serta halaman placeholder Indonesia. Mapping menu: Beranda `/`, Bacaan `/bacaan`, Ibadah `/ibadah`, Kalender `/calendar`, Profil `/profile`. Hub Bacaan berisi Al-Qur’an `/quran` dan Hadis `/hadith`. Hub Ibadah berisi Jadwal Salat `/prayer` dan Arah Kiblat `/qibla`. Gunakan routing berbasis file dan link internal; uji back/forward.

## Di luar scope

Data API, audio player, login/profil akun, kalender fungsional, countdown, route detail surah dinamis, proxy, dan deployment/fallback host.

Countdown tetap menjadi kebutuhan Beranda dan Jadwal Salat pada milestone fitur terkait; pengelompokan ini tidak menghapus kebutuhan tersebut maupun terjemahan dan murottal Al-Qur’an.

## File terkait

`src/routes/+layout.svelte`, `src/lib/components/Header.svelte`, `src/lib/components/BottomNav.svelte`, `src/routes/+page.svelte`, `src/routes/quran/+page.svelte`, `src/routes/calendar/+page.svelte`, `src/routes/profile/+page.svelte`, `src/routes/ibadah/+page.svelte`, `src/routes/prayer/+page.svelte`, `src/routes/qibla/+page.svelte`, `src/routes/hadith/+page.svelte`, `tests/e2e/navigation.spec.js`, konfigurasi Playwright bila belum tersedia.

Path di atas adalah target perubahan, bukan klaim bahwa file sudah ada.

Tambahkan `src/routes/bacaan/+page.svelte` untuk hub Bacaan.

## Dependensi dan keputusan

Menunggu M0-01 dan M0-02 merged. Pengguna telah memilih halaman kumpulan fitur Bacaan dan Ibadah dengan pengelompokan sesuai scope. Hosting tidak menghalangi shell statis, tetapi fallback deployment dinamis ditunda.

## Acceptance criteria

- [ ] AC-1 — Kelima menu membuka URL sesuai mapping scope; `/bacaan` menampilkan dua pilihan Al-Qur’an dan Hadis, sedangkan `/ibadah` menampilkan dua pilihan Jadwal Salat dan Arah Kiblat. Keempat pilihan membuka placeholder internal yang benar.
- [ ] AC-2 — Semua link fitur memakai origin website yang sama, tanpa `/#/` dan tanpa membuka penyedia API/tab baru. Routing ditangani SvelteKit, tanpa router custom.
- [ ] AC-3 — Halaman menu aktif memakai `aria-current="page"`. Pada `/quran` dan `/hadith`, kelompok Bacaan disorot; pada `/prayer` dan `/qibla`, kelompok Ibadah disorot. Tautan kelompok tidak mengklaim dirinya halaman saat ini (gunakan `aria-current="location"` bila sesuai). Tombol back/forward memperbarui halaman serta state aktif.
- [ ] AC-4 — Setiap tujuan statis dapat dibuka langsung dan di-refresh pada hasil build statis tanpa 404. Tidak mengklaim rute dinamis/deployment sudah teruji.
- [ ] AC-5 — Header/layout konsisten; placeholder berbahasa Indonesia menjelaskan fitur belum tersedia dan tidak memuat data ibadah palsu atau form login.
- [ ] AC-6 — Pada 360px dan desktop, konten tidak tertutup navigasi bawah; label terbaca, fokus terlihat, skip link berfungsi, dan navigasi keyboard mengikuti urutan wajar.
- [ ] AC-7 — Smoke test Playwright mencakup perpindahan menu, URL internal, direct load/reload halaman statis, serta back/forward. Test tidak bergantung API eksternal.

## Catatan teknis

Baca dokumentasi routing dan adapter-static sesuai versi SvelteKit 2 yang terpasang. Gunakan rute statis yang diprerender untuk issue ini. Jangan menetapkan fallback `index.html` sebelum memastikan tidak konflik dengan halaman root dan host pilihan.

## Verifikasi

Playwright pada hasil build untuk lima menu dan empat tujuan hub Bacaan/Ibadah; screenshot mobile/desktop, terang/gelap; uji keyboard dan browser back/forward.

## Definition of Done

- [ ] Semua AC dipetakan ke bukti di PR; tidak ada keputusan wajib yang masih ditebak.
- [ ] Pemeriksaan relevan lulus; hasil yang tidak dijalankan/tidak berlaku diberi alasan eksplisit.
- [ ] Kode dan komentar Inggris, UI Indonesia; tidak ada debugging console.log atau secret.
- [ ] Tidak ada perubahan di luar scope; dependensi baru dijustifikasi; dokumentasi terkait diperbarui.
- [ ] Reviewer memberi APPROVE pada commit terbaru; maksimal tiga putaran, lalu needs-human.
- [ ] Persetujuan manusia untuk perubahan yang dilindungi tersedia bila relevan; merge akhir oleh manusia.
