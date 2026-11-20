# Keputusan dan Pertanyaan Terbuka Pippo

| Metadata | Nilai |
|---|---|
| Versi dokumen | 1.21 |
| Diperbarui | 2026-11-20 |
| Status | Register dari PRD v0.5.2 + keputusan rekomendasi OQ-35–39 tanggal 2026-11-20 |

[Indeks](README.md) · [PRD](PRD.md) · [Fitur](FEATURES.md) · [Changelog](CHANGELOG.md)

Keputusan DECIDED pada register lama diwarisi dari catatan owner dalam PRD v0.5.2. ID tetap dan tidak didaur ulang. Keputusan baru mencatat tanggal penerimaan secara terpisah; persetujuan PRD/ROADMAP berlaku sesuai revisi yang tercatat.

**Legend:** `DECIDED` = requirement final untuk v1 sesuai scope; `CONDITIONAL` = aturan sudah diputus tetapi bergantung bukti/kapasitas; `OPEN` = belum diputus; `PARTIALLY DECIDED` = sebagian jawaban sudah diputus, sisanya tetap terbuka; `DEFERRED` = dikeluarkan dari v1 dengan syarat review fase berikutnya.

| ID | Status | Keputusan / tindak lanjut |
|---|---|---|
| **OQ-01** | **DECIDED** | Nama produk **Pippo**; binary/package ID mengikuti `pippo` dan namespace aplikasi yang tersedia/divalidasi. |
| **OQ-02** | **DECIDED** | **KDE Plasma Wayland dan Hyprland** sebagai DE target v1. DE lain bukan release blocker. |
| **OQ-03** | **DECIDED** | **C++20 + Qt6/QML** untuk core dan UI; Qt Wayland + Layer Shell Qt wajib divalidasi pada KDE/Hyprland (belum diuji) ([rationale](#oq-03-stack)). |
| **OQ-04** | **DECIDED** | Default **hide saat fullscreen**, kecuali override user. |
| **OQ-05** | **DECIDED** | **Floating top-center overlay**, tidak menyatu/memodifikasi panel sistem. |
| **OQ-06** | **DECIDED** | Collapsed hanya indikator ringkas; full mini menu bar tersedia pada expanded. |
| **OQ-07** | **DECIDED** | Clipboard **restore-to-clipboard** pada v1; auto-paste optional jika backend aman dan user consent. |
| **OQ-08** | **DECIDED** | Clipboard survive reboot; **30 hari, 500 item, 250 MiB** sebagai default limits; rule eviction wajib. |
| **OQ-09** | **DECIDED** | PNG/JPEG pada v1; GIF/SVG ditunda. |
| **OQ-10** | **DECIDED** | Kontrol brightness **internal laptop/display** dahulu; monitor eksternal optional. |
| **OQ-11** | **CONDITIONAL** | User bisa pilih metode jadwal salat; default lokal Indonesia **hanya setelah parameter dan mapping terverifikasi**; sebelum itu wajib explicit selection. |
| **OQ-12** | **DECIDED** | Silent visual reminder default; audio & lokasi otomatis opt-in. |
| **OQ-13** | **DECIDED** | **MVP AI non-local** (remote OpenAI/Anthropic + compatible BYOK); local inference bukan scope MVP. |
| **OQ-14** | **DECIDED** | Lisensi proyek **`MIT OR Apache-2.0`** telah disetujui. metadata proyek mencantumkan ekspresi SPDX tersebut; sertakan `LICENSE-MIT` dan `LICENSE-APACHE`. Audit lisensi dependency tetap merupakan release gate, bukan open question ([rationale](#oq-14-lisensi)). |
| **OQ-15** | **DEFERRED / CONDITIONAL** | Provider SSO hanya **P2** setelah uji program resmi, legal, API/SDK C++ dan provider consent. |
| **OQ-16** | **DECIDED** | Benchmark P0 pada **8 GiB RAM, iGPU, 1080p 60Hz**. |
| **OQ-17** | **CONDITIONAL** | Target **English + Bahasa Indonesia bila kapasitas memungkinkan**; i18n-ready sejak P0 wajib, English minimal. |
| **OQ-18** | **DECIDED** | Unduh **release package files** lebih dulu; managed package repository dipertimbangkan setelah beta. |
| **OQ-19** | **DECIDED** | **Local-first + per-user filesystem permissions** v1; encryption-at-rest optional P2. API keys tetap perlu keyring. |
| **OQ-20** | **DECIDED** | Fitur Notchy tambahan tidak masuk v1 tanpa persetujuan scope yang eksplisit. |

## Status pertanyaan lanjutan

OQ-03 dan OQ-14 tetap final menurut catatan PRD v0.5.2. Tabel ini mempertahankan OQ-21 sampai OQ-34; OQ-21 dan OQ-24 sampai OQ-33 telah diputuskan owner pada 2026-11-17; OQ-34 ditunda sesuai rekomendasi yang diterima owner, OQ-22/23 sudah ditutup. Tambahan gap review ada di tabel berikutnya. Seluruh rekomendasi sementara, penetapan owner per peran, dan klasifikasi blocker adalah **(agent's call)**, bukan keputusan owner. OQ-22/23 tetap keputusan scope yang sudah tercatat.

| ID | Status | Pertanyaan | Rekomendasi sementara / trade-off (agent's call) | Owner | Memblokir |
|---|---|---|---|---|---|
| **OQ-21** | **DECIDED (2026-11-17)** | Owner menyetujui FX provider bebas melalui endpoint custom + mapping JSON; untuk MoneyMatch memilih `https://transfer.moneymatch.co/utility/rate/MYR/IDR` dengan respons angka polos. | Pilihan scope/endpoint adalah keputusan owner; mode number dan validasi rinci dicatat di FR-12. GET teramati HTTP 200, MIME text/html, body `4442.1491`; bukan jaminan API stabil/izin reuse. | Scope: owner; validasi sumber: TBD engineering | Scope tidak lagi terbuka; kontrak akses/rate limit tetap gate rilis. |
| **OQ-22** | **CLOSED: REMOVED FROM SCOPE** | Pemilihan negeri/wilayah Malaysia untuk public holiday. | Dihapus dari fitur kalender atas keputusan product owner; tidak perlu dijawab/diimplementasikan. | Tidak perlu tindak lanjut | Tidak; scope dibatalkan |
| **OQ-23** | **CLOSED: REMOVED FROM SCOPE** | Sumber/API/lisensi public holiday Malaysia. | Dihapus dari fitur kalender atas keputusan product owner; tidak perlu data provider atau holiday adapter. | Tidak perlu tindak lanjut | Tidak; scope dibatalkan |
| **OQ-24** | **DECIDED (2026-11-17, pilihan A)** | V1 mencakup notifikasi milik Pippo dan sumber eksternal hanya jika integrasinya tersedia serta diaktifkan user. | Keputusan owner: first-party + opt-in adapters; tidak mensyaratkan inbox/intersepsi universal atau penggantian notification daemon. Cakupan eksternal mengikuti bukti adapter. | Scope: owner; pengujian adapter: TBD engineering | Scope tidak lagi terbuka; capability, consent, dedupe dan klaim cakupan tetap perlu diuji. |
| **OQ-25** | **DECIDED (2026-11-17, pilihan A)** | Audio mixer v1 mengatur volume, mute dan output untuk playback stream aplikasi yang aktif. Microphone per aplikasi dan aturan routing permanen ditunda. | Keputusan owner: active playback dahulu; tidak menambahkan custom routing persistence milik Pippo. Policy routing OS tetap dihormati. Microphone master conditional pada FR-14 tidak diperluas menjadi per-app capture. | Scope: owner; pengujian audio: TBD engineering | Scope tidak lagi terbuka; backend/per-stream controls tetap perlu diuji. OQ-26 shortcut sudah diputuskan (A); validasi binding tetap diperlukan. |
| **OQ-26** | **DECIDED (2026-11-17, pilihan A)** | Shortcut Audio Mixer awalnya kosong; user menentukan shortcut sendiri dan diberi tahu apabila ada konflik. | Keputusan owner: unbound by default, configurable, tidak mengambil alih binding existing. Kemampuan pendaftaran/deteksi konflik serta fallback harus diuji per desktop. | Scope: owner; pengujian shortcut: TBD engineering | Default tidak lagi terbuka; capability dan konflik pada KDE/Hyprland belum diuji. |
| **OQ-27** | **DECIDED (2026-11-17, pilihan A)** | Sinkronisasi kalender Microsoft setelah user memberi izin, ditambah input waktu/link meeting manual sebagai fallback lokal. | Keputusan owner: authorized Microsoft calendar + manual fallback pada v1; bukan manual-only atau pengambilan kalender dari cookie/token PWA. Persetujuan tenant, app registration dan jalur API tetap perlu divalidasi. | Scope: owner; pengujian kalender/tenant: TBD engineering | Sumber tidak lagi terbuka; autentikasi, izin organisasi dan sync/recurrence belum diuji. OQ-28 handler dan reminder saat mulai sudah diputuskan (A). |
| **OQ-28** | **DECIDED (2026-11-17, A untuk kedua bagian)** | Join memakai browser default OS dan bisa diganti user melalui pengaturan. Reminder tambahan saat meeting mulai opsional, default OFF; user dapat mengaktifkan. Reminder T−5 tetap default. | Kedua bagian merupakan keputusan owner. Join membuka flow, tidak otomatis mengaktifkan kamera/mikrofon atau masuk call; reminder tambahan hanya ketika diaktifkan user. | Scope: owner; pengujian meeting: TBD engineering | Default handler dan at-start alert sudah diputus; URL handoff dan scheduler belum diuji. |
| **OQ-29** | **DECIDED (2026-11-17, A untuk kedua bagian)** | Accept/Decline v1 hanya melalui adapter yang mendukung dan teruji, selain itu Open Teams. Nama/nomor caller default disembunyikan, tampil setelah user mengaktifkan dan hanya jika datanya tersedia dari adapter. | Kedua bagian merupakan keputusan owner. Tanpa opt-in atau tanpa metadata adapter, gunakan label generik Panggilan masuk; jangan mengarang identitas. teams-for-linux tetap kandidat yang perlu diuji, bukan klaim kompatibilitas universal/PWA. | Scope: owner; pengujian adapter/privacy: TBD engineering | Scope actions dan identitas sudah diputus; capability dan metadata caller belum diuji. |
| **OQ-30** | **DECIDED (2026-11-17, pilihan A)** | Owner memilih Codex CLI sebagai integrasi agent pertama untuk MVP. | Prioritas produk adalah keputusan owner; satu adapter opt-in untuk Codex CLI dahulu. Event transport, payload, completion/approval capability dan versi CLI harus diverifikasi dari dokumentasi resmi sebelum menentukan hook/IPC; belum ada protokol/command final. | Prioritas: owner; verifikasi event/adapter: TBD engineering | Agent pertama tidak lagi terbuka; jalur event dan kelayakan integrasi belum diuji. OQ-31 A: notifikasi + Open Agent, tanpa direct approval v1. |
| **OQ-31** | **DECIDED (2026-11-17, pilihan A)** | Pippo memberi notifikasi dan Open Agent; review serta approval tetap dilakukan di agent. | Keputusan owner: tidak ada tombol/request Approve/Reject langsung dari Pippo pada v1. Membuka agent bukan approval, pending tidak diubah menjadi approved secara lokal. Evaluasi direct approval memerlukan scope terpisah. | Scope: owner; validasi event/handoff: TBD engineering | Scope approval tidak lagi terbuka; notifikasi/handoff Codex CLI belum diverifikasi. |
| **OQ-32** | **DECIDED (2026-11-17, A untuk seluruh bagian)** | Minimum 1 menit; catch-up satu ringkasan/reminder lalu jadwal normal; snooze 5/10/15 + custom default 10; quiet hours mengikuti DND sistem; akhir default terus sampai dimatikan dengan opsi tanggal/jumlah; interval awal form kosong dan wajib diisi user. | Seluruh bagian tersebut keputusan owner. Minimum 1 menit bukan nilai default; form tidak menyimpan reminder interval kosong. Local-only dan anti-spam tetap berlaku, sinkronisasi kalender umum tidak ditambahkan. DND mengikuti capability desktop. | Scope: owner; validasi form/scheduler/DND: TBD engineering | Tidak ada keputusan produk tersisa pada OQ-32; form, scheduler dan DND backend belum diuji. |
| **OQ-33** | **DECIDED (2026-11-17, rekomendasi diterima owner)** | Caffeine default OFF; saat diaktifkan durasi default 30 menit dan hanya menahan display idle. Suspend inhibition terpisah, hanya jika user mengaktifkan secara eksplisit dan backend mendukung. | Manual lock, kebijakan OS/admin dan critical battery tetap berlaku. Mode manual/indefinite memerlukan status aktif yang terlihat dan kontrol OFF. Tidak menetapkan threshold auto-stop baterai baru dari Q&A ini. | Scope: owner; pengujian inhibitor: TBD engineering | Default tidak lagi terbuka; efektivitas backend, expiry/off dan manual lock belum diuji. |
| **OQ-34** | **DEFERRED / P2 OPTIONAL (2026-11-17, rekomendasi diterima owner)** | Server/VM/bare-metal monitor tetap di luar MVP/v1; tunggu contoh endpoint liveness/readiness dan autentikasi dari owner sebelum membahas detail integrasi. | Tidak membuat generic monitor, scaffold/interface, dependency atau polling pada P1. Skema, interval, grouping dan alert baru dievaluasi bersama kontrak API pada P2; bukan blocker v1. | Kontrak API: owner ketika P2 dimulai; engineering belum ditugaskan | Hanya pekerjaan P2 setelah kontrak tersedia; tidak memblokir P0/P1. |

## Gap hasil review dan tindak lanjut owner 2026-11-20

Product owner menerima rekomendasi berikut pada 2026-11-20. OQ-36 diputuskan; OQ-35, OQ-37 dan OQ-38 sebagian diputuskan dengan bukti atau parameter tersisa; OQ-39 tetap terbuka. Penetapan owner berdasarkan peran merupakan **(agent's call)**; nama penanggung jawab masih TBD. Pertanyaan nonblocking tidak menahan spike P0 yang independen.

| ID | Status | Pertanyaan | Owner | Memblokir | Keputusan / tindak lanjut |
|---|---|---|---|---|---|
| OQ-35 | **PARTIALLY DECIDED (2026-11-20)** | Apa bukti masalah pengguna, ukuran baseline perpindahan konteks, dan target keberhasilan tugas mikro? Mengapa sekarang? | TBD (product owner) | Target outcome sebelum beta; bukan blocker spike teknis | **Diputuskan:** ukur keberhasilan dan waktu penyelesaian untuk tugas mikro yang disepakati sebelum beta; tetapkan target setelah baseline ada. Bukti wawancara/observasi atau data serta target numerik masih TBD. |
| OQ-36 | **DECIDED (2026-11-20)** | Bagaimana clipboard mengatasi quota/retention ketika seluruh item pinned atau pinned melewati 30 hari? | Product owner (scope); engineering (implementasi) | Tidak lagi memblokir keputusan M1.3; perilaku harus dipenuhi dan diuji | Pinned item tidak dihapus karena umur, tetapi tetap dihitung terhadap quota; setelah di-unpin, item yang melewati 30 hari eligible untuk dihapus. Saat item baru membuat batas terlampaui, evict item unpinned yang sudah ada, tertua dulu, sampai item baru muat. Jika tidak ada item lama yang dapat dievict, tolak item baru dengan feedback agar user menghapus/unpin item. Jangan hapus pinned diam-diam. |
| OQ-37 | **PARTIALLY DECIDED (2026-11-20)** | Berapa minimum Qt yang benar-benar didukung dan kombinasi versi distro/compositor/portal yang menjadi target paket? | TBD (engineering + release owner) | Minimum final dan klaim compatibility sebelum M2.6; kandidat cukup untuk mulai M0.1 | Target uji mencakup KDE Plasma Wayland dan Hyprland; Qt 6.8 menjadi kandidat untuk M0.1. Minimum final dan versi distro/compositor/portal baru ditetapkan setelah build serta uji paket; catat matriks hasil. |
| OQ-38 | **PARTIALLY DECIDED (2026-11-20)** | Berapa warm-up, jumlah sampel latency, ambang RSS growth soak, toleransi sampling CPU/RAM, dan periode/denominator crash-free? | TBD (engineering / QA) | Sahkan parameter tersisa sebelum M0.3 dan klaim performance/reliability | Protokol awal: warm-up 5 menit; 100 sampel per interaksi untuk p50/p95; pengukuran idle 10 menit; soak 24 jam. Engineering/QA masih harus menetapkan sampling CPU/RAM, ambang RSS growth, crash-free denominator/periode dan lingkungan reproducible. Telemetry tidak wajib. |
| OQ-39 | **OPEN** | Siapa owner produk/engineering/release, kapasitas/budget, peserta beta, tanggal fase, kanal support, dan prosedur rollback paket/data? | TBD (product owner + release owner) | Jadwal komitmen dan kesiapan beta publik | Tetapkan owner per peran, kapasitas, rencana dukungan dan prosedur upgrade/backup/rollback sebelum menyusun tanggal fase atau mengundang peserta beta. Nama, kapasitas, tanggal dan peserta tetap TBD. |

## Asumsi editorial revisi

- Pengelompokan capability C-1 sampai C-12 dan pemetaan Must/Could adalah **(agent's call)** berdasarkan fase P0/P1/P2 lama; tidak menambah fitur. Scope P1 completion diberi label “v1 completion” agar tidak tercampur dengan MVP usable.
- Struktur dokumen, label status, dan format bukti adalah **(agent's call)** untuk handoff; bukan persetujuan implementasi.
- Rincian parsing FX GET/JSON mapping/angka polos merupakan **(agent's call)** untuk memenuhi pilihan endpoint owner; source contract/rate limit masih perlu verifikasi.
- Target angka yang sudah ada tetap diwarisi dari v0.5.2 sebagai target desain, bukan baseline terukur atau klaim tercapai.

## OQ-03 Stack

**Pros:** QML memudahkan animasi state dan motion; Qt Quick scene graph mendukung GPU rendering; integrasi KDE, D-Bus, dan C++/QML matang; tidak perlu FFI Rust-Qt.

**Cons:** kompleksitas memory safety C++; Qt runtime/QML plugins menambah kebutuhan packaging; konsumsi RAM perlu diukur; Layer Shell tidak otomatis memberi kompatibilitas GNOME Wayland.

**Trade-off:** UX/motion dan kemudahan implementasi diprioritaskan dengan biaya dependensi dan audit lisensi lebih besar. Qt Wayland + Layer Shell Qt wajib diuji pada kedua DE target, bukan diasumsikan bekerja identik.

**P0 benchmark:** 8 GiB RAM, iGPU, 1080p/60Hz; ukur RAM idle, CPU idle, frame time, input latency, startup, fullscreen behavior, multi-monitor, dan fokus input. Keputusan stack tidak dibuka kembali tanpa persetujuan owner.

## OQ-14 Lisensi

**Keputusan final owner:** Pippo menggunakan **`MIT OR Apache-2.0`** (dual licensing dengan operator SPDX **OR**). Penerima lisensi dapat memilih **salah satu** dari MIT atau Apache-2.0 untuk menggunakan kode Pippo, bukan wajib menerapkan kedua lisensi sekaligus. Keduanya permisif: penggunaan komersial dan distribusi proprietary diizinkan sepanjang kewajiban lisensi yang dipilih dipenuhi. Apache-2.0 secara eksplisit mengatur grant paten dan pemberitahuan tertentu; MIT lebih ringkas.

**Implementasi repository (wajib sebelum rilis sumber):**

- Cantumkan SPDX `MIT OR Apache-2.0` di metadata proyek/paket, README, dan header source yang relevan.
- Sertakan teks lisensi resmi dalam `LICENSE-MIT` dan `LICENSE-APACHE`; README mencantumkan pernyataan dual-license.
- Dokumentasikan aturan kontribusi dan pernyataan lisensi kontribusi di `docs/CONTRIBUTING.md`; pastikan aset seperti logo, font, dan artwork memiliki lisensi/izin sendiri bila berbeda.
- Dokumentasikan dan sertakan atribusi/NOTICE pihak ketiga sebagaimana diwajibkan dependency atau aset yang digunakan; lakukan audit SPDX dan dependency tree sebelum publish paket RPM/DEB/Arch.

**Catatan compliance:** Keputusan lisensi Pippo sudah final; **audit lisensi dependency adalah release gate, bukan alasan untuk membuka kembali OQ-14**. Jika toolkit atau dependency ternyata inkompatibel, revisi pilihan dependency atau eskalasi perubahan scope/lisensi secara eksplisit. Audit lisensi Qt6, QML plugins, dan Layer Shell Qt secara spesifik. MIT OR Apache-2.0 **tidak** mewajibkan fork/modifikasi yang didistribusikan tetap open source. Pemenuhan kewajiban distribusi tetap perlu audit dependency dan aset yang benar-benar dipakai.
