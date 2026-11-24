## Ringkasan

<!-- Jelaskan masalah konkret dan perilaku setelah perubahan. -->

Closes #<nomor-issue>

## Bukti acceptance criteria

| Kriteria issue | Implementasi dan bukti verifikasi |
|---|---|
| AC-1 | |

## Cara menguji

<!-- Langkah yang dapat diulang, hasil yang diharapkan, dan lingkungan/commit. -->

1.

| Pemeriksaan | Hasil aktual dan bukti / alasan tidak berlaku |
|---|---|
| `npm run lint` | Belum dijalankan |
| `npm test` | Belum dijalankan |
| `npm run build` | Belum dijalankan |
| E2E / aksesibilitas / performa yang relevan | Belum diperiksa |
| CI pada commit terbaru | Tautan run dan status |

## Catatan reviewer

<!-- Cantumkan hanya yang relevan; tulis tidak berlaku jika kosong. -->

- Dokumentasi resmi Svelte/SvelteKit yang dibaca untuk perubahan komponen:
- API teruji, perubahan model, sumber/atribusi, dan tautan catatan API:
- Dependensi baru dan alasannya:
- Risiko, keterbatasan, atau keputusan yang belum selesai:
- Tautan persetujuan manusia untuk perubahan AGENTS/workflow/secret, bila ada:
- Review manusia konten agama/logika salat, bila diperlukan:

## Screenshot

<!-- Untuk UI: mobile/desktop dan tema/state yang berubah. Jika tidak ada perubahan UI, tulis tidak berlaku. -->

## Checklist

<!-- Centang hanya setelah diverifikasi. Jelaskan item yang tidak berlaku, jangan mengklaim sudah diuji. -->

- [ ] Semua acceptance criteria terpenuhi; scope sesuai issue.
- [ ] Service → model → UI, fetch terpusat, dan Svelte 5 runes dipatuhi.
- [ ] Data eksternal aman; tanpa HTML mentah atau secret di klien.
- [ ] Loading/error/kosong/stale diperiksa sesuai perubahan.
- [ ] Test bermakna dan pemeriksaan wajib lulus dengan bukti di atas.
- [ ] Aksesibilitas dan bahasa UI Indonesia diperiksa jika relevan.
- [ ] Tanpa console.log debugging; dependensi baru dijustifikasi.
- [ ] Sumber/atribusi dan dokumentasi terkait diperbarui.

## Pelacakan review

<!-- Reviewer mengisi putusan dalam review GitHub, bukan mengandalkan checklist penulis PR. -->

- Putaran terakhir: belum direview (maksimal 3).
- Commit yang direview dan tautan review:
- Putusan: `APPROVE` / `REQUEST_CHANGES`.
- Jika putaran 3 masih gagal: `needs-human`, ringkasan blocker, hentikan loop.

Merge akhir dilakukan manusia setelah seluruh syarat terpenuhi.
