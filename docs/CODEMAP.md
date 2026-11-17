# Peta Codebase Pippo

| Metadata | Nilai |
|---|---|
| Versi dokumen | 1.0 |
| Diverifikasi | 2026-11-17 terhadap isi workspace |
| Status | Belum ada source; peta masih kosong dan siap diisi bersamaan dengan kode pertama |

[Indeks](README.md) · [Current state](CURRENT-STATE.md) · [Arsitektur](ARCHITECTURE.md) · [Panduan agent](AGENT-GUIDE.md)

Dokumen ini memetakan source yang **benar-benar ada**: modul, path, entry point, public type, kontrak data/IPC, perintah build/test, dan test pemiliknya. Tujuannya supaya agent cukup membaca peta ini, lalu membuka hanya file yang relevan, tanpa memindai seluruh repository.

Pembagian dengan dokumen lain:

- ARCHITECTURE memuat **target** layout dan boundary. CODEMAP memuat **kondisi aktual**. Kalau keduanya berbeda, CODEMAP yang benar tentang kode; selisihnya dicatat di §6 sebagai deviasi.
- FEATURES memuat perilaku. CODEMAP hanya menyebut ID FR yang diimplementasikan modul, tanpa menyalin acceptance criteria.
- CURRENT-STATE memuat status dan bukti. CODEMAP tidak menyatakan fitur selesai; kolom status hanya merujuk ke CURRENT-STATE.

## 1. Cara agent memakai peta ini

1. Cari modul lewat ID FR/C atau nama fitur di §2.
2. Baca kartu modulnya di §3 (entry point, public type, dependency, test).
3. Buka hanya file yang disebut kartu, ditambah test pemiliknya. Lebarkan pencarian hanya kalau kartu tidak menjawab, lalu perbaiki kartunya pada perubahan yang sama.
4. Kalau path di kartu tidak ditemukan, peta sudah basi. Laporkan dan perbarui; jangan mengandalkan isi kartu itu.

Pertanyaan status, scope, atau keputusan tidak memerlukan source: gunakan CURRENT-STATE, PRD dan DECISIONS.

## 2. Indeks modul

**Saat ini belum ada modul.** Workspace hanya berisi `AGENTS.md` dan `docs/`. Satu baris per modul ditambahkan pada perubahan yang membuat folder/target pertamanya.

| Modul | Path | FR / C | Tanggung jawab singkat | Kartu |
|---|---|---|---|---|
| — | — | — | Belum ada source | — |

Nama modul mengikuti folder fitur pada [ARCHITECTURE §4](ARCHITECTURE.md#4-target-layout-source) (`shell`, `system`, `media`, `audio`, dan seterusnya), `app`, `platform/<adapter>`, serta `Pippo/App` untuk QML.

## 3. Kartu modul

Belum ada kartu. Salin template berikut ketika modul pertama dibuat. Template ini berada di dalam code block agar tidak terbaca sebagai modul nyata.

```markdown
### <modul> — `src/features/<modul>/`

- **FR / C:** FR-xx, C-x
- **Tanggung jawab:** satu atau dua kalimat. Apa yang TIDAK dikerjakan modul ini.
- **Entry point:** tempat modul dibuat dan di-wire (mis. `src/app/AppBootstrap.cpp`, fungsi/baris pemasangan).
- **Public type:** `TypeName` (`path/TypeName.h`) — peran singkat. Hanya type yang dipakai di luar modul.
- **Diekspos ke QML:** nama property/signal/invokable atau model, beserta file QML pemakainya.
- **Bergantung pada:** adapter/port lain (path), library eksternal.
- **Dipakai oleh:** modul lain yang terhubung lewat bootstrap/signal.
- **Data & kontrak:** tabel SQLite, key QSettings, secret keyring, D-Bus name, file XDG. Rujuk §4.
- **Thread:** GUI thread / worker / thread milik library.
- **Test:** path test unit/integration dan perintah ctest label/filter.
- **Batasan aktif:** capability yang belum didukung, workaround, TODO yang memengaruhi perilaku.
- **Status:** lihat CURRENT-STATE (FR-xx). Jangan mengisi status di sini.
```

Kartu cukup pendek untuk dibaca sekilas (sekitar 10–20 baris). Rincian algoritme tetap berada di kode dan komentar seperlunya, bukan di kartu.

## 4. Kontrak yang terlihat dari luar modul

Kontrak ini sering menjadi alasan agent membaca banyak file. Catat begitu kontrak dibuat atau berubah.

| Kontrak | Lokasi definisi | Status |
|---|---|---|
| Entry point executable dan alur startup | — | Belum ada |
| QML module URI dan root type | — | Belum ada. Rencana: URI `Pippo.App`, dimuat dengan `QQmlApplicationEngine::loadFromModule` (lihat TECH-STACK §3). |
| Skema SQLite dan migration | — | Belum ada |
| Key QSettings / preferences | — | Belum ada |
| Item keyring (service/attribute) | — | Belum ada |
| D-Bus name/interface milik Pippo, CLI flag, single-instance channel | — | Belum ada |
| Environment variable yang dibaca | — | Belum ada |
| Path data/cache XDG aktual | — | Belum ada |

Untuk setiap kontrak tulis path file definisinya dan versi skema; isi lengkap skema tetap berada di source atau migration, tidak disalin ke sini.

## 5. Build, target, dan test aktual

| Item | Nilai aktual |
|---|---|
| Target CMake | Belum ada. Rencana penamaan: executable `pippo`, library `pippo_*` (ARCHITECTURE §4). |
| Configure / build | Belum dapat dijalankan; tidak ada `CMakeLists.txt`. Kandidat perintah ada di SETUP §5. |
| Test | Belum ada. Catat label/filter `ctest` per modul ketika test pertama dibuat. |
| Lint/format | Belum ada konfigurasi. |

Isi tabel ini hanya dengan perintah yang sudah pernah dijalankan berhasil, beserta tanggalnya.

## 6. Deviasi dari ARCHITECTURE

Belum ada. Kalau implementasi menyimpang dari target layout atau boundary (misalnya folder digabung, adapter dipindah), tulis satu baris: apa, kenapa, dan apakah ARCHITECTURE perlu diperbarui atau ADR dibuat.

## 7. Menjaga peta tetap akurat

Perbarui CODEMAP pada perubahan yang sama ketika:

- folder fitur/adapter, target CMake, atau file QML panel ditambah, dipindah, diganti nama atau dihapus;
- public type, entry point, wiring di bootstrap, atau dependency antarmodul berubah;
- kontrak di §4 dibuat atau berubah (tabel/migration, key settings, D-Bus/CLI, env, path XDG);
- perintah build/test atau lokasi test berubah.

Tidak perlu memperbarui peta untuk perubahan isi function yang tidak mengubah hal di atas.

Pemeriksaan cepat sebelum menutup pekerjaan, setelah `src/` ada (perintah ini belum pernah dijalankan karena belum ada source):

```bash
# Folder fitur/adapter yang belum disebut di CODEMAP
for d in src/features/*/ src/platform/*/; do
  [ -d "$d" ] && ! grep -qF "${d%/}" docs/CODEMAP.md && echo "Belum dipetakan: ${d%/}"
done
```

Kalau dokumen ini melewati sekitar 400 baris, pindahkan kartu ke `docs/codemap/<modul>.md` dan biarkan indeks §2 tetap di sini. Jangan membuat folder itu sebelum dibutuhkan.
