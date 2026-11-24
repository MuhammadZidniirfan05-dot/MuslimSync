# PRD — MuslimSync

> **Connecting Faith, Time, and Life.** · Faith in Sync
> Versi: 0.1 (draft) · Status: Siap dipecah menjadi GitHub Issues

---

## 1. Ringkasan

MuslimSync adalah platform web keislaman terintegrasi yang menyatukan Al-Qur'an, Hadis, Jadwal Salat, Kalender Hijriah, Cuaca BMKG, dan Arah Kiblat dalam satu dashboard yang modern, minimalis, dan tenang. Semua data diambil dari API eksternal; MuslimSync bertindak sebagai lapisan integrasi dan pengalaman pengguna.

**Positioning:** Islamic Daily Companion
**Brand essence:** Faith in Sync
**Kepribadian:** modern, minimalis, informatif, tenang, tepercaya

## 2. Tujuan dan Non-Tujuan

### Tujuan (MVP)
1. Dashboard beranda yang menampilkan salat berikutnya, tanggal Hijriah, cuaca, dan ayat pilihan dalam satu layar.
2. Membaca Al-Qur'an (teks Arab, terjemahan Indonesia, audio) dengan cepat dan nyaman.
3. Menampilkan jadwal salat akurat sesuai lokasi pengguna.
4. Seluruh fitur terhubung ke API, tanpa data statis hardcode (kecuali fallback).
5. Responsif (mobile-first), cepat, dan dapat dipasang sebagai PWA.

### Non-Tujuan (MVP)
- Tidak ada akun/login dan backend database sendiri.
- Tidak ada fitur sosial atau komunitas.
- Tidak ada Zakat dan Tracker (masuk fase 2).
- Tidak membuat konten keislaman orisinal (tafsir, fatwa). Hanya menampilkan sumber API.

## 3. Pengguna

| Persona | Kebutuhan utama |
|---|---|
| Pekerja urban | Tahu waktu salat berikutnya sekilas, notifikasi, cuaca untuk perjalanan |
| Pelajar/mahasiswa | Baca Qur'an dan hadis dengan terjemahan, pencarian cepat |
| Orang tua | Tampilan jelas, huruf besar, navigasi sederhana |

## 4. Kebutuhan Fungsional

Prioritas: **P0** = wajib MVP, **P1** = MVP jika waktu cukup, **P2** = fase berikutnya.

### 4.1 Beranda / Dashboard (P0)
- FR-HOME-1: Sapaan dinamis berdasarkan waktu (Selamat Pagi/Siang/Sore/Malam) dan "Assalamualaikum".
- FR-HOME-2: Kartu **Salat Berikutnya** dengan nama, jam, dan hitung mundur.
- FR-HOME-3: Kartu **Kalender Hijriah** (tanggal Hijriah hari ini).
- FR-HOME-4: Kartu **Cuaca Hari Ini** (suhu, kondisi, sumber BMKG).
- FR-HOME-5: Kartu **Ayat Pilihan** yang berganti harian.
- FR-HOME-6: Grid pintasan ke semua fitur.
- FR-HOME-7: Tiap kartu punya state loading (skeleton), error, dan kosong.

### 4.2 Al-Qur'an (P0)
- FR-QR-1: Daftar 114 surah dengan pencarian nama.
- FR-QR-2: Halaman surah: teks Arab (font Amiri Quran), terjemahan Indonesia, nomor ayat.
- FR-QR-3: Audio murottal per ayat/surah.
- FR-QR-4: Penanda terakhir dibaca (localStorage).
- FR-QR-5 (P1): Tafsir ringkas per ayat.
- FR-QR-6 (P1): Pengaturan ukuran huruf Arab dan toggle terjemahan.

### 4.3 Hadis (P1)
- FR-HD-1: Pilih kitab (mis. Bukhari, Muslim) dan jelajahi hadis berhalaman.
- FR-HD-2: Tampilkan Arab, terjemahan Indonesia, dan sumber/nomor hadis.
- FR-HD-3: Hadis harian di beranda (opsional).

### 4.4 Jadwal Salat (P0)
- FR-PR-1: Deteksi lokasi (Geolocation API) dengan fallback pencarian kota manual.
- FR-PR-2: Tampilkan Imsak, Subuh, Terbit, Zuhur, Asar, Magrib, Isya.
- FR-PR-3: Metode perhitungan default Kemenag RI; dapat diubah.
- FR-PR-4: Sorot waktu salat berikutnya dan hitung mundur.
- FR-PR-5 (P1): Notifikasi azan (Notification API, saat tab/PWA aktif).
- FR-PR-6 (P1): Jadwal bulanan.

### 4.5 Kalender Hijriah (P1)
- FR-CAL-1: Tampilan bulan dengan penanda Masehi dan Hijriah berdampingan.
- FR-CAL-2: Konversi tanggal Masehi ke Hijriah dan sebaliknya.
- FR-CAL-3: Penanda hari besar Islam.
- FR-CAL-4: Catatan: hasil hisab bisa berbeda satu hari dari ketetapan Kemenag/sidang isbat; tampilkan disclaimer.

### 4.6 Cuaca BMKG (P1)
- FR-WX-1: Prakiraan cuaca berdasarkan lokasi (kode wilayah administrasi BMKG).
- FR-WX-2: Prakiraan beberapa jam ke depan dan harian.
- FR-WX-3: Atribusi sumber "BMKG" wajib tampil.

### 4.7 Arah Kiblat (P1)
- FR-QB-1: Hitung arah kiblat dari koordinat pengguna (rumus great-circle, dihitung di klien).
- FR-QB-2: Kompas visual memakai DeviceOrientation bila tersedia, fallback ke derajat angka.

### 4.8 Umum (P0)
- FR-GEN-1: Mode terang dan gelap mengikuti sistem, dengan toggle manual.
- FR-GEN-2: Navigasi bawah (mobile): Beranda, Bacaan, Ibadah, Kalender, Profil.
- FR-GEN-2a: Menu Bacaan membuka halaman kumpulan fitur `/bacaan` dengan dua pilihan: Al-Qur’an (`/quran`) dan Hadis (`/hadith`). Menu Ibadah membuka `/ibadah` dengan dua pilihan: Jadwal Salat (`/prayer`) dan Arah Kiblat (`/qibla`). Semua navigasi tetap internal di MuslimSync, sesuai keputusan pengguna. Pada M0, tujuan fitur berupa placeholder; implementasi data tetap mengikuti milestone masing-masing.
- FR-GEN-2b: Pengelompokan menu tidak menghapus hitung mundur salat di Beranda (FR-HOME-2) dan Jadwal Salat (FR-PR-4), maupun teks Arab, terjemahan Indonesia, dan murottal pada fitur Al-Qur’an.
- FR-GEN-3: Pengaturan lokasi, metode salat, dan tema disimpan lokal.
- FR-GEN-4: PWA: dapat dipasang dan shell tersedia offline.

## 5. Sumber API (kandidat, wajib diverifikasi sebelum implementasi)

| Fitur | API kandidat utama | Alternatif | Catatan |
|---|---|---|---|
| Al-Qur'an | EQuran.id API v2 | Al-Quran Cloud, Quran.com API | Kemenag translation, audio, tafsir |
| Hadis | api.hadith.gading.dev | HadithAPI (butuh key) | Cek stabilitas dan lisensi; siapkan fallback |
| Jadwal Salat | Aladhan API (method Kemenag) | MyQuran API v2 | Pilih berdasarkan akurasi dan CORS |
| Kalender Hijriah | Aladhan (konversi Hijriah) | Intl API browser (`islamic-umalqura`) | Bisa beda dengan Kemenag |
| Cuaca | API prakiraan cuaca publik BMKG | — | Butuh kode wilayah adm4; cek syarat atribusi |
| Kiblat | Hitungan lokal | Aladhan qibla | Tidak perlu jaringan |
| Geocoding | Browser Geolocation + pencarian kota | Nominatim (hormati rate limit) | Perlu pemetaan ke kode wilayah BMKG |

**Aturan:** Agent wajib membuka dokumentasi resmi tiap API dan menguji endpoint nyata sebelum menulis adapter. Hasilnya dicatat di `docs/api-notes.md`. Jangan mengarang endpoint atau skema respons.

## 6. Kebutuhan Non-Fungsional

| Aspek | Target |
|---|---|
| Performa | LCP < 2,5 dtk pada 4G; JS awal < 100 KB gzip |
| Aksesibilitas | WCAG 2.1 AA; kontras terjaga; navigasi keyboard; teks Arab dengan `lang="ar"` dan `dir="rtl"` |
| Responsif | 360px sampai desktop; mobile-first |
| Keandalan | Cache stale-while-revalidate; tampil data terakhir saat offline |
| Privasi | Lokasi hanya di perangkat; tanpa tracking pihak ketiga; tanpa menyimpan data pribadi di server |
| Keamanan | Tanpa API key di klien; secret hanya di proxy; sanitasi semua data eksternal sebelum render |
| i18n | Indonesia sebagai bahasa utama; struktur siap untuk Inggris |
| Browser | 2 versi terakhir Chrome, Safari, Firefox, Edge |

## 7. Identitas Visual

- Tema: *Modern Islamic Minimalism*.
- Warna: Deep Emerald `#103D31`, Jade Green `#169873`, Soft Mint `#80DDB4`, Cloud White `#F2F6F3`, Charcoal `#182522`.
- Tipografi: Plus Jakarta Sans (heading), Inter (konten), Amiri Quran (ayat).
- Semua nilai di atas dijadikan **design tokens** (CSS custom properties).

## 8. Metrik Keberhasilan

- Lighthouse Performance, Accessibility, dan Best Practices ≥ 90.
- Salat berikutnya benar pada uji manual di minimal 3 kota (Jakarta, Makassar, Jayapura).
- Semua kartu dashboard memiliki state error yang tertangani (tidak ada layar kosong/putih).
- 100% issue MVP selesai melalui alur agent (Issue → PR → Review → Merge).

## 9. Rencana Rilis dan Backlog Awal

Setiap butir menjadi satu GitHub Issue dengan acceptance criteria. Urutan = dependensi.

### Milestone M0 — Fondasi
1. Inisialisasi proyek (SvelteKit + Svelte 5 + adapter-static, struktur folder, lint, format, CI dasar).
2. Design tokens, reset CSS, tema terang/gelap.
3. Layout aplikasi: header, navigasi bawah, routing bawaan SvelteKit (disetujui pengguna; navigasi tetap di dalam website MuslimSync).
4. API client dasar (fetch wrapper, timeout, retry, cache SWR).
5. Dokumentasi catatan API (`docs/api-notes.md`) hasil verifikasi agent perencana.

### Milestone M1 — Inti ibadah
6. Modul lokasi (geolocation, pencarian kota, penyimpanan).
7. Adapter dan UI Jadwal Salat + hitung mundur.
8. Adapter dan UI Al-Qur'an (daftar surah, detail surah).
9. Audio player murottal.
10. Dashboard beranda (kartu salat, Hijriah, ayat pilihan).

### Milestone M2 — Pelengkap
11. Kalender Hijriah.
12. Cuaca BMKG + pemetaan kode wilayah.
13. Arah Kiblat.
14. Hadis.
15. Notifikasi azan.

### Milestone M3 — Kualitas dan rilis
16. PWA (manifest, service worker, offline shell).
17. Aksesibilitas dan audit performa.
18. Deploy ke hosting statis + domain.

### Fase 2 (di luar MVP)
MuslimSync Tracker, Zakat, tafsir lengkap, akun dan sinkronisasi lintas perangkat.

## 10. Risiko

| Risiko | Dampak | Mitigasi |
|---|---|---|
| API publik tidak stabil/berubah | Fitur mati | Adapter + normalizer terpisah, cache, fallback API kedua |
| CORS ditolak API tertentu | Data tak bisa diambil dari browser | Proxy tipis (Cloudflare Worker) dengan cache |
| Perbedaan hisab Hijriah/salat | Kepercayaan pengguna | Tampilkan metode, disclaimer, default Kemenag |
| Pemetaan lokasi ke kode BMKG rumit | Cuaca salah lokasi | Pencarian wilayah manual + simpan pilihan |
| Agent murah menghasilkan kode salah | Regresi | Loop review agent mahal, CI wajib hijau, batas iterasi |
| Konten keagamaan keliru | Sensitif | Hanya tampilkan data API bersumber; sertakan atribusi |

## 11. Pertanyaan Terbuka

1. Hosting: Cloudflare Pages atau GitHub Pages?
2. Apakah proxy diperlukan sejak M0 atau hanya jika CORS bermasalah?
3. API hadis mana yang lolos uji stabilitas dan lisensi?
4. Apakah notifikasi azan cukup saat tab aktif, atau perlu push (membutuhkan backend)?
