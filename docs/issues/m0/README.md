# Issue Milestone M0 — Fondasi

Status: **lima issue telah diterbitkan** ke [MuslimSync](https://github.com/MuhammadZidniirfan05-dot/MuslimSync) dalam [Milestone M0 — Fondasi](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/milestone/1). Katalog 25 label proyek telah dibuat tanpa menghapus label bawaan. Belum ada issue agent:ready; source proyek/dokumen belum dipublikasikan ke branch GitHub, dan folder lokal belum menjadi repositori Git saat pemeriksaan.

| ID perencanaan | Backlog PRD | Draf | Dependensi / hambatan |
|---|---|---|---|
| [M0-01 / #1](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/1) | #1 | [Scaffold dan CI](./01-scaffold.md) | Dokumen sumber tersedia; izin workflow CI (needs-human) |
| [M0-02 / #2](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/2) | #2 | [Tokens dan tema](./02-theme.md) | #1 merged |
| [M0-03 / #3](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/3) | #3 | [Layout dan routing](./03-layout.md) | Menunggu #1/#2 merged; hub Bacaan dan Ibadah sudah disetujui |
| [M0-04 / #4](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/4) | #4 | [HTTP dan cache SWR](./04-http-cache.md) | #1 merged |
| [M0-05 / #5](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/5) | #5 | [Review catatan API](./05-api-review.md) | Audit sudah ada secara lokal; masih perlu PR review |

Routing bawaan SvelteKit sudah disetujui. Semua fitur tetap di website MuslimSync. Host dan fallback rute dinamis belum dipilih; scaffold dan shell dalam draf ini hanya menargetkan rute statis yang diprerender.

Milestone GitHub dan label milestone:M0 keduanya sudah dibuat. Label dari [katalog](../../../.github/labels.json) tersedia; jangan menambahkan agent:ready sebelum dependensi dan keputusan terkait selesai. Tidak ada status approved atau completed otomatis untuk pekerjaan audit yang sudah ada.

Issue menyebut path dokumen sumber dan menjelaskan bahwa dokumen masih lokal, agar tidak membuat tautan ke branch yang belum ada. Setelah dokumen dipublikasikan dengan izin pengguna, tambahkan permalink sumber yang benar sebelum agent:ready. Dokumen ini tidak mengizinkan push/merge/deploy atau implementasi fitur.

Langkah C selesai untuk penerbitan issue. Langkah D telah menghasilkan [panduan Implementer semi-manual](../../IMPLEMENTER-GUIDE.md), termasuk prompt satu issue dan alur review maksimal tiga putaran. Implementasi belum dimulai; publikasi dokumen sumber dan izin CI issue #1 masih diperlukan. Tidak ada kode aplikasi atau workflow yang diimplementasikan dalam langkah ini.
