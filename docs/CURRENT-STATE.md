# Current State Pippo

| Metadata | Nilai |
|---|---|
| Versi dokumen | 1.20 |
| Diverifikasi | 2026-11-20, berdasarkan file yang tersedia di workspace ini |
| Tahap | Spesifikasi / pra-implementasi |
| Versi aplikasi / rilis | Belum ada artefak atau versi aplikasi yang dapat diverifikasi di workspace |
| Target produk | PRD 0.6.18 draft; OQ-21 dan OQ-24 sampai OQ-33 diputuskan; OQ-34 deferred, keputusan lama dipertahankan |

[Indeks](README.md) · [PRD](PRD.md) · [Fitur](FEATURES.md) · [Keputusan](DECISIONS.md) · [Changelog](CHANGELOG.md)

## 1. Snapshot aktual

Pada awal review workspace hanya berisi PRD, arsitektur, tech stack dan indeks docs. Review ini menata dokumentasi serta menambahkan entry point AGENTS. Tidak ada source produk yang dibaca atau diubah karena source belum tersedia.

```text
pippo/
├── AGENTS.md
└── docs/
    ├── README.md
    ├── CURRENT-STATE.md
    ├── PRD.md
    ├── FEATURES.md
    ├── DECISIONS.md
    ├── ROADMAP.md
    ├── ARCHITECTURE.md
    ├── CODEMAP.md
    ├── TECH-STACK.md
    ├── SETUP.md
    ├── VALIDATION.md
    ├── CHANGELOG.md
    └── AGENT-GUIDE.md
```

Pohon ini hanya berkas proyek yang relevan; direktori metadata lingkungan tersembunyi tidak ditampilkan. Pohon `src/` dalam ARCHITECTURE adalah rancangan masa depan. Begitu source dibuat, layout dan kontrak aktual dicatat di [CODEMAP](CODEMAP.md); pohon di atas hanya diperbarui untuk berkas tingkat atas.

| Area | Kondisi yang dapat diverifikasi |
|---|---|
| Source C++/QML dan entry point executable | Belum tersedia. |
| CMakeLists, manifest/lockfile, presets | Belum tersedia; contoh dalam TECH-STACK hanya ilustrasi. |
| Unit/integration/E2E test dan fixtures | Belum tersedia; VALIDATION adalah rencana. |
| CI, packaging RPM/DEB/Arch, release files | Belum tersedia. |
| Qt/Layer Shell/PipeWire/portal compatibility | Belum diuji untuk Pippo; tidak ada kombinasi yang boleh diklaim supported. |
| Benchmark RSS/CPU/latency/FPS/soak | Belum dijalankan; semua angka di PRD adalah target. |
| LICENSE-MIT / LICENSE-APACHE / NOTICE / CONTRIBUTING | Belum tersedia. Keputusan lisensi bukan bukti berkas sudah dibuat. |
| ADR dan archive versi lama | Tidak tersedia. Riwayat dokumen lama hanya dari tabel changelog yang diwarisi. |
| Git commit/branch/history | Repository Git dengan remote `origin` (GitHub). Branch `main`: `3ed6b8e` initial commit berisi `.gitignore` (2026-11-16) dan `842eb84` set dokumentasi pertama (2026-11-17). Belum ada tag rilis. |
| CLI / D-Bus API Pippo / database schema / config contract | Belum diimplementasikan atau distabilkan. Nama pada desain bukan API yang bisa dipanggil. |

Snapshot ini tidak menyimpulkan kondisi repository/deployment lain yang tidak tersedia dalam workspace.

## 2. Status fitur aktual

`not-started` berarti belum ada source/bukti implementasi di workspace. `backlog` berarti P2 opsional, belum dijadwalkan. “Gate terbuka” bukan status runtime: tidak ada aplikasi berjalan untuk melakukan probe sekarang.

| ID | Status aktual | Target / gate utama |
|---|---|---|
| FR-01 CPU/RAM | not-started | P0; sampler dan toleransi ukur. |
| FR-02 Battery | not-started | MVP; UPower/absent state. |
| FR-03 Now Playing | not-started | P0; MPRIS-capable player. |
| FR-04 Clipboard | not-started | MVP; backend kedua desktop + OQ-36 pinned/quota. |
| FR-05 Controls | not-started | MVP; brightness internal v1; backend/permissions. |
| FR-06 Notes | not-started | MVP; durable local autosave. |
| FR-07 Salat | not-started | v1; metode terverifikasi OQ-11. |
| FR-08 Ask AI | not-started | MVP remote BYOK; provider tests/keyring. |
| FR-09 SSO | backlog | P2 conditional OQ-15. |
| FR-10 Customization | not-started | MVP; shell/menu/preferences. |
| FR-11 Alerts/recovery | not-started | MVP; single instance, OS notifications. |
| FR-12 FX custom/MoneyMatch | not-started | v1; OQ-21 DECIDED, GET awal berhasil; implementasi, kontrak akses/rate limit belum tervalidasi. |
| FR-13 Calendar | not-started | v1; offline; tanpa public holiday. |
| FR-14 Audio | not-started | OQ-25 DECIDED A: active playback volume/mute/output; per-app mic/permanent routing ditunda. OQ-26 DECIDED A: shortcut kosong/configurable; backend dan konflik belum diuji. |
| FR-15 Notification hub | not-started | OQ-24 DECIDED A: first-party + opt-in integrasi eksternal yang tersedia; capability/cakupan belum diuji. |
| FR-16 Teams reminder | not-started | OQ-27 A: Microsoft calendar sync berizin + manual fallback; OQ-28 A: at-start opsional/default OFF. Auth/tenant/scheduler belum diuji. |
| FR-17 Join | not-started | OQ-28 handler A: browser default OS, configurable; URL handoff belum diuji. At-start FR-16 dipilih opsional/default OFF; scheduler belum diuji. |
| FR-18 Call actions | not-started | OQ-29 DECIDED A: Accept/Decline via adapter teruji atau Open Teams; caller tersembunyi default/opt-in jika tersedia. Backend/metadata belum diuji. |
| FR-19 Agent events | not-started | OQ-30 A: Codex CLI pertama; jalur event/transport/capability belum diverifikasi. OQ-31 A: notifikasi + Open Agent; tanpa direct approval v1. |
| FR-20 Reminders | not-started | OQ-32 A: minimum 1 menit, catch-up maksimal satu ringkasan per reminder lalu jadwal normal. Snooze 5/10/15 + custom default 10 menit dipilih A; quiet hours mengikuti DND sistem tanpa jadwal tambahan; akhir default terus sampai dimatikan dengan opsi tanggal/jumlah; interval awal form kosong/wajib diisi (A), scheduler/DND belum diuji. |
| FR-21 Server monitor | backlog | OQ-34 rekomendasi diterima: DEFERRED/P2 optional, tunggu contoh endpoint/auth owner; tidak menjadi gate P1. |
| FR-22 Caffeine | not-started | OQ-33 rekomendasi diterima: OFF, aktivasi default 30 menit/display idle; suspend opt-in; efektivitas inhibitor belum diuji. |

Tidak ada fitur implemented, verified, atau released yang dapat dicatat saat ini. Target C/Q dan acceptance criteria tetap berada pada PRD/FEATURES; jangan menggandakan isinya di tabel status.

## 3. Hasil review dokumentasi

Roadmap (2026-11-20): ROADMAP 1.0 ditambahkan. Fase PRD §8 dipecah menjadi milestone M0.1 sampai M2.6 dengan dependency, blocker OQ dan exit check yang merujuk PRD/VALIDATION. Urutan adalah usulan (agent's call), tanpa tanggal karena OQ-39 masih TBD. ARCHITECTURE §9 kini menunjuk ke ROADMAP agar urutan tidak tercatat dua kali.

Review kesesuaian dengan PRD 0.6.18 (2026-11-17, putaran kedua):

- PRD tidak diubah. Dokumen lain dicocokkan terhadap PRD; ditemukan dan diperbaiki enam ketidaksesuaian kecil: SETUP menyebut settings di SQLite (ARCHITECTURE: QSettings), SETUP mewajibkan link QtNetwork/QtSql/QtTest sejak P0 (TECH-STACK: link saat dipakai), FR-08 membuka opsi penyimpanan key selain keyring (PRD §4: wajib keyring), alur Join menyebut PWA/Teams-for-Linux sebagai pembuka (OQ-28 A: browser default OS), OQ-03 terbaca seolah Layer Shell sudah divalidasi, dan opsi libpulse di FR-05/14 tidak tercatat di TECH-STACK.
- Indeks belum mencantumkan pernyataan lisensi OQ-14; ditambahkan tanpa membuat berkas lisensi.
- Tech stack dicek ulang lewat Context7 (Qt 6.8, layer-shell-qt, xdg-desktop-portal, WirePlumber). Contoh CMake tetap sesuai. Temuan baru dicatat di TECH-STACK §3: `loadFromModule`, `useLayerShell()` deprecated, alur Request/Response portal, flag Inhibit, dan pemindahan stream lewat `target.object`.
- Ditambahkan CODEMAP sebagai peta source aktual untuk agent (masih kosong karena belum ada kode), beserta aturan pembaruan di AGENT-GUIDE dan AGENTS.

Yang diperbaiki pada 2026-11-17 (putaran pertama):

- PRD dipadatkan menjadi kebutuhan tingkat produk dengan goals/metrics, capabilities, quality bars, fase, risiko dan rollout. Rincian UX/FR/AC dipindahkan utuh ke FEATURES.
- Current state dipisahkan dari rancangan; changelog dokumentasi dipisahkan dari rilis aplikasi.
- Sisa rancangan holiday provider, MalaysiaCalendar dan setting wilayah yang sudah dibatalkan dihapus dari desain aktif. OQ-22/23 tetap ditutup; penyebutan historis tetap ada pada changelog/register.
- Struktur source disatukan pada ARCHITECTURE, termasuk meetings/reminders/agents/Caffeine yang belum tercakup di pohon lama. Tidak ada scaffolding produk yang dibuat.
- Contoh build dipusatkan di TECH-STACK; konflik Qt 6.5/6.8 dicatat pada OQ-37. Klaim versi upstream “latest/verified” tanpa bukti dalam workspace dihapus.
- Link arsip yang tidak mempunyai file tujuan dihapus. Reference API/provider lama diberi batas verifikasi; tidak dipresentasikan sebagai integration test.
- Owner/blocker ditambahkan pada pertanyaan terbuka; gap baru OQ-35 sampai OQ-39 dicatat tanpa mengarang baseline, deadline atau nama owner.

## 4. Gap dan batas kesiapan

Product owner masih perlu mengisi evidence/target outcome OQ-35 dan rencana owner/beta OQ-39. Engineering perlu menentukan minimum Qt/target distro dari uji OQ-37 dan protokol pengukuran OQ-38. Clipboard pinned/quota belum final (OQ-36).

Keputusan scope OQ-21 dan OQ-24 sampai OQ-33 sudah dipilih owner; rincian canonical ada di DECISIONS/FEATURES. OQ-32 selesai diputuskan, termasuk interval form awal kosong/wajib diisi, minimum 1 menit. Semua fitur tetap not-started: keputusan produk tidak membuktikan event Codex CLI, Microsoft tenant/auth, call actions, notification adapters, audio/shortcut, DND atau inhibitor Caffeine sudah bekerja.

OQ-34 DEFERRED/P2 optional, menunggu contoh endpoint/auth owner; bukan blocker P1. Tidak ada pertanyaan fitur lama yang masih OPEN/PARTIALLY DECIDED pada OQ-21 sampai OQ-34; pertanyaan aktif tersisa OQ-35 sampai OQ-39, dengan kondisi OQ-11/15/17 tetap berlaku. Rekomendasi yang belum dipilih owner tidak berubah menjadi keputusan. Spike P0 yang independen tetap dapat dipersiapkan saat implementasi diminta.

Belum ada bukti bug runtime karena aplikasi belum ada. Risiko di PRD adalah risiko desain, bukan temuan bug yang sudah direproduksi. Belum ada dokumen penggunaan runtime, endpoint IPC, skema DB, atau migration konkret untuk dirujuk agent; tambahkan ketika dipilih dan diuji.

## 5. Langkah berikutnya

Urutan milestone dan blocker keputusan ada di [ROADMAP](ROADMAP.md). Belum ada milestone yang dimulai.

1. Tinjau revisi PRD dan urutan ROADMAP; jawab OQ yang memblokir milestone terdekat (OQ-38 sebelum M0.3, OQ-36 sebelum M1.3). Catat keputusan tanpa mengubah status draft menjadi approved otomatis.
2. Saat implementasi diminta, mulai M0.1 fondasi repo lalu M0.2 shell minimal; siapkan kombinasi distro/Qt/compositor/portal yang akan diuji. Review dokumentasi ini tidak memulai implementasi.
3. Rekam bukti per FR/C/Q pada VALIDATION/CURRENT-STATE saat tersedia, termasuk revisi source dan environment; hasil spike yang mengubah desain masuk ADR/DECISIONS.
4. Sebelum beta, penuhi gates packaging, license files/audit, upgrade/recovery, privacy dan support. Jangan membuat placeholder fitur P2.

## 6. Bukti review ini

| Pemeriksaan | Hasil / batas |
|---|---|
| Inventaris berkas workspace | Dilakukan; pada awal review hanya empat dokumen. Setelah penataan, berkas proyek tercantum pada snapshot §1. |
| Konsistensi requirement | ID FR-01 sampai FR-22 dan OQ-01 sampai OQ-34 dipertahankan; OQ-35 sampai OQ-39 tambahan review. |
| Dokumentasi Qt/QML build | Diambil melalui Context7 dari dokumentasi resmi Qt untuk executable QML backing target dan policy baseline. Bukan uji compile/runtime. |
| Dokumentasi stack lain (putaran kedua) | Context7: Qt 6.8 (`/websites/doc_qt_io_qt-6_8`), layer-shell-qt (`/kde/layer-shell-qt`), xdg-desktop-portal (`/flatpak/xdg-desktop-portal`), WirePlumber (`/pipewire/wireplumber`). Hasil di TECH-STACK §3. Bukan uji compile, runtime atau compositor. |
| Link internal, anchor dan struktur Markdown | PASS (2026-11-20, setelah ROADMAP): 14 berkas Markdown dan 99 link/anchor lokal, 0 rusak; struktur tabel/fences serta kelengkapan FR/OQ/C/Q diperiksa. Tidak memvalidasi link eksternal/provider aktif atau berizin. |
| Build/test/benchmark/package | Tidak dijalankan; source dan build definition belum tersedia. |
| Endpoint FX MoneyMatch | GET pada 2026-11-17 11:26:08 WIB memberi HTTP 200, MIME text/html; charset=utf-8, body `4442.1491`; [bukti dan batas klaim](FEATURES.md#pemeriksaan-endpoint-moneymatch). Belum implementasi/integration test; Context7 tidak menemukan dokumentasi MoneyMatch. |
| Paket distro / API lainnya | Tidak diuji ulang; SETUP/FEATURES menyebut status kandidat/perlu verifikasi. |

Pembaruan berikutnya wajib mengganti tanggal dan bukti yang relevan, bukan hanya menandai dokumen “up to date”. Perubahan klaim implementasi harus menyertakan hasil verifikasi; baca AGENT-GUIDE.
