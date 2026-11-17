# Changelog Pippo

[Indeks](README.md) · [Current state](CURRENT-STATE.md) · [PRD](PRD.md)

Log ini membedakan perubahan spesifikasi/dokumentasi dari rilis aplikasi. Nomor PRD/arsitektur/stack adalah versi dokumen. Tanggal historis di bawah diwarisi dari changelog v0.5.2/v1.2/v1.1, bukan diverifikasi dari Git tags atau arsip: metadata Git tidak dapat dipakai dan file archive yang dulu ditautkan tidak tersedia di workspace.

## Rilis aplikasi

Belum ada rilis, tag, binary, paket atau release notes aplikasi yang dapat diverifikasi di workspace. Jangan mengubah nomor dokumen menjadi nomor produk.

Saat ada rilis nyata, tambahkan versi/tanggal/artefak serta Added/Changed/Fixed/Removed dan catatan breaking change/migration yang relevan. Jangan memasukkan rencana atau target benchmark sebagai hasil rilis.

## Review kesesuaian PRD dan peta codebase 2026-11-17

- Dokumen dicocokkan terhadap PRD 0.6.18; PRD tidak diubah. Diperbaiki: SQLite vs QSettings dan link modul Qt di SETUP, keyring wajib di FR-08, handler Join pada alur meeting, wording validasi OQ-03, opsi libpulse di TECH-STACK, pernyataan lisensi OQ-14 di indeks.
- Tech stack dicek ulang lewat Context7 (Qt 6.8, layer-shell-qt, xdg-desktop-portal, WirePlumber); catatan API ditambahkan ke TECH-STACK §3. Tidak ada build atau uji runtime.
- CODEMAP 1.0 ditambahkan: peta source aktual (modul, entry point, public type, kontrak data/IPC, build/test, deviasi) agar agent dapat memahami kode tanpa memindai seluruh repository. Masih kosong karena belum ada source. AGENTS, AGENT-GUIDE, ARCHITECTURE, FEATURES, SETUP dan indeks menunjuk ke CODEMAP.
- Versi: CURRENT-STATE/FEATURES/DECISIONS 1.19; ARCHITECTURE 1.11; TECH-STACK 1.4; SETUP 1.1; AGENT-GUIDE 1.1; CODEMAP 1.0. PRD tetap 0.6.18 draft; VALIDATION tetap 1.15.

## Keputusan interval awal dan penyelesaian OQ-32 2026-11-17 (A)

- Owner memilih interval awal form kosong dan wajib diisi, minimum 1 menit. Minimum recurrence bukan nilai default form; tidak mengisi 1/30 menit otomatis.
- OQ-32 kini DECIDED untuk semua bagian. OQ-21 dan OQ-24 sampai OQ-33 selesai diputuskan; OQ-34 tetap DEFERRED/P2 optional. Pertanyaan aktif berikutnya OQ-35 sampai OQ-39, dimulai dari evidence kebutuhan pengguna.
- PRD 0.6.18 draft; FEATURES/DECISIONS/CURRENT-STATE 1.18; VALIDATION 1.15; indeks diperbarui. Ringkasan status PRD/current state dipadatkan dengan merujuk register/kontrak canonical. Tidak ada implementasi/uji form atau scheduler.

## Keputusan akhir reminder berulang 2026-11-17 (OQ-32 bagian akhir A)

- Owner memilih default terus berulang sampai dimatikan, dengan opsi tanggal akhir atau jumlah pengulangan. Pause/stop tetap tersedia; pengulangan baru berhenti setelah batas yang dipilih tercapai.
- OQ-32 tetap PARTIALLY DECIDED hanya untuk nilai interval awal form membuat reminder. Minimum recurrence 1 menit bukan default nilai form.
- PRD 0.6.17 draft; FEATURES/DECISIONS/CURRENT-STATE 1.17; VALIDATION 1.14; indeks disinkronkan. Tidak ada implementasi/uji scheduler. Pertanyaan berikutnya nilai interval awal form pada sisa OQ-32.

## Keputusan quiet hours 2026-11-17 (OQ-32 bagian quiet hours A)

- Owner memilih cukup mengikuti DND sistem, tanpa jadwal quiet hours tambahan pada Pippo. DND tetap mengikuti capability backend; tidak mengubah setting OS atau mengklaim status yang tidak dapat dibaca.
- OQ-32 tetap PARTIALLY DECIDED: minimum/catch-up/snooze/quiet hours dipilih; end/default belum dijawab. Waktu aktif/rule reminder individual tidak diubah menjadi quiet-hours scheduler global.
- PRD 0.6.16 draft; FEATURES/DECISIONS/CURRENT-STATE 1.16; VALIDATION 1.13; indeks disinkronkan. Tidak ada implementasi/uji DND atau scheduler. Pertanyaan berikutnya akhir reminder berulang pada sisa OQ-32.

## Rekomendasi Caffeine dan server monitor diterima 2026-11-17 (OQ-33/34)

- Owner menerima rekomendasi OQ-33: Caffeine default OFF, aktivasi default 30 menit/display idle; suspend inhibition terpisah dan opt-in jika backend mendukung. Mode indefinite memerlukan status terlihat/kontrol OFF; manual lock/OS policy/critical battery tetap berlaku, tanpa threshold auto-stop baterai baru dari Q&A ini.
- Owner menerima rekomendasi OQ-34: server monitor DEFERRED/P2 optional; tunggu contoh endpoint liveness/readiness dan autentikasi dari owner. Tidak membuat scaffold/dependency/polling pada P1 atau menjadikannya blocker v1.
- OQ-32 quiet hours dan end/default tidak dijawab oleh persetujuan 33/34; tetap terbuka. PRD 0.6.15 draft; FEATURES/DECISIONS/CURRENT-STATE 1.15; ARCHITECTURE 1.10; VALIDATION 1.12; indeks disinkronkan. Tidak ada implementasi/uji inhibitor/API server.

## Keputusan snooze reminder 2026-11-17 (OQ-32 bagian snooze A)

- Owner memilih preset snooze 5, 10 dan 15 menit plus durasi custom; nilai default snooze 10 menit.
- OQ-32 tetap PARTIALLY DECIDED: minimum, catch-up dan snooze sudah dipilih; quiet hours dan end/default masih terbuka. Interval minimum recurrence 1 menit tidak diubah menjadi interval default recurrence.
- PRD 0.6.14 draft; FEATURES/DECISIONS/CURRENT-STATE 1.14; VALIDATION 1.11; indeks disinkronkan. Tidak ada implementasi atau uji snooze/scheduler. Pertanyaan berikutnya quiet hours pada sisa OQ-32.

## Keputusan reminder terlewat 2026-11-17 (OQ-32 bagian catch-up A)

- Owner memilih setelah aktif kembali tampil maksimal satu ringkasan per reminder terlewat, lalu lanjut jadwal normal; tidak mereplay semua occurrence lama. Anti-spam/DND tetap berlaku.
- OQ-32 tetap PARTIALLY DECIDED: minimum 1 menit dan catch-up sudah dipilih, snooze/quiet hours/end-default belum dijawab. Resume/restart berulang tidak boleh mengulang ringkasan yang sama.
- PRD 0.6.13 draft; FEATURES/DECISIONS/CURRENT-STATE 1.13; VALIDATION 1.10; indeks disinkronkan. Tidak ada implementasi atau uji scheduler. Pertanyaan berikutnya pengaturan snooze pada sisa OQ-32.

## Keputusan interval minimum reminder 2026-11-17 (OQ-32 bagian minimum A)

- Owner memilih interval minimum reminder berulang 1 menit. Minimum ini tidak menetapkan interval default saat membuat reminder atau kebijakan catch-up/snooze/quiet hours/end.
- OQ-32 berstatus PARTIALLY DECIDED. Kontrak FR-20, capability C-6 dan rencana validasi minimum diperbarui; policy anti-spam dan local-only tetap berlaku.
- PRD 0.6.12 draft; FEATURES/DECISIONS/CURRENT-STATE 1.12; VALIDATION 1.9; indeks disinkronkan. Scheduler belum diimplementasikan/diuji. Pertanyaan berikutnya catch-up reminder terlewat pada sisa OQ-32.

## Keputusan approval agent 2026-11-17 (Q&A OQ-31 A)

- Owner memilih notifikasi + Open Agent; review dan approval dilakukan di agent. Direct Approve/Reject dari Pippo tidak masuk scope v1, meskipun source memiliki action API.
- Kontrak FR-19, alur UX, capability C-7, boundary dan gates disinkronkan: Open Agent bukan approval dan pending hanya berubah berdasarkan konfirmasi source; event tidak boleh menjalankan aksi approval.
- OQ-31 menjadi DECIDED. PRD 0.6.11 draft; FEATURES/DECISIONS/CURRENT-STATE 1.11; ARCHITECTURE 1.9; VALIDATION 1.8; indeks diperbarui. Event/handoff Codex CLI belum diuji. Pertanyaan berikutnya OQ-32, kebijakan reminder.

## Keputusan agent pertama 2026-11-17 (Q&A OQ-30 A)

- Owner memilih Codex CLI sebagai integrasi agent pertama untuk MVP, satu adapter opt-in dahulu. Connector agent lain tetap kandidat fase lanjutan.
- OQ-30 menjadi DECIDED untuk prioritas produk. Hook/event transport, payload, versi CLI dan kemampuan completion/approval belum diverifikasi; tidak ada API/command atau mekanisme pengiriman event yang diklaim sudah tersedia.
- PRD 0.6.10 draft; FEATURES/DECISIONS/CURRENT-STATE 1.10; ARCHITECTURE 1.8; VALIDATION 1.7; indeks disinkronkan. Tidak ada implementasi/integration test. Pertanyaan berikutnya OQ-31, scope approval agent.

## Keputusan identitas penelepon 2026-11-17 (OQ-29 lanjutan A)

- Owner memilih nama/nomor caller default disembunyikan, tampil hanya setelah user mengaktifkan dan data tersedia dari adapter; tanpa data/opt-in gunakan label generik Panggilan masuk.
- OQ-29 kini DECIDED untuk kedua bagian. Direct Accept/Decline tetap conditional pada adapter teruji, selain itu Open Teams. Tidak ada metadata caller atau kompatibilitas backend yang diklaim sudah teruji.
- PRD 0.6.9 draft; FEATURES/DECISIONS/CURRENT-STATE 1.9; VALIDATION 1.6; indeks disinkronkan. Tidak ada implementasi atau uji privacy/call. Pertanyaan berikutnya OQ-30, agent pertama.

## Keputusan call actions 2026-11-17 (OQ-29 bagian actions A)

- Owner memilih Accept/Decline pada v1 hanya melalui adapter yang mendukung dan teruji; jika tidak tersedia, fallback Open Teams.
- OQ-29 berstatus PARTIALLY DECIDED: scope actions dipilih A, tampilan nama/nomor caller belum dijawab. Tidak ada klaim bahwa adapter tertentu sudah kompatibel; guardrail consent tetap berlaku.
- PRD 0.6.8 draft; FEATURES/DECISIONS/CURRENT-STATE 1.8; indeks disinkronkan. Tidak ada implementasi/uji panggilan. Pertanyaan berikutnya sisa OQ-29 tentang identitas penelepon.

## Keputusan reminder saat meeting mulai 2026-11-17 (OQ-28 lanjutan A)

- Owner memilih reminder saat meeting mulai opsional, default OFF, dapat diaktifkan user. Reminder T−5 tetap default; mematikan reminder tambahan tidak menghapus T−5.
- OQ-28 kini DECIDED untuk kedua bagian: handler browser default OS/configurable dan at-start reminder opsional/default OFF. Pilihan handler sebelumnya tetap berlaku.
- PRD 0.6.7 draft; FEATURES/DECISIONS/CURRENT-STATE 1.7; VALIDATION 1.5; indeks diperbarui. Tidak ada implementasi atau pengujian scheduler. Pertanyaan berikutnya OQ-29.

## Keputusan handler Join 2026-11-17 (OQ-28 bagian handler A)

- Owner memilih browser default OS untuk Join Meeting; user dapat mengganti aplikasi pembuka melalui pengaturan.
- OQ-28 berstatus PARTIALLY DECIDED: pilihan ini hanya menjawab handler. Reminder kedua saat meeting mulai belum dijawab; rekomendasi default OFF bukan keputusan owner.
- PRD 0.6.6 draft; FEATURES/DECISIONS/CURRENT-STATE 1.6; VALIDATION 1.4; indeks disinkronkan. Tidak ada implementasi atau uji handoff URL; pertanyaan berikutnya sisa OQ-28 tentang reminder saat mulai.

## Keputusan sumber meeting 2026-11-17 (Q&A OQ-27 A)

- Owner memilih sinkronisasi kalender Microsoft dengan izin user, ditambah input waktu/link meeting manual sebagai fallback lokal pada v1.
- OQ-27 menjadi DECIDED untuk scope sumber. Autentikasi, app registration, kebijakan/consent tenant, recurrence dan sinkronisasi tetap perlu diverifikasi; keputusan ini bukan konfirmasi izin organisasi atau integrasi sudah bekerja.
- PRD 0.6.5 draft; FEATURES/DECISIONS/CURRENT-STATE 1.5; ARCHITECTURE 1.7; VALIDATION 1.3; indeks diperbarui. Fitur tetap not-started. Pertanyaan berikutnya OQ-28 (handler Join dan reminder saat mulai).

## Keputusan shortcut Audio Mixer 2026-11-17 (Q&A OQ-26 A)

- Owner memilih shortcut awal kosong, diatur user dan diberi pemberitahuan bila konflik. Tidak ada kombinasi default atau takeover binding existing secara otomatis.
- OQ-26 menjadi DECIDED. FR-14 dan gate validasi menjelaskan binding/fallback serta keterbatasan deteksi konflik; capability backend belum diuji dan fitur tetap not-started.
- PRD 0.6.4 draft; FEATURES/DECISIONS/CURRENT-STATE 1.4; ARCHITECTURE 1.6; VALIDATION 1.2; indeks disinkronkan. Pertanyaan berikutnya OQ-27. Tidak ada implementasi produk.

## Keputusan audio mixer 2026-11-17 (Q&A OQ-25 A)

- Owner memilih volume, mute dan output untuk playback stream aplikasi yang aktif. Microphone per aplikasi dan aturan routing permanen milik Pippo ditunda; policy backend OS tetap dihormati. Kontrol microphone master conditional tidak diubah menjadi per-app capture.
- FR-14, capability C-3, boundary audio dan current state disinkronkan. Fitur tetap not-started; pilihan scope tidak membuktikan dukungan backend. OQ-26 shortcut masih terbuka.
- PRD 0.6.3 draft; FEATURES/DECISIONS/CURRENT-STATE 1.3; ARCHITECTURE 1.5; indeks diperbarui. Tidak ada perubahan source atau implementasi produk.

## Keputusan notification hub 2026-11-17 (Q&A OQ-24 A)

- Owner memilih notifikasi Pippo + sumber eksternal yang integrasinya tersedia dan diaktifkan user. Inbox notifikasi lintas aplikasi yang lengkap tidak menjadi kewajiban v1; notification daemon OS tetap berjalan normal.
- Scope OQ-24 menjadi DECIDED; capability adapter, consent, dedupe/privacy dan cakupan sumber yang diklaim tetap harus diuji. Fitur masih not-started, tidak ada implementasi atau klaim kompatibilitas baru.
- PRD 0.6.2 draft; FEATURES/DECISIONS/CURRENT-STATE 1.2; indeks disinkronkan. Gate VALIDATION yang sudah ada tetap berlaku. Pertanyaan berikutnya OQ-25.

## Keputusan FX 2026-11-17 (setelah Q&A OQ-21)

- Owner menyetujui provider FX bebas melalui endpoint custom dan mapping JSON. MoneyMatch memakai endpoint GET yang diberikan owner; ditambahkan rancangan mode angka polos untuk respons numerik berlabel text/html.
- GET biasa pada 11:26:08 WIB (04:26:08 UTC, header Date respons) menghasilkan HTTP 200, `Content-Type: text/html; charset=utf-8`, body `4442.1491`. Nilai adalah pengamatan sesaat, bukan fallback/hardcoded rate atau bukti kontrak API stabil.
- FR-12 merinci Test Endpoint, JSON field path/whole-body number, validation, presisi desimal, fetched-at terpisah dari timestamp provider, serta stale/unavailable. Kontrak/izin sumber dan rate limit tetap perlu verifikasi; belum ada implementasi produk.
- Versi dokumen: PRD 0.6.1 draft; FEATURES/DECISIONS/CURRENT-STATE/VALIDATION 1.1; ARCHITECTURE 1.4; TECH-STACK 1.3. Indeks disinkronkan. OQ-24 menjadi pertanyaan berikutnya; OQ lain belum diputuskan.

## Perubahan dokumentasi 2026-11-17

| Dokumen | Versi setelah review | Perubahan |
|---|---|---|
| PRD | 0.6.0 draft | Memisahkan kebutuhan tingkat produk dari UX/FR/teknis; menambahkan capabilities C-1 sampai C-12, quality bars Q-1 sampai Q-7, metric gaps dan rollout TBD. Scope/keputusan lama dipertahankan; belum ada persetujuan revisi. |
| FEATURES | 1.0 | Memindahkan UX, alur, FR-01 sampai FR-22 dan AC dari PRD v0.5.2; menambahkan peta fase/gate; memperjelas bahwa semuanya rencana. |
| DECISIONS | 1.0 | Memusatkan OQ-01 sampai OQ-34 beserta rationale stack/lisensi; owner dan blocker per pertanyaan; gap baru OQ-35 sampai OQ-39 dan penandaan rekomendasi agent. |
| ARCHITECTURE | 1.3 | Menyatukan target layout dan ownership, melengkapi meetings/reminders/agents/Caffeine; menghapus sisa holiday setting/provider; memisahkan durable data dari regenerable cache; memusatkan contoh build di TECH-STACK. |
| TECH-STACK | 1.2 | Menghapus klaim versi upstream latest/verified tanpa bukti tersimpan; minimum supported tetap TBD, kandidat Qt 6.8 diberi label; satu contoh layout build dan referensi Qt/QML diperiksa melalui Context7. |
| SETUP | 1.0 | Memindahkan prerequisites/perintah persiapan dari indeks; menyatakan kandidat paket belum diuji dan build belum bisa dijalankan. |
| VALIDATION | 1.0 | Memindahkan target compatibility, security, packaging/test matrix dan DoD; aturan merekam bukti serta perbedaan implemented/verified/released. |
| CURRENT-STATE | 1.0 | Snapshot workspace pra-implementasi, seluruh status FR, gap, bukti dan next steps. |
| README / AGENT-GUIDE / root AGENTS | Indeks / guide 1.0 / entry point | Rute baca docs-first, pemilik informasi, freshness dan kewajiban update dalam perubahan yang sama; link archive yang tidak ada dihapus. |

Tidak ada produk yang diimplementasikan, dependency host yang dipasang, atau dokumen/tiket yang dipublikasikan melalui perubahan ini. Keputusan owner lama tetap dibedakan dari usulan editorial review.

## Riwayat PRD sebelum restrukturisasi

| Versi | Tanggal | Perubahan |
|---|---|---|
| v0.5.2 | 17 Nov 2026 | **FR-13 dipangkas menjadi Local Calendar View** tanpa public holiday Indonesia/Malaysia, source selection, adapters/API, cache atau red holiday markers. OQ-22/23 ditutup karena dihapus dari scope. Teams meeting calendar FR-16/17 tetap terpisah. |
| v0.5.1 | 17 Nov 2026 | Perjelas FR-13 menjadi **Public Holiday Calendar ID/MY**: Indonesia default via API Hari Libur, Malaysia via PublicHolidays.com.my sebagai referensi dan sumber terstruktur hanya jika legal; kontrak adapter mengikuti field API yang tersedia, limit dua sumber, source selector, cache/provenance, red marker verified-only, OQ-23 tetap open. |
| v0.5 | 16 Nov 2026 | Tambah **FR-16-22**: Teams/PWA meeting T−5, Join, opt-in teams-for-linux D-Bus incoming call controls, AI Agent completed/approval events, repeatable reminders menit/jam, server liveness/readiness monitor **P2 optional**, dan Caffeine. Tambah OQ-27-34, safeguards, UX, scope, tests, source references. |
| v0.4 | 16 Nov 2026 | Tambah FR-12 MoneyMatch MYR→IDR, FR-13 public holiday Malaysia, FR-14 per-app Audio Mixer + shortcuts + routing, FR-15 native/PWA/terminal notification hub; OQ-21-26 dan guardrails legal/privacy/performance; rapikan inkonsistensi versi dan stack. |
| v0.3 | 13 Nov 2026 | OQ-03 DECIDED: **C++20 + Qt6/QML**; MIT OR Apache-2.0 tetap final. |
| v0.2.1 | 13 Nov 2026 | OQ-14 DECIDED: **MIT OR Apache-2.0**. |

## Riwayat arsitektur sebelum restrukturisasi

| Versi dokumen | Tanggal tercatat | Perubahan tercatat |
|---|---|---|
| 1.2 | 2026-11-17 | Sinkronisasi FR-13 ke Calendar lokal; menghapus holiday adapter/provider/fixtures. Review berikutnya menemukan sisa setting/wilayah yang diperbaiki pada 1.3. |
| 1.1 | 2026-11-16 | Konsolidasi dokumentasi di docs/, path canonical dan target tree. Tidak mengubah keputusan stack/runtime. |

Versi 1.0 pernah ditautkan oleh indeks lama, tetapi snapshot dan entri revisi aslinya tidak tersedia; tidak ada isi/tanggal historis baru yang direkonstruksi.

## Riwayat tech stack sebelum restrukturisasi

| Versi dokumen | Tanggal tercatat | Perubahan tercatat |
|---|---|---|
| 1.1 | 2026-11-17 | FR-13 Calendar lokal tanpa holiday/API; menghapus dependency holiday. |
| 1.0 | 2026-11-16 | Katalog teknologi awal, tabel snapshot versi, policy Qt/CMake, dependency mapping, contoh build serta gate license/performance/CI. Klaim snapshot terverifikasi tidak didukung rekaman di workspace; direvisi pada 1.2. |

Riwayat lebih lama hanya boleh ditambah ketika ada sumber yang dapat dirujuk. Ini bukan pernyataan semua file historis ada dalam repo.
