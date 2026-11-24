# M0-05 — Review dan finalisasi catatan verifikasi API

Status: **diterbitkan ke GitHub** — [Issue #5](https://github.com/MuhammadZidniirfan05-dot/MuslimSync/issues/5). ID M0 adalah ID perencanaan.
Milestone tujuan: **M0 — Fondasi**.
Label rencana: `type:chore`, `area:docs`, `milestone:M0`. Belum diberi `agent:ready`.

## Konteks

PRD §5 dan §9 M0 #5; ARCHITECTURE §5. Audit telah menghasilkan catatan dan bukti request; issue ini memverifikasi hasil tersebut, bukan mengarang ulang endpoint.

Acuan: [PRD](../../PRD.md), [arsitektur](../../ARCHITECTURE.md), [aturan agent](../../../AGENTS.md), [catatan API](../../api-notes.md).

## Scope

Review catatan audit, tautan resmi, metadata pengujian, skema, CORS, lisensi/atribusi, keputusan pengguna, keterbatasan, dan backlog verifikasi yang tersisa. Lengkapi bukti metadata audit lanjutan Hadis bila diperlukan.

## Di luar scope

Adapter/komponen, pergantian provider tanpa keputusan, akun/API key baru, menyalin corpus ke repositori, otomatis menyatakan semua lisensi selesai, atau menyelesaikan kebijakan produk A2–A6 secara sepihak.

## File terkait

`docs/api-notes.md`, `docs/api-probe-results.json`; dokumen keputusan tambahan hanya bila ada keputusan eksplisit baru.

Path di atas adalah target perubahan, bukan klaim bahwa file sudah ada.

## Dependensi dan keputusan

Bahan audit langkah A sudah tersedia; tidak bergantung scaffold. Repositori dan PR review diperlukan agar pekerjaan dokumentasi masuk alur Issue → PR → Review. Tidak otomatis ditutup karena berkas sudah ada.

## Acceptance criteria

- [ ] AC-1 — Lima fitur API tercakup: Qur'an, Hadis, salat, Hijriah, cuaca. Setiap kandidat mempunyai URL dokumentasi resmi, endpoint aktual, hasil atau alasan gagal/belum diuji, serta tanggal audit.
- [ ] AC-2 — Bukti membedakan GET JSON, HEAD audio, native audio, header CORS, dan CORS browser; tidak menyimpulkan SLA, akurasi agama, atau izin konten dari HTTP 200.
- [ ] AC-3 — EQuran tercatat sebagai utama disetujui dengan Arab/Indonesia/murottal; MyQuran Hadis sebagai kandidat disetujui dengan hak terjemahan/pagination terbuka. Gading gagal dan fallback yang belum disetujui tidak ditandai siap.
- [ ] AC-4 — Lisensi perangkat lunak dibedakan dari hak terjemahan/audio dan ketentuan layanan. Atribusi BMKG/EQuran serta keterbatasan penyedia lain tercatat dengan tautan.
- [ ] AC-5 — Metadata request dapat dibaca sebagai JSON dan tidak berisi secret, lokasi pribadi, atau body corpus. Audit lanjutan dibedakan dari 25 request awal; tidak mengklaim bukti yang tidak ada.
- [ ] AC-6 — Persetujuan routing SvelteKit tercatat; keputusan lokasi, akurasi salat, metode Hijriah/hari besar, fallback terjemahan dan pemetaan adm4 tetap terlihat terbuka.
- [ ] AC-7 — PR menjelaskan apa yang direview, koreksi yang dilakukan, tautan rusak bila ada, dan verifikasi tambahan yang dibutuhkan sebelum adapter/launch. Pemeriksaan npm tidak berlaku jika proyek belum memiliki tooling.

## Catatan teknis

Jangan melakukan ulang semua request jika buktinya cukup; ulangi hanya URL/skema yang meragukan atau berubah dan simpan hasil terbaru tanpa menghapus sejarah kegagalan. Review manusia diperlukan untuk kesimpulan konten agama/logika waktu; daftar API tersedia bukan endorsement akurasi.

## Verifikasi

Parse JSON, periksa konsistensi URL dan status terhadap catatan, cek tautan sumber relevan serta keputusan pengguna. Tidak ada unit test aplikasi untuk perubahan dokumentasi murni.

## Definition of Done

- [ ] Semua AC dipetakan ke bukti di PR; tidak ada keputusan wajib yang masih ditebak.
- [ ] Pemeriksaan relevan lulus; hasil yang tidak dijalankan/tidak berlaku diberi alasan eksplisit.
- [ ] Kode dan komentar Inggris, UI Indonesia; tidak ada debugging console.log atau secret.
- [ ] Tidak ada perubahan di luar scope; dependensi baru dijustifikasi; dokumentasi terkait diperbarui.
- [ ] Reviewer memberi APPROVE pada commit terbaru; maksimal tiga putaran, lalu needs-human.
- [ ] Persetujuan manusia untuk perubahan yang dilindungi tersedia bila relevan; merge akhir oleh manusia.
