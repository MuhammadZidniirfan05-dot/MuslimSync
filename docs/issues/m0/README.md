# Issue Milestone M0 — Fondasi

Status: **lima issue telah diterbitkan** ke [MuslimSync](https://github.com/MuhammadZidniirfan05-dot/MuslimSync) dalam [Milestone M0 — Fondasi](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/milestone/1). Katalog 25 label proyek telah dibuat tanpa menghapus label bawaan. Dokumen/template telah dipublikasikan ke branch `main` dan folder lokal telah menjadi repositori Git. Prasyarat issue #1 sudah dipenuhi; status terkini setiap issue mengikuti label GitHub.

| ID perencanaan | Backlog PRD | Draf | Dependensi / hambatan |
|---|---|---|---|
| [M0-01 / #1](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/1) | #1 | [Scaffold dan CI](./01-scaffold.md) | Dokumen tersedia; izin CI sudah dicatat; siap untuk Implementer |
| [M0-02 / #2](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/2) | #2 | [Tokens dan tema](./02-theme.md) | #1 merged |
| [M0-03 / #3](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/3) | #3 | [Layout dan routing](./03-layout.md) | Menunggu #1/#2 merged; hub Bacaan dan Ibadah sudah disetujui |
| [M0-04 / #4](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/4) | #4 | [HTTP dan cache SWR](./04-http-cache.md) | #1 merged |
| [M0-05 / #5](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/5) | #5 | [Review catatan API](./05-api-review.md) | Audit sudah ada secara lokal; masih perlu PR review |

Routing bawaan SvelteKit sudah disetujui. Semua fitur tetap di website MuslimSync. Host dan fallback rute dinamis belum dipilih; scaffold dan shell dalam draf ini hanya menargetkan rute statis yang diprerender.

Milestone GitHub dan label milestone:M0 keduanya sudah dibuat. Label dari [katalog](../../../.github/labels.json) tersedia; jangan menambahkan agent:ready sebelum dependensi dan keputusan terkait selesai. Tidak ada status approved atau completed otomatis untuk pekerjaan audit yang sudah ada.

Issue menautkan dokumen sumber pada commit publikasi. Baca juga komentar keputusan terbaru dan dokumen pada branch dasar sebelum mulai. Publikasi awal telah diizinkan pengguna; merge/deploy dan implementasi fitur tidak dijalankan dalam langkah publikasi ini.

Langkah C selesai untuk penerbitan issue. Langkah D telah menghasilkan [panduan Implementer semi-manual](../../IMPLEMENTER-GUIDE.md), termasuk prompt satu issue dan alur review maksimal tiga putaran. Publikasi dokumen sumber dan izin CI issue #1 telah selesai. Tidak ada kode aplikasi atau workflow yang diimplementasikan dalam langkah ini.
