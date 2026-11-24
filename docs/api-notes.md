# Catatan verifikasi API MuslimSync

Tanggal audit: **24 November 2026**. Status: **langkah A selesai sebagai laporan verifikasi; pilihan dan pengecualian menunggu konfirmasi pemilik proyek**. Belum ada adapter, komponen, proxy, atau perubahan pada dokumen sumber.

**Persetujuan pengguna, 24 November 2026:** EQuran.id disetujui sebagai sumber utama Qur'an (Arab, terjemahan Indonesia, murottal per ayat/surah); MyQuran v2 disetujui sebagai **kandidat pengganti** Hadis Gading. Persetujuan ini tidak menyelesaikan hak terjemahan/pagination Hadis, tidak mengaktifkan fallback Qur'an, dan tidak menetapkan keputusan A2–A7. Langkah B menyusun pedoman dan template; belum ada implementasi fitur.

**Persetujuan routing, 24 November 2026:** pengguna menyetujui routing bawaan SvelteKit setelah penjelasan bahwa navigasi tetap dalam website MuslimSync. Keputusan routing A7 selesai; hosting dan konfigurasi fallback deployment masih terbuka. PRD M0 #3 dan AGENTS.md diselaraskan dengan keputusan ini.

**Persetujuan pengelompokan menu:** menu utama adalah Beranda, Bacaan, Ibadah, Kalender, dan Profil. Rute `/bacaan` menawarkan Al-Qur’an (`/quran`) dan Hadis (`/hadith`); `/ibadah` menawarkan Jadwal Salat (`/prayer`) dan Arah Kiblat (`/qibla`). Keputusan ini menggantikan pengelompokan sebelumnya yang menaruh Hadis di Ibadah. Semua navigasi tetap internal. Hitung mundur salat tetap direncanakan di Beranda dan Jadwal Salat; Al-Qur’an tetap mencakup Arab, terjemahan Indonesia, dan murottal. M0 hanya membangun hub dan placeholder; fitur data tetap di milestone berikutnya. Repositori tujuan yang diberikan pengguna: https://github.com/MuhammadZidniirfan05-dot/MuslimSync.

Acuan: [PRD](./PRD.md), terutama §4–6, dan [ARCHITECTURE](./ARCHITECTURE.md), terutama §4–5. Stack final: SvelteKit 2.x, Svelte 5 runes, adapter-static. Versi paket stabil harus diperiksa saat scaffolding; audit ini tidak menetapkan versi paket.

## 1. Metode dan batas bukti

- Dokumentasi resmi dibuka sebelum memilih URL pengujian. Parameter contoh memakai tanggal tetap dan koordinat representatif kota, bukan lokasi pribadi pengguna.
- Request terminal menggunakan Windows `curl.exe`, HTTPS, timeout 18–25 detik, tanpa key/cookie, dengan `Origin: http://localhost:5173`. Ringkasan URL, waktu UTC mesin, status HTTP, durasi, dan header disimpan di [api-probe-results.json](./api-probe-results.json). HTTP `000` berarti tidak menerima respons HTTP, bukan kode status server.
- Browser terintegrasi juga melakukan GET dengan `credentials: 'omit'` dari origin `https://example.com`. Respons `type: cors` dan JSON yang bisa dibaca menjadi bukti CORS browser. Ini belum merupakan uji dari origin deployment MuslimSync.
- GET sederhana tidak memakai header khusus atau `Content-Type: application/json`, sehingga tidak memerlukan preflight. OPTIONS dan request berkredensial belum diuji. Header wildcard tidak membuktikan dukungan kredensial.
- Uji sesaat membuktikan keterjangkauan saat audit, bukan SLA atau stabilitas jangka panjang. Tidak ada load test. Lisensi perangkat lunak, ketentuan layanan, serta hak teks/terjemahan/audio dibedakan.
- Body konten keagamaan tidak disalin ke repositori. Bentuk respons dan metadata di bawah berasal dari respons nyata kecuali ditandai **dokumentasi saja**.

## 2. Ringkasan keputusan yang diusulkan

| Fitur / kandidat | Hasil teknis | Status untuk implementasi |
|---|---|---|
| Qur'an — EQuran v2 | Daftar/detail/tafsir 200; JSON dan CORS browser berhasil; audio dapat dimuat | Utama yang disarankan, dengan atribusi dan ketentuan EQuran |
| Qur'an — Al Quran Cloud | Arab dan Indonesia 200; CORS berhasil | Fallback bersyarat: sumber penerjemah `id.indonesian` belum jelas |
| Qur'an — Quran Foundation/Quran.com | Request tanpa autentikasi 400 | Belum lolos uji sukses; butuh registrasi, autentikasi, dan penyesuaian cache |
| Hadis — Gading | TLS gagal di terminal dan browser | Tidak layak diandalkan saat audit; skema runtime dan CORS belum terverifikasi |
| Hadis — HadithAPI | Tanpa key 403 JSON | Tidak memenuhi terjemahan Indonesia berdasarkan dokumentasi; jangan dijadikan fallback transparan |
| Hadis — MyQuran v2 (audit lanjutan) | Daftar 9 kitab serta sampel Bukhari/Muslim berhasil; Arab + Indonesia; CORS browser berhasil | Kandidat pengganti Gading yang disarankan secara teknis; hak terjemahan dan pagination perlu dituntaskan |
| Hadis — fawazahmed0 via jsDelivr (audit lanjutan) | Katalog edisi dan sampel Indonesia berhasil; CORS browser berhasil | Kandidat cadangan; sumber penerjemah tidak jelas dan pasangan Arab belum diuji |
| Salat — Aladhan | Metode 20, tiga kota, zona waktu benar pada respons; 200 + CORS | Utama bersyarat keputusan akurasi dan privasi |
| Salat — MyQuran v2 | Cari kota dan jadwal Jakarta 200 + CORS | Fallback teknis tersedia; metode/jadwal berbeda, harus diberi label sumber |
| Hijriah — Aladhan | Konversi dua arah dan kalender bulanan 200 + CORS | Layak teknis; metode kalender dan daftar hari besar perlu keputusan |
| Hijriah — Intl | Format `islamic-umalqura` berhasil di browser | Fallback tampilan lokal; bukan pengganti lengkap API dua arah |
| Cuaca — BMKG | Lokasi adm4 contoh, prakiraan 200 + CORS | Layak teknis; pemetaan lokasi ke adm4 belum diputuskan |

Belum diperlukan proxy semata-mata karena CORS untuk JSON EQuran, Aladhan, MyQuran, atau BMKG yang diuji. Proxy tidak otomatis memperbaiki TLS upstream atau ketidakjelasan hak penggunaan.

## 3. Al-Qur'an — EQuran.id v2

Sumber: [dokumentasi v2](https://equran.id/apidev/v2), [overview](https://equran.id/apidev/overview), [syarat layanan](https://equran.id/terms).

| Request nyata | Hasil |
|---|---|
| `GET https://equran.id/api/v2/surat` | 200, 114 item |
| `GET https://equran.id/api/v2/surat/1` | 200, 7 ayat |
| `GET https://equran.id/api/v2/tafsir/1` | 200, objek dengan array `tafsir` |
| `GET https://equran.id/api/v2/surat/115` | 404, `code: 404`, `message`; tidak ada `data` |

Semua respons di atas memiliki `Access-Control-Allow-Origin: *`; detail surah juga lolos GET browser. Content-Type JSON. Wrapper sukses `{ code, message, data }`.

Skema yang diamati:

- Daftar: `data[]` berisi `nomor`, `nama`, `namaLatin`, `jumlahAyat`, `tempatTurun`, `arti`, `deskripsi`, `audioFull`.
- Detail: metadata yang sama, `ayat[]`, `suratSelanjutnya`, `suratSebelumnya`. Pada surah 1, `suratSebelumnya` bernilai `false`, bukan objek.
- Ayat: `nomorAyat`, `teksArab`, `teksLatin`, `teksIndonesia`, `audio` dengan key qari `01`–`06` pada sampel.
- Tafsir: `data.tafsir[]` dengan `ayat` dan `teks`.
- Mapping internal: `nomor → number`, `nama → nameArabic`, `namaLatin → nameLatin`, `arti → meaning`, `jumlahAyat → ayahCount`, `tempatTurun → revelation`; `nomorAyat → number`, `teksArab → arabic`, `teksIndonesia → translation`, URL dari `audio[qariId] → audioUrl`.

URL audio diambil dari respons, tidak dibentuk sendiri:

- Ayat: `https://cdn.equran.id/audio-partial/Misyari-Rasyid-Al-Afasi/001001.mp3`
- Surah: `https://cdn.equran.id/audio-full/Misyari-Rasyid-Al-Afasi/001.mp3`

Keduanya HEAD 200 `audio/mpeg`, tanpa header ACAO pada hasil HEAD. Elemen `Audio` tanpa `crossOrigin` berhasil memuat ayat pertama: durasi 6,031111 detik, `readyState: 4`. Ini uji pemuatan media, bukan penilaian isi rekaman atau keberhasilan autoplay. Jangan mengasumsikan dukungan fetch blob/Web Audio; audio penuh baru diuji HEAD.

**Ketentuan:** API boleh digunakan untuk pengembangan aplikasi dengan penggunaan wajar; pembagian konten memerlukan sumber EQuran.id, data tidak boleh diperjualbelikan. Terjemahan, tafsir, dan audio tetap dilindungi hak cipta. Bukan lisensi MIT/public-domain untuk seluruh konten. Usulan kredit: “Sumber: EQuran.id; data Al-Qur'an: Kemenag RI”, disertai qari yang dipilih. Angka kuota REST v2 tidak ditemukan pada halaman yang dibaca; batas Vector Search tidak boleh diterapkan sebagai fakta kuota v2.

TTL proyek 30 hari sesuai arsitektur; HEAD/GET detail menunjukkan `max-age=2592000` dan SWR `604800`, sementara overview menyebut cache REST 24 jam. Bedakan cache server dari TTL aplikasi; periksa ulang jika kontrak berubah. Jangan render `deskripsi` atau tafsir sebagai HTML mentah.

### Alternatif Al Quran Cloud

Sumber: [referensi API](https://alquran.cloud/api), [OpenAPI resmi](https://api.alquran.cloud/v1/documentation/openapi/yaml), [ketentuan](https://alquran.cloud/terms-and-conditions).

Request nyata, seluruhnya 200 JSON dengan ACAO `*`:

- `https://api.alquran.cloud/v1/edition?language=id`: menemukan `id.indonesian`, `id.muntakhab`, `id.jalalayn`.
- `https://api.alquran.cloud/v1/surah/1`: teks Arab.
- `https://api.alquran.cloud/v1/surah/1/id.indonesian`: 7 ayat; juga lolos CORS browser.

Wrapper `{ code, status, data }`. Surah mempunyai `ayahs[]`; ayat memuat `number` global, `numberInSurah`, `text`, dan metadata juz/halaman. Gabungkan teks Arab dan terjemahan berdasarkan surah serta `numberInSurah`, bukan indeks tanpa validasi. Nama surah/arti berbahasa Indonesia tidak otomatis setara EQuran.

**Ketentuan dan blocker:** teks harus dipertahankan, kredit sumber dan penerjemah diperlukan; terjemahan/rekaman memiliki pemegang hak masing-masing. Pada respons nyata `id.indonesian.englishName` adalah `Unknown`. Jangan mengklaim terjemahan Kemenag atau menerapkan fallback otomatis sampai sumbernya diklarifikasi. Streaming rekaman memiliki batas penggunaan pribadi/edukasi dan catatan khusus komersial; audio alternatif belum diuji. Batas request berupa soft limit; angka belum diverifikasi.

### Alternatif Quran Foundation / Quran.com

Sumber: [first API call](https://api-docs.quran.com/docs/quickstart/first-api-call/), [autentikasi](https://api-docs.quran.com/docs/quickstart/manual-authentication/), [Developer Terms](https://api-docs.quran.foundation/legal/developer-terms/).

`GET https://apis.quran.foundation/content/api/v4/chapters` tanpa autentikasi menghasilkan **400**, ACAO `*`, `{ message, type: 'invalid_request', success: false }`. Belum ada bukti respons sukses atau CORS untuk alur autentikasi. Dokumentasi memerlukan `x-auth-token` dan `x-client-id`; alur backend memakai client secret yang tidak boleh dikirim ke frontend.

Ketentuan membatasi penyimpanan biasa sampai satu minggu, dengan pengecualian bersyarat melalui Content Sync dan penggunaan tertentu. TTL Qur'an 30 hari di arsitektur tidak dapat langsung dipakai untuk kandidat ini. Kredit Quran Foundation dan sumber edisi/rekaman harus dipenuhi. Tunda kandidat ini sampai autentikasi, proxy bila diperlukan, dan kebijakan penyimpanan dirancang serta diuji; tidak ada akun/key yang dibuat dalam audit.

## 4. Hadis

### Gading

Sumber: [README resmi](https://github.com/gadingnst/hadith-api), [LICENSE](https://github.com/gadingnst/hadith-api/blob/master/LICENSE).

Request nyata:

- `https://api.hadith.gading.dev/books`
- `https://api.hadith.gading.dev/books/bukhari?range=1-2`

Keduanya gagal sebelum HTTP: curl exit 35, `SEC_E_ILLEGAL_MESSAGE`. Pengulangan `/books` di luar sandbox tetap gagal. Browser juga gagal dengan `ERR_SSL_VERSION_OR_CIPHER_MISMATCH`. Alat web melaporkan 502 saat mengambil `/books`; ini tidak dijadikan status HTTP origin yang terkonfirmasi. **CORS, JSON runtime, pagination aktual, dan stabilitas belum dapat dinilai.** Verifikasi sertifikat tidak dinonaktifkan.

**Dokumentasi saja:** `/books/{name}?range={start}-{end}`, batas rentang 300; `/books/{name}/{number}` untuk satu hadis. README menyebut Arab, Indonesia, dan sumber tafsirq.com. Jangan menganggap contoh README sebagai kontrak runtime teruji.

Repo berlisensi MIT: pertahankan notice copyright/lisensi bila menyalin perangkat lunaknya. Ini tidak dengan sendirinya membuktikan izin redistribusi semua terjemahan hasil pengumpulan dari pihak ketiga. Hak konten/atribusi asal masih perlu klarifikasi. Usulan sementara: fitur Hadis menampilkan status tidak tersedia ketika sumber gagal, sesuai fallback arsitektur; jangan mengganti konten atau menerjemahkan sendiri.

### HadithAPI

Sumber: [pengantar](https://hadithapi.com/), [endpoint hadis](https://hadithapi.com/docs/hadiths).

URL terdokumentasi `https://hadithapi.com/api/hadiths/?apiKey={API_KEY}`. Tanpa key, path dengan slash menghasilkan 301; path tujuan tanpa slash `https://hadithapi.com/api/hadiths` menghasilkan 403 JSON `{ status: 403, message: 'API key is required.' }`, ACAO `*`. Respons sukses belum diuji; CORS pada respons error tidak menjamin CORS request autentikasi.

Dokumentasi hanya menyebut Arab, Urdu, Inggris. Tidak memenuhi FR-HD-2 Indonesia. API gratis tidak berarti seluruh dataset berlisensi terbuka; ketentuan lisensi/atribusi konten yang eksplisit belum ditemukan pada dokumentasi yang dibaca. Tidak direkomendasikan sebagai fallback MVP. Bila kelak dipakai, key harus di proxy, bukan query yang dibentuk frontend.

### Audit lanjutan pengganti Gading — 24 November 2026

Permintaan pengguna: cari pengganti API yang gagal dan pastikan Qur'an memiliki murottal serta terjemahan. **EQuran tetap memenuhi kebutuhan Qur'an tersebut** berdasarkan uji §3; kegagalan TLS sebelumnya terjadi pada Gading, bukan EQuran. Hasil lanjutan di bagian ini terpisah dari 25 request awal pada lampiran JSON.

**MyQuran v2 — kandidat pengganti utama secara teknis.** Sumber: [dokumentasi v2](https://api.myquran.com/v2/doc) dan [portal penyedia](https://api.myquran.com/).

| URL nyata | Bukti audit lanjutan |
|---|---|
| `https://api.myquran.com/v2/hadis/perawi` | Terminal GET 200, ACAO `*`, `status: true`, 9 kitab |
| `https://api.myquran.com/v2/hadis/bukhari/1` | Terminal dan browser GET 200; browser `type: cors`; Arab dan terjemahan Indonesia tersedia |
| `https://api.myquran.com/v2/hadis/muslim/1` | Browser GET 200, `type: cors`; field Arab dan Indonesia tersedia |

Terminal memakai `Origin: http://localhost:5173`; browser memakai origin `https://example.com`, tanpa kredensial. Daftar kitab berisi `name`, `slug`, `total`; Bukhari melaporkan 6638, Muslim 4930. Detail berisi `{ status, request, info, data }`; `info.perawi` mengidentifikasi kitab; `data` berisi `number`, `arab`, `id`. Di sini `id` adalah **teks Indonesia**, bukan identifier. Mapping usulan: `number → number`, `arab → arabic`, `id → translation`, `info.perawi → book`, sumber `MyQuran`.

Ada inkonsistensi metadata: `info.format` daftar menulis `/hadits/slug/nomor`, sedangkan URL dokumentasi dan request sukses memakai `/hadis/...`. Gunakan URL teruji, jangan mengikuti string metadata itu tanpa verifikasi. Endpoint rentang/pagination belum terverifikasi; jangan mengarang parameter `page` atau `range`. Navigasi per nomor bisa memakai endpoint detail, sedangkan daftar berhalaman FR-HD-1 memerlukan keputusan ukuran halaman dan strategi request terbatas/cache atau endpoint batch yang teruji.

Portal mengizinkan data untuk aplikasi/situs/layanan digital. Namun, identitas penerjemah, lisensi terjemahan, serta aturan atribusi hadis khusus belum ditemukan pada sumber yang dibaca. Jangan menafsirkan sumber Kemenag untuk **salat** sebagai sumber hadis. Usulan kredit aplikasi: “Sumber: MyQuran; kitab dan nomor sesuai respons”. Kandidat ini lolos keterjangkauan dan format sampel, belum audit akurasi seluruh korpus atau stabilitas jangka panjang. Hak konten tetap perlu klarifikasi sebelum publikasi.

**fawazahmed0/hadith-api — kandidat cadangan.** Sumber: [README](https://github.com/fawazahmed0/hadith-api), [Unlicense](https://github.com/fawazahmed0/hadith-api/blob/1/LICENSE), dan [References](https://github.com/fawazahmed0/hadith-api/blob/1/References.md).

- `https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1/editions.json`: terminal GET 200, ACAO `*`. Katalog Bukhari memuat edisi `ind-bukhari`, bahasa `Indonesian`, `author: 'Unknown'`, `source: ''`.
- ID edisi dari katalog digunakan pada pola URL README: `https://cdn.jsdelivr.net/gh/fawazahmed0/hadith-api@1/editions/ind-bukhari/1.json`. Browser GET 200 dengan `type: cors`.
- Respons `{ metadata, hadiths }`; item memuat `hadithnumber`, `arabicnumber`, `text`, `grades`, `reference`. Teks edisi Indonesia berada pada `text`, bukan `id`. Ini satu edisi bahasa; pasangan teks Arab dan keselarasan penomorannya belum diuji. Jangan menggabungkan dua edisi hanya berdasarkan indeks array.

Repo memakai Unlicense untuk perangkat lunak; identitas penerjemah kosong pada katalog belum menyelesaikan hak terjemahan pihak ketiga. References mencantumkan Gading di antara banyak referensi, tetapi tidak membuktikan asal setiap edisi. CDN alternatif dapat membantu ketersediaan host, bukan otomatis menjadi sumber konten independen. Tidak ada batas numerik yang diuji; tetap cache dan batasi request meski README mengiklankan tanpa rate limit. Jangan mengaktifkan sebagai fallback penuh sebelum Arab, penomoran, dan sumber terjemahan lolos pemeriksaan.

**Pilihan disetujui pengguna:** EQuran untuk Qur'an (Arab + Indonesia + murottal); MyQuran v2 sebagai kandidat pengganti Hadis Gading. Hak terjemahan dan pagination Hadis tetap terbuka. Tabel kandidat historis di PRD/arsitektur belum diubah; persetujuan ini menjadi acuan pemilihan provider.

## 5. Jadwal salat

### Aladhan

Sumber: [Prayer Times API](https://aladhan.com/prayer-times-api), [metode](https://aladhan.com/calculation-methods), [ketentuan](https://aladhan.com/credits-and-terms).

`GET https://api.aladhan.com/v1/methods` menghasilkan 200 JSON. `data.KEMENAG` berisi id `20`, parameter Fajr `20`, Isha `18`. Jangan mengandalkan default metode berdasarkan lokasi; kirim `method=20` secara eksplisit.

URL harian yang diuji:

```text
https://api.aladhan.com/v1/timings/24-11-2026?latitude=-6.2088&longitude=106.8456&method=20
https://api.aladhan.com/v1/timings/24-11-2026?latitude=-5.1477&longitude=119.4327&method=20
https://api.aladhan.com/v1/timings/24-11-2026?latitude=-2.5337&longitude=140.7181&method=20
```

Ketiganya 200 JSON, ACAO `*`; Jakarta juga lolos browser. Hasil tanggal 24 November 2026:

| Kota | Zona respons | Imsak | Subuh | Terbit | Zuhur | Asar | Magrib | Isya |
|---|---|---|---|---|---|---|---|---|
| Jakarta | Asia/Jakarta | 03:53 | 04:03 | 05:26 | 11:39 | 15:04 | 17:52 | 19:07 |
| Makassar | Asia/Makassar | 04:04 | 04:14 | 05:37 | 11:49 | 15:14 | 18:00 | 19:15 |
| Jayapura | Asia/Jayapura | 03:44 | 03:54 | 05:16 | 11:24 | 14:49 | 17:31 | 18:45 |

Wrapper `{ code, status, data }`; `data.timings` berisi waktu; `data.date` berisi tanggal Gregorian/Hijriah; `data.meta` memuat timezone, metode, school, offset. Mapping times: `Imsak/Fajr/Sunrise/Dhuhr/Asr/Maghrib/Isha → imsak/fajr/sunrise/dhuhr/asr/maghrib/isha`. Gunakan tanggal + zona lokasi untuk countdown, bukan zona perangkat. Setelah Isya ambil jadwal esok; jangan mengulang Subuh hari ini. Uji countdown tersebut masuk implementasi, belum dilakukan di audit API.

**Akurasi:** metode bernama Kemenag adalah pilihan perhitungan Aladhan, bukan jaminan identik jadwal resmi Kemenag. Tiga kota baru lulus pemeriksaan bentuk data/zona waktu, bukan validasi resmi atau target metrik salat berikutnya di PRD. Jangan menambahkan koreksi menit tanpa keputusan pemilik dan pembanding resmi.

**Ketentuan:** layanan gratis tanpa akun, tanpa garansi; halaman ketentuan menyatakan log tidak disimpan lebih dari 29 hari. Tidak ditemukan kewajiban teks atribusi khusus pada halaman itu; usulan produk tetap “Aladhan — metode Kemenag RI”. Ini bukan pernyataan lisensi terbuka seluruh konten. Request koordinat membuat penyedia menerima koordinat; klaim “lokasi hanya di perangkat” perlu koreksi atau desain lain.

Cache 24 jam dengan key lokasi, tanggal, zona waktu, metode, school, dan penyesuaian. Jangan menukar jadwal dari metode/sumber berbeda tanpa metadata sumber. Endpoint bulanan salat belum diuji; FR-PR-6 perlu verifikasi tambahan sebelum adapter bulanan.

### MyQuran v2

Sumber: [portal resmi](https://api.myquran.com/), [dokumentasi v2](https://api.myquran.com/v2/doc). Portal juga menawarkan v3; audit mempertahankan v2 sesuai kandidat PRD.

- `GET https://api.myquran.com/v2/sholat/kota/cari/jakarta` → 200, `data: [{ id: '1301', lokasi: 'KOTA JAKARTA' }]`.
- ID hasil pencarian dipakai untuk `GET https://api.myquran.com/v2/sholat/jadwal/1301/2026/11/24` → 200, `status: true`, `request`, `data`.
- Keduanya ACAO `*`; jadwal juga lolos browser. `data` berisi `id`, `lokasi`, `daerah`, `jadwal`; waktu berada pada `imsak`, `subuh`, `terbit`, `dhuha`, `dzuhur`, `ashar`, `maghrib`, `isya`, dengan `date` ISO. Sampel tidak menyediakan zona waktu atau parameter ganti metode.

Jakarta: Imsak 03:55, Subuh 04:05, Terbit 05:23, Zuhur 11:43, Asar 15:06, Magrib 17:55, Isya 19:09. Perbedaan terhadap Aladhan bukan bukti salah satu salah: referensi koordinat/perhitungan dapat berbeda. MyQuran tidak dapat dianggap pengganti setara untuk semua metode pilihan pengguna.

Portal mengizinkan penggunaan untuk aplikasi/situs/layanan digital dan mengaitkan jadwal dengan Bimas Islam Kemenag. Lisensi data bernama serta kewajiban atribusi spesifik belum ditemukan pada halaman yang diperiksa. Usulan kredit: “MyQuran — sumber jadwal Bimas Islam Kemenag RI”. Dokumentasi memperingatkan bahwa kegagalan data tertentu bisa tetap HTTP 200; validasi `status` dan isi `data`, tangani 429/Retry-After. Kuota numerik belum diverifikasi. Kota lain, data bulanan, dan pemetaan zona waktu masih perlu diuji sebelum mengaktifkan fallback luas.

## 6. Hijriah

Sumber: [dokumentasi Aladhan](https://aladhan.com/islamic-calendar-api), [OpenAPI resmi](https://api.aladhan.com/v1/documentation/openapi/islamic-calendar/yaml). Ketentuan sama dengan Aladhan di atas.

| Request nyata | Hasil |
|---|---|
| `https://api.aladhan.com/v1/gToH/24-11-2026` | Gregorian 24-11-2026 → Hijriah 14-06-1448 |
| `https://api.aladhan.com/v1/hToG/14-06-1448` | Kembali ke Gregorian 24-11-2026 |
| `https://api.aladhan.com/v1/gToHCalendar/11/2026` | 30 pasangan tanggal untuk November |

Semua 200 JSON dengan ACAO `*`, lolos browser. Wrapper `{ code, status, data }`; `data` objek untuk konversi, array untuk kalender. Setiap pasangan memiliki `hijri` dan `gregorian`; day/year berbentuk string, `month.number` angka. Default yang diamati `hijri.method: 'HJCoSA'`, bukan ketetapan Kemenag. Contoh model di arsitektur menulis 13 Jumadil Akhir untuk tanggal uji; jangan menyalin contoh itu sebagai patokan hasil tanggal.

Respons juga memuat `holidays` yang mencakup peringatan di luar hari besar yang umum di Indonesia. **Jangan menampilkan seluruh array sebagai hari besar resmi Indonesia.** FR-CAL-3 memerlukan definisi hari besar dan sumber yang disetujui. Metode kalender default juga perlu dipilih secara eksplisit dan konsisten antara dashboard, salat, kalender, serta cache.

Fallback browser yang benar-benar diuji:

```js
new Intl.DateTimeFormat('id-ID-u-ca-islamic-umalqura', {
  dateStyle: 'full', timeZone: 'Asia/Jakarta'
}).format(new Date('2026-11-24T05:00:00Z'));
```

Hasil: `Selasa, 14 Jumadilakhir 1448 H`. Kesamaan satu tanggal tidak membuktikan UAQ dan HJCoSA selalu sama. Intl hanya diuji untuk format Gregorian → Hijriah; tidak menyediakan API konversi balik langsung atau daftar hari besar. Tandai metode fallback dan tampilkan disclaimer perbedaan dengan sidang isbat. Cache 24 jam, pisahkan berdasarkan tanggal/metode/penyesuaian.

## 7. Cuaca BMKG dan lokasi

Sumber: [dokumentasi resmi prakiraan](https://data.bmkg.go.id/prakiraan-cuaca/).

`GET https://api.bmkg.go.id/publik/prakiraan-cuaca?adm4=31.71.03.1001` → 200 JSON, ACAO `*`, lolos browser. Kode contoh berasal dari dokumentasi dan respons menyebut Kemayoran, Jakarta Pusat. Root `{ data, lokasi }`; `lokasi` memuat adm1–adm4, nama wilayah, lat/lon, timezone. Slot berada di `data[0].cuaca[dayIndex][slotIndex]` pada sampel; ada tiga kelompok hari. Jangan mengasumsikan jumlah slot hari pertama selalu penuh.

Slot pertama yang diamati: `datetime: '2026-11-24T03:00:00Z'`, `t: 32`, `weather_desc: 'Cerah Berawan'`, `hu: 70`, `ws: 2.8`, `local_datetime: '2026-11-24 10:00:00'`; `vs` dan `vs_text` null. `analysis_date: '2026-11-24T00:00:00'`. Mapping: `datetime → time`, `t → tempC`, `weather_desc → condition`, `image → icon`, `hu → humidity`, `ws → windKph`. Prefer waktu dengan `Z`; jangan parse string waktu lokal sebagai zona perangkat. URL ikon berisi spasi pada sampel; validasi URL HTTPS, jangan menyisipkan SVG eksternal sebagai markup.

Uji negatif `adm4=invalid` → 400 JSON `{ error: 'Parameter adm tidak valid' }`, ACAO `*`.

**Kontrak publik:** prakiraan tiga hari dengan interval tiga jam, pemutakhiran dua kali sehari; batas 60 request/menit/IP. Atribusi **BMKG (Badan Meteorologi, Klimatologi, dan Geofisika)** wajib terlihat di aplikasi. Tidak ditemukan lisensi standar seperti CC pada halaman ini; jangan menyebutnya public domain. Cache aplikasi 30–60 menit menurut arsitektur, simpan waktu produksi dan label stale. Ini prakiraan, bukan observasi cuaca saat ini.

**Pemetaan belum terselesaikan:** koordinat GPS, ID kota MyQuran, dan adm4 bukan identifier yang dapat saling digantikan. Tidak ada endpoint koordinat → adm4 yang diverifikasi dalam audit ini. Usulan awal adalah pilihan wilayah manual; tetap perlu sumber daftar wilayah yang sah dan terverifikasi sebelum membuat pencariannya. Jangan memakai Kemayoran sebagai fallback diam-diam untuk semua lokasi.

### Geocoding dan kiblat

Kiblat dihitung lokal sesuai FR-QB-1; tidak perlu menguji atau mengaktifkan API kiblat untuk memenuhi desain saat ini.

Nominatim adalah kandidat pendukung, belum dipilih atau diuji endpoint-nya. [Kebijakan resminya](https://operations.osmfoundation.org/policies/nominatim/) membatasi total aplikasi maksimum 1 request/detik, memerlukan identitas aplikasi dan atribusi OpenStreetMap, serta melarang autocomplete klien. Data berlisensi ODbL; cache dan kemampuan mengganti penyedia diperlukan. Pemilihan harus merupakan keputusan pengembang yang memahami kebijakan, bukan default otomatis. Nominatim juga belum dibuktikan memberikan kode adm4 BMKG yang kompatibel.

## 8. Konsekuensi implementasi dan pengujian ulang

1. Semua request JSON melalui `src/lib/core/http.js`, lalu service/normalizer, kemudian model internal. UI tidak mengetahui kontrak mentah; service mengembalikan `{ data, source, fetchedAt, stale }` atau error terkelola.
2. Validasi status HTTP **dan** payload; tangani JSON salah bentuk, array kosong, null, serta timeout. Retry GET maksimal dua kali dengan backoff menurut arsitektur; jangan retry 400/401/403/404 secara membabi buta. Hormati 429/Retry-After.
3. Render teks eksternal sebagai teks yang di-escape Svelte. Tanpa `{@html}`/`innerHTML`; jangan mengubah isi ayat/terjemahan untuk sanitasi. Validasi tipe dan URL media terpisah.
4. Cache harus memuat versi skema dan identitas sumber/metode/edisi. Sumber fallback perlu terlihat; cache tidak boleh mencampur terjemahan atau jadwal yang berbeda.
5. Untuk audio `<audio src>` dan ikon `<img src>`, pemuatan oleh browser berbeda dari `fetch` JSON. Perjelas aturan arsitektur untuk pemuatan media saat menulis pedoman implementer.
6. Sebelum rilis: ulangi CORS dari origin deployment, uji perangkat/browser target, error/offline/stale, audio setelah interaksi pengguna, dan pembanding jadwal resmi tiga kota. Sebelum adapter tambahan: verifikasi endpoint yang belum diuji (salat bulanan, katalog adm4, dan fallback audio).

## 9. Keputusan yang perlu konfirmasi

| ID | Ambiguitas / hambatan | Usulan untuk diputuskan, bukan keputusan sepihak |
|---|---|---|
| A1 | Gading gagal TLS; audit lanjutan MyQuran berhasil untuk Arab + Indonesia | Gunakan MyQuran v2 sebagai kandidat pengganti; tuntaskan hak terjemahan dan strategi pagination sebelum aktivasi; state tidak tersedia saat gagal |
| A2 | API berbasis koordinat bertentangan dengan “lokasi hanya di perangkat” | Izinkan pengiriman lokasi ke penyedia untuk fitur terkait dengan penjelasan dan persetujuan pengguna, tanpa penyimpanan oleh MuslimSync; jangan menjanjikan penyedia tidak mencatat request |
| A3 | Metode Kemenag Aladhan tidak sama persis dengan MyQuran | Pertahankan Aladhan method 20 sebagai hasil perhitungan berlabel, wajib pembanding resmi sebelum rilis; atau prioritaskan jadwal resmi kota |
| A4 | Metode Hijriah dan cakupan hari besar belum ditentukan | Pilih HJCoSA atau UAQ secara eksplisit; tetapkan sumber/daftar hari besar Indonesia, bukan semua `holidays` upstream |
| A5 | Hak/sumber fallback Qur'an Indonesia belum jelas | EQuran utama; jangan aktifkan fallback terjemahan Al Quran Cloud sampai penerjemah dan ketentuannya jelas |
| A6 | GPS → adm4 belum ada sumber pemetaan terverifikasi | Setujui pilihan wilayah manual sebagai tahap awal, kemudian verifikasi sumber katalog wilayah |
| A7 | Routing bawaan SvelteKit disetujui pengguna | Selesai untuk routing; navigasi fitur tetap di MuslimSync. Hosting dan fallback deployment belum dipilih |

Langkah B selesai; berkas pedoman/template tersedia lokal dan label sudah dibuat di repositori. Langkah C menerbitkan lima issue M0; lihat [daftar issue](./issues/m0/README.md). Langkah D menghasilkan [panduan Implementer semi-manual](./IMPLEMENTER-GUIDE.md); implementasi belum dimulai. PRD/arsitektur diselaraskan dengan routing serta hub Bacaan dan Ibadah yang disetujui; keputusan API lain tetap terbuka.
