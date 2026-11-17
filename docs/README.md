# Dokumentasi Pippo

Pippo adalah rencana aplikasi floating island untuk Linux, menggunakan C++20 + Qt6/QML dengan target KDE Plasma Wayland dan Hyprland x86_64.

**Status workspace per 2026-11-17: tahap spesifikasi. Belum ada source aplikasi, build, test, paket atau bukti benchmark.** Daftar fitur di docs adalah target produk. Mulai dari CURRENT-STATE untuk membedakan rencana dari hasil nyata.

## Mulai membaca

1. [CURRENT-STATE.md](CURRENT-STATE.md): apa yang ada, progres, bukti, batasan, dan langkah berikutnya.
2. [PRD.md](PRD.md): masalah, pengguna, tujuan, scope, capabilities, quality bars dan fase.
3. [AGENT-GUIDE.md](AGENT-GUIDE.md): rute baca sesuai tugas dan aturan menjaga docs tetap sinkron.
4. [CODEMAP.md](CODEMAP.md): untuk tugas yang menyentuh kode. Peta modul, entry point dan kontrak yang benar-benar ada, supaya cukup membuka file yang relevan.

Untuk memahami proyek/status, tiga dokumen pertama cukup sebagai titik masuk tanpa menelusuri source. Untuk memahami kode, CODEMAP menunjukkan file mana yang perlu dibuka; ketika mengimplementasikan perubahan, source dan hasil uji terkait tetap perlu diverifikasi.

## Dokumen aktif dan pemilik informasi

| Dokumen | Versi | Memiliki informasi tentang |
|---|---|---|
| [CURRENT-STATE.md](CURRENT-STATE.md) | 1.19 | Kondisi aktual, feature status, bukti uji/rilis, gap dan next steps. |
| [PRD.md](PRD.md) | 0.6.18 draft | Kebutuhan produk, C-1 sampai C-12, Q-1 sampai Q-7, scope/fase dan rollout. |
| [FEATURES.md](FEATURES.md) | 1.19 planned | UX shell dan kontrak rinci FR-01 sampai FR-22, acceptance criteria. |
| [DECISIONS.md](DECISIONS.md) | 1.19 | Keputusan tercatat, OQ tetap, pertanyaan aktif, owner/blocker. |
| [ARCHITECTURE.md](ARCHITECTURE.md) | 1.11 proposed | Rancangan dependency, target source layout, boundaries, threading, storage. |
| [CODEMAP.md](CODEMAP.md) | 1.0 (kosong) | Layout source aktual: modul, entry point, public type, kontrak data/IPC, build/test, deviasi dari ARCHITECTURE. |
| [TECH-STACK.md](TECH-STACK.md) | 1.4 proposed | Dependency/version policy, modul, contoh build tunggal dan catatan API yang dicek ke dokumentasi resmi. |
| [SETUP.md](SETUP.md) | 1.1 preparation | Prerequisites dan kandidat perintah persiapan; build belum dapat dijalankan. |
| [VALIDATION.md](VALIDATION.md) | 1.15 planned | Target compatibility, security/packaging/test matrix dan release gates. |
| [CHANGELOG.md](CHANGELOG.md) | Log | Perubahan dokumentasi yang tercatat; bagian rilis aplikasi terpisah. |
| [AGENT-GUIDE.md](AGENT-GUIDE.md) | 1.1 | Prosedur onboarding/handoff, perubahan docs, bukti dan freshness. |

Versi di tabel adalah **versi dokumen**, bukan versi aplikasi. Dokumen proposed/planned tidak menjadi implemented hanya karena lengkap ditulis.

## Aturan sumber kebenaran

Setiap jenis informasi dimiliki satu dokumen pada tabel di atas. Dokumen lain menautkan rincian, bukan menyimpan salinan lengkapnya. Jika ada konflik, catat gap dan sinkronkan dokumen pemilik; jangan diam-diam mengubah requirement menjadi apa yang kebetulan sudah dikerjakan.

- Keputusan owner berstatus DECIDED yang diwarisi dari PRD lama tetap berlaku; revisi PRD baru masih draft sampai disetujui.
- Target/fase berada di PRD; perilaku rinci di FEATURES; bukti/status aktual di CURRENT-STATE. Keduanya dapat berbeda tanpa berarti target sudah tercapai.
- Angka benchmark adalah target sampai laporan hasil dapat dirujuk. Link API/upstream bukan hasil uji integrasi.
- `docs/adr/` dan `docs/archive/` **belum ada**. Buat hanya saat mempunyai keputusan signifikan atau snapshot asli yang perlu disimpan. Link arsip pada indeks lama dihapus karena file tujuannya tidak tersedia.
- Tidak ada root README duplikat. Root AGENTS hanya instruksi masuk agent; build/config/license tetap di lokasi teknis yang diperlukan tooling.
- ARCHITECTURE memuat target layout; CODEMAP memuat layout yang benar-benar ada. Jika berbeda, CODEMAP benar tentang kode dan selisihnya dicatat sebagai deviasi.

Lisensi kode yang diputuskan: `MIT OR Apache-2.0` (OQ-14). Berkas `LICENSE-MIT` dan `LICENSE-APACHE` belum dibuat.

Publikasi dokumen, pembuatan tiket, dan implementasi produk tidak termasuk perubahan dokumentasi ini.
