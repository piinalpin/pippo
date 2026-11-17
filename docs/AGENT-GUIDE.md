# Panduan Agent Pippo

Versi 1.1, diperbarui 2026-11-17. [Indeks](README.md) · [Current state](CURRENT-STATE.md) · [Codemap](CODEMAP.md)

## 1. Urutan baca

Mulai dengan README dan CURRENT-STATE untuk mengetahui scope dokumen, kondisi aktual, bukti dan langkah berikutnya. Baca PRD untuk tujuan/constraints. Jangan memindai seluruh source hanya untuk mengetahui status atau daftar fitur.

| Tugas | Dokumen lanjutan yang perlu dibaca |
|---|---|
| Menjawab status/progres | CURRENT-STATE; CHANGELOG bila perlu riwayat. |
| Meninjau scope atau merancang fitur | PRD + bagian FR terkait di FEATURES + OQ terkait di DECISIONS. |
| Memahami kode atau mencari lokasi perubahan | CODEMAP: indeks modul, kartu modul, kontrak §4. Buka hanya file yang disebut kartu beserta test pemiliknya. |
| Implementasi/perbaikan fitur | Kontrak FR/UX terkait, kartu modul di CODEMAP, ARCHITECTURE boundary, TECH-STACK dan VALIDATION; kemudian source/test yang disebut kartu. |
| Menyiapkan environment | SETUP + TECH-STACK; pastikan source/build sudah tersedia sebelum menjalankan build. |
| Mengubah dependency atau integrasi API | TECH-STACK + FR/OQ terkait; verifikasi docs resmi versi yang dipakai sebelum coding. |
| Menyiapkan rilis | PRD gate/fase + VALIDATION + CURRENT-STATE + CHANGELOG; audit berkas/artefak nyata. |

Docs cukup untuk onboarding, planning, status dan penjelasan perilaku yang sudah terdokumentasi. Untuk implementasi, bug diagnosis atau klaim hasil uji, verifikasi source/artefak terkait; dokumentasi tidak menggantikan pemeriksaan kebenaran kode. Jika bukti belum ada, tulis belum diverifikasi.

## 2. Membaca status dengan benar

- `draft` adalah status persetujuan dokumen. `proposed` / `planned` menyatakan desain/requirement, bukan kemampuan yang tersedia.
- `not-started` → `in-progress` → `implemented` → `verified` → `released` adalah label status kerja **(agent's call)**. Gunakan hanya tahap yang mempunyai bukti. `backlog` untuk P2 belum dijadwalkan; `blocked` harus menyebut dependensi dan pekerjaan yang terpengaruh.
- `implemented` belum berarti seluruh gate lulus. `verified` menyebut lingkungan/skenario/bukti; `released` menyebut versi dan lokasi artefak. Jangan menaikkan status karena docs atau mock saja sudah lengkap.
- DECIDED berasal dari keputusan yang tercatat; CONDITIONAL menunggu kondisi; OPEN belum diputus; DEFERRED ditunda; CLOSED/REMOVED tidak dikerjakan. Rekomendasi agent tetap rekomendasi sampai owner memutuskan.
- Minimum dependency kandidat, API method pada desain, folder tree target dan angka performance bukan fakta runtime. Tahun/tanggal/nomor versi dokumen tidak menentukan versi binary.

## 3. Memperbarui docs bersama perubahan

| Jenis perubahan | Dokumen pemilik yang diperbarui |
|---|---|
| Scope, goal, priority atau fase produk | PRD; DECISIONS untuk keputusan/approval; FEATURES bila kontrak berubah. |
| Behavior/default/limitation fitur | FEATURES; DECISIONS jika menjawab OQ; CURRENT-STATE untuk keadaan nyata. |
| Folder/module, data flow, storage atau ownership (rancangan) | ARCHITECTURE. |
| Folder/module, target CMake, public type, wiring, kontrak data/IPC/config, perintah build/test yang dibuat atau diubah di kode | CODEMAP pada perubahan yang sama; deviasi dari ARCHITECTURE ke CODEMAP §6. |
| Versi/dependency, build/tooling | TECH-STACK dan SETUP; catat hasil verifikasi. |
| Test/gate atau coverage baru | VALIDATION untuk ekspektasi; CURRENT-STATE untuk hasil dan link laporan. |
| Progres, blocker, dukungan desktop/adapter, hasil benchmark | CURRENT-STATE dengan bukti dan batas klaim. |
| Perubahan yang perlu diketahui penerus | CHANGELOG pada bagian yang sesuai; README jika dokumen/versi berubah. |

Update pada change yang sama, jangan menunda docs sampai akhir rilis. Tautkan pemilik detail daripada menyalin tabel lengkap ke banyak tempat. Pertahankan ID C/Q/FR/OQ; tambahkan ID baru, jangan mendaur ulang yang dibatalkan.

Setelah ada implementasi, catat command menjalankan/build/test, lokasi source pemilik, path config/data aktual, public CLI/IPC contract, migration dan limitation yang benar-benar berlaku di CODEMAP (kartu modul, §4, §5). Saat ini semuanya belum tersedia; jangan membuat contoh yang seolah executable atau schema final.

Peta yang basi lebih merugikan daripada tidak ada peta. Jika kartu CODEMAP menyebut path yang tidak ada atau public type yang sudah berubah, perbaiki kartunya pada perubahan yang sama sebelum melanjutkan. Jalankan pemeriksaan cepat di CODEMAP §7 sebelum menutup pekerjaan yang menambah atau memindah folder.

## 4. Bukti dan handoff

Setiap klaim hasil perlu tanggal, ID requirement, revisi source/commit jika tersedia, environment, perintah/skenario, pass/fail dan tautan laporan. Pengujian Wayland harus menyebut compositor/portal; uji salah satu desktop tidak membuktikan keduanya. Exception release perlu persetujuan owner dan batas klaim.

Simpan laporan nyata di `docs/` dengan nama deskriptif ketika tersedia. Diagnostics/fixture tidak memuat credential, payload clipboard, prompt, notifikasi privat atau meeting token. Synthetic data diberi label; contoh rate FX bukan kurs live.

Tutup pekerjaan dengan memperbarui snapshot status, hasil uji, gap yang tersisa dan next steps. CHANGELOG menjelaskan apa yang berubah; CURRENT-STATE menjelaskan kondisi akhirnya. Jangan menulis “semua selesai” jika test/gate relevan belum dijalankan.

## 5. Freshness dan konflik

Tanggal verifikasi menunjukkan kapan klaim dicocokkan dengan bukti, bukan jaminan docs akan selalu benar. Jika source dan docs berbeda, catat perbedaannya; perbaiki dokumen status/kontrak sesuai keputusan yang berwenang. Jangan diam-diam mengubah requirement untuk menyesuaikan bug implementasi.

Bump versi dokumen ketika substansi berubah; catat ringkas di CHANGELOG dan sinkronkan indeks. Status PRD menjadi approved hanya setelah owner menyetujui revisi yang sama. Buat ADR hanya untuk keputusan teknis signifikan dengan rationale/bukti; jangan menautkan file archive/ADR yang belum ada.

Pada pergantian agent, teruskan keputusan yang berlaku, scope aktif, pekerjaan/bukti yang sudah ada, blocker, risiko dan langkah berikutnya melalui docs. Tidak perlu mengulang penelusuran penuh source untuk konteks yang sudah tercatat: lokasi kode ada di CODEMAP, status di CURRENT-STATE.
