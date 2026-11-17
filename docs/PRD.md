---
title: Pippo
status: draft
size: full
version: 0.6.18
date: 2026-11-17
owner: TBD
---

# PRD Pippo

Dynamic Island / Notch untuk Linux.

[Indeks](README.md) · [Current state](CURRENT-STATE.md) · [Fitur](FEATURES.md) · [Keputusan](DECISIONS.md) · [Changelog](CHANGELOG.md)

PRD ini memisahkan kebutuhan tingkat produk dari kontrak fitur, rancangan teknis dan status implementasi. Revisi 0.6.18 mempertahankan keputusan lama serta keputusan Q&A: OQ-21 endpoint FX custom/JSON/angka polos, OQ-24 notifikasi Pippo + opt-in integrasi eksternal tersedia, OQ-25 kontrol playback stream aktif tanpa microphone per aplikasi atau aturan routing permanen v1, OQ-26 shortcut Audio Mixer kosong/configurable dengan pemberitahuan konflik, serta OQ-27 sinkronisasi kalender Microsoft berizin + input waktu/link manual sebagai fallback.

Untuk OQ-28, handler Join dipilih browser default OS yang dapat diganti user; reminder tambahan saat mulai opsional dan default OFF, dapat diaktifkan user. Status keseluruhan tetap draft; persetujuan keputusan satu fitur tidak berarti persetujuan seluruh PRD. Semua fitur masih berupa rencana di workspace ini. Riwayat dan rationale keputusan ada di CHANGELOG dan DECISIONS.

## 1. Ringkasan dan visi

Pippo membantu pengguna Linux melihat informasi penting dan menyelesaikan tugas mikro tanpa terus berpindah aplikasi. Produk berupa floating island kecil di bagian atas layar yang dapat memperlihatkan informasi ringkas, panel interaktif, dan pengingat sesuai konteks.

Target v1 adalah desktop Linux x86_64 dengan KDE Plasma Wayland dan Hyprland. Pippo menggabungkan informasi sistem/media, clipboard, audio/perangkat, notes, AI remote pilihan pengguna, salat, kalender lokal, notifikasi, meeting, reminder, dan keep-awake. Fungsi inti bekerja lokal; koneksi eksternal hanya untuk fitur yang membutuhkannya dan sesuai persetujuan pengguna. [Notchy](https://notchy.dev/id/) adalah referensi pola interaksi, bukan daftar fitur yang otomatis harus disalin.

## 2. Masalah, pengguna, dan bukti

Informasi sistem, kontrol musik/perangkat, clipboard, catatan, dan pengingat tersebar di panel, tray, serta aplikasi terpisah. Pengguna perlu berpindah jendela untuk tugas singkat. Aplikasi pendamping yang boros resource juga bertentangan dengan kebutuhan desktop yang responsif.

| Pengguna | Pekerjaan yang ingin diselesaikan |
|---|---|
| Developer / power user Linux | Memeriksa status, clipboard, catatan, dan aktivitas agent tanpa kehilangan konteks kerja. |
| Pengguna laptop / media aktif | Mengendalikan playback, audio, baterai, konektivitas, dan layar dengan cepat. |
| Pengguna pengingat salat / produktivitas | Mendapat pengingat yang tepat, dapat dibisukan, dan tidak menumpuk. |
| Pengguna meeting Teams / kebutuhan MYR→IDR | Mengetahui jadwal, membuka meeting, dan memeriksa informasi kurs dari sumber yang jelas. |

Persona di atas merangkum kebutuhan yang sudah ada di v0.5.2, bukan hasil riset baru. Bukti wawancara/observasi, frekuensi masalah, baseline waktu atau perpindahan konteks, alasan urgensi, serta biaya menunda masih **TBD**; product owner menindaklanjuti OQ-35. Workaround saat ini adalah memakai aplikasi/panel terpisah menurut problem statement lama; belum ada observasi terukur.

## 3. Goals dan ukuran keberhasilan

Target teknis di bawah diwarisi dari v0.5.2. Belum ada hasil ukur. Tanggal fase, owner bernama, serta metrik outcome produk tidak dibuat-buat.

| ID | Goal | Metrik | Baseline | Target | Evaluasi / owner |
|---|---|---|---|---|---|
| G-1 | Mengurangi gangguan tugas mikro | Keberhasilan tugas, waktu penyelesaian, perpindahan konteks | TBD | TBD (OQ-35) | Beta; TBD product owner |
| G-2 | UI responsif dengan resource rendah | CPU, RSS, latency, frame rate | Belum diukur | Q-1 sampai Q-3 | P0 dan sebelum rilis; TBD engineering |
| G-3 | Memberikan dukungan desktop yang jujur | Gate fitur inti pada kedua desktop target; alasan untuk capability unavailable | Belum diuji | Semua gate scope P0/P1 lulus atau exception disetujui owner | v1; TBD QA/release owner |
| G-4 | Menjaga kontrol data pengguna | Pengiriman AI tanpa submit, secret di log, capture tanpa consent | Belum diuji | 0 pada skenario validasi Q-5 | MVP dan v1; TBD engineering |
| G-5 | Dapat dipasang dan dihapus melalui paket distro | Install/upgrade/uninstall pada kombinasi yang diklaim | Belum ada paket | Semua kombinasi rilis yang diklaim lulus smoke test | v1; TBD release owner |
| G-6 | Stabil dipakai seharian | Soak dan crash-free sessions | Belum diukur | Soak 24 jam tanpa growth berkelanjutan; ≥99,5% crash-free sessions | P0/beta; protokol OQ-38 |

Guardrails: tidak mengubah panel sistem, tidak mencuri input pengguna, tidak mengambil alih notification daemon, tidak mengirim konteks ke AI otomatis, dan tidak melakukan auto-approval agent.

## 4. Scope dan non-goals

Scope P0/P1 mencakup capabilities C-1 sampai C-11 dengan batas dan gate yang tercantum. Integrasi eksternal bersifat capability-based: ketidaktersediaan harus terlihat jelas dan mempunyai fallback, bukan keberhasilan palsu.

Di luar scope v1:

- Mengganti/memodifikasi panel sistem; parity semua DE/WM, GNOME Wayland atau X11, ARM64/32-bit, Flatpak/Snap.
- OCR, file shelf, timer tambahan, lyrics, window snapping, hardware HUD, atau fitur referensi UX lain tanpa persetujuan scope.
- Local inference pada MVP; login consumer dengan cookie/session token; provider SSO sebelum evaluasi resmi P2.
- Cloud sync, enkripsi data lokal at rest sebagai kewajiban v1, atau managed package repositories pada beta awal. Secret tetap wajib di keyring.
- Transaksi/transfer uang, settlement rate terjamin, atau akses MoneyMatch dengan scraping/private endpoint/token aplikasi lain.
- Public holiday Indonesia/Malaysia/negara lain, penanda tanggal merah holiday, API/feed/ICS holiday, atau sinkronisasi kalender umum. Calendar lokal terpisah dari integrasi meeting Teams.
- Intersepsi notifikasi universal, takeover daemon, volume per tab PWA tanpa stream terpisah, atau media yang tidak mengekspos antarmuka kontrol yang didukung.
- Pengambilan kalender/token Teams dari browser, accept-call universal, atau penerimaan call otomatis. Direct call action membutuhkan adapter yang terbukti mendukungnya.
- Eksekusi/approval agent otomatis atau direct Approve/Reject dari Pippo pada v1 (OQ-31 A); remote server monitor sebagai syarat MVP/v1.
- Spoofing mouse/keyboard/presence Teams, bypass manual lock atau kebijakan OS melalui Caffeine.

Produk dianggap gagal walau tampilannya bekerja jika mengganggu pengetikan, kehilangan/menghapus data diam-diam, membocorkan data, memutus notifikasi OS, atau menyatakan integrasi supported tanpa bukti.

## 5. Perjalanan pengguna utama

1. Pengguna melihat status/media ringkas, membuka informasi lebih lanjut, melakukan kontrol yang tersedia, lalu kembali bekerja tanpa fokus direbut event baru.
2. Pengguna memulihkan clipboard atau mengedit notes yang tetap tersedia setelah restart; konteks hanya dibagikan ke AI ketika dipilih dan disubmit secara eksplisit.
3. Pengguna mengubah volume/output stream aplikasi tertentu tanpa mengubah stream aplikasi lain, dengan hasil yang mencerminkan keadaan OS.
4. Pengguna membuat pengingat/salat/meeting, menerima reminder sesuai waktu lokal, lalu dismiss/snooze atau membuka meeting; resume tidak memicu banjir pengingat lama.
5. Pengguna mengaktifkan sumber agent atau notifikasi yang dipercaya, melihat hasil/permintaan tindakan, lalu membuka agent untuk review/approval di agent tersebut (OQ-31 A). Pippo tidak mengirim Approve/Reject; pending approval tetap pending sampai sumber mengonfirmasi.
6. Pengguna melihat kalender bulanan secara offline, memeriksa kurs dengan provenance atau status unavailable, dan mengaktifkan keep-awake dengan status yang jelas.

Rincian interaksi, state shell, serta acceptance criteria tetap ada dalam [FEATURES](FEATURES.md).

## 6. Capabilities dan prioritas

ID C berikut adalah pengelompokan editorial baru **(agent's call)**, diturunkan dari FR/fase lama. Must berarti wajib pada fase yang disebut; seluruh Must dengan fase MVP membentuk MVP usable. P0 adalah validasi sebelum MVP. Conditional berarti fungsi fallback tetap wajib sementara integrasi penuh menunggu gate. Tidak ada prioritas fitur baru yang diputuskan lewat revisi ini.

| ID | Capability / hasil bagi pengguna | Prioritas | Fase | Kriteria keberhasilan | Rincian |
|---|---|---|---|---|---|
| C-1 | Mengakses status/media dari island tanpa mengganggu desktop | Must | P0 → MVP | Interaksi shell dan kontrol player yang tersedia bekerja pada kedua desktop; fullscreen hide default. | UX shell, FR-01/02/03/10 |
| C-2 | Memulihkan clipboard text/gambar dan mengelola notes lokal | Must | MVP | Konten bertahan setelah restart, dapat dicari/dihapus, quota/retention ditegakkan tanpa kehilangan diam-diam. | FR-04/06; OQ-36 |
| C-3 | Mengatur audio/perangkat secara independen | Must | MVP; brightness internal v1 | Playback stream aktif A dapat diubah volume/mute/output tanpa mengubah B; kontrol OS mencerminkan hasil nyata atau alasan unavailable. Per-app mic dan aturan routing permanen ditunda. | FR-05/14; OQ-25/26 DECIDED A |
| C-4 | Menggunakan AI remote pilihan sendiri | Must | MVP | OpenAI, Anthropic, dan custom-compatible endpoint teruji dengan submit eksplisit, stop, dan keyring. | FR-08 |
| C-5 | Menerima notifikasi dan menjalankan aplikasi dengan aman | Must | MVP → v1 adapters | Alert Pippo/recovery bekerja; sumber eksternal hanya melalui integrasi tersedia yang diaktifkan user, dengan cakupan teruji tanpa duplicate toast atau daemon takeover. | FR-11/15; OQ-24 DECIDED A |
| C-6 | Membuat reminder berulang dan mengendalikan keep-awake | Must | MVP | Reminder berulang memiliki interval minimum 1 menit, dapat snooze/pause, dan setelah aktif kembali memberi maksimal satu ringkasan per reminder terlewat lalu lanjut jadwal normal; Caffeine default OFF, aktivasi default 30 menit/display idle, suspend opt-in; off/expiry memulihkan idle policy yang didukung. | FR-20/22; OQ-32/33 DECIDED; kontrak rinci FR-20/22 |
| C-7 | Mengetahui hasil dan permintaan tindakan agent | Must | MVP basic | Codex CLI menjadi target adapter opt-in pertama untuk completion/approval events, setelah jalur event terverifikasi; notifikasi + Open Agent, review/approval di agent tanpa direct Approve/Reject dari Pippo. | FR-19; OQ-30/31 DECIDED A |
| C-8 | Melihat jadwal salat dan kalender lokal | Must | v1 completion | Salat offline tervalidasi; kalender dapat berpindah bulan/Today secara offline tanpa holiday provider. | FR-07/13; OQ-11 |
| C-9 | Memeriksa MYR→IDR melalui endpoint custom, termasuk MoneyMatch | Must, data conditional | v1 completion | Endpoint dan mapping JSON/angka polos dapat dikonfigurasi dan diuji; sumber serta waktu pengambilan jelas, fallback unavailable tanpa kurs palsu. | FR-12; OQ-21 DECIDED |
| C-10 | Mengikuti meeting dan merespons panggilan yang didukung | Must, call conditional | v1 completion | Kalender Microsoft berizin dapat disinkronkan dan input waktu/link manual tersedia sebagai fallback; reminder T−5 dan Join melalui browser default OS atau pilihan user bekerja benar. Call action hanya pada adapter tervalidasi. | FR-16/17/18; OQ-27 DECIDED A; OQ-28 DECIDED A; OQ-29 DECIDED A |
| C-11 | Menggunakan paket native yang dapat diakses | Must | v1 completion | Paket target teruji; keyboard, reduced motion, multi-monitor dan resource i18n tervalidasi. | Q-6/7; VALIDATION |
| C-12 | Memperluas integrasi setelah v1 | Could | P2 optional | Proposal terpisah dengan sumber/API, biaya resource, izin dan persetujuan scope sebelum dibangun. | FR-09/21; OQ-15/34 DEFERRED |

P2 juga mencakup kandidat local LLM, GNOME/X11, external brightness, GIF/SVG, managed repos, deeper notification history, connector agent tambahan, PWA call control bila API tersedia, serta FX trend/alert bila data berizin. Daftar kandidat bukan komitmen rilis.

## 7. Quality bars

Baseline hardware yang sudah dicatat pada OQ-16: x86_64, 8 GiB RAM, iGPU, 1920×1080 @ 60 Hz. Ukur KDE Plasma Wayland dan Hyprland terpisah; catat CPU/kernel/Qt/compositor, gunakan Release build untuk performance. Semua Q di bawah adalah Must pada fase terkait; bukti saat ini belum ada.

| ID | Ekspektasi | Kondisi / verifikasi |
|---|---|---|
| Q-1 | Idle CPU rata-rata ≤0,5% satu logical CPU; idle RSS ≤90 MiB | 10 menit setelah warm-up, collapsed, satu monitor, indeks 500 clipboard text, tanpa request AI/animasi. Warm-up OQ-38. |
| Q-2 | Active RSS ≤160 MiB | Expanded dengan artwork dan 20 thumbnail terlihat; gambar besar lazy decode. |
| Q-3 | Click-to-expanded p95 ≤150 ms; hover-to-peek p95 ≤120 ms termasuk debounce; target 60 FPS | Hardware baseline, setelah warm-up; tanpa blocking I/O di UI. Jumlah sampel OQ-38. |
| Q-4 | Soak 24 jam tanpa RSS growth berkelanjutan; crash-free sessions ≥99,5% | Workload reproducible; beta lewat laporan atau telemetry opt-in. Ambang/denominator/periode OQ-38. |
| Q-5 | Local-first dan aksi sensitif eksplisit | Tidak ada prompt/clipboard/note dikirim ke AI otomatis; secret tidak masuk log; third-party capture opt-in; no auto-approval/call answer. |
| Q-6 | Interaksi dapat dipakai tanpa mouse | Keyboard/focus/label aksesibilitas, high contrast, reduced motion, HiDPI diuji. English minimal; Indonesian conditional OQ-17. Standar konformansi formal belum ditetapkan. |
| Q-7 | Distribusi dan recovery dapat diverifikasi | Install/upgrade/uninstall paket target, single instance, penyimpanan transaksional dan migration tanpa overwrite diam-diam. Audit dependency/asset/license sebelum publikasi. |

Overhead incremental harus dicatat untuk Calendar open/close, FX off/on, observer notifications, 10 stream audio, 1000 reminder, event burst, Teams/agent enabled/disabled, serta Caffeine active/inactive. Target idle baseline tidak boleh dianggap hasil benchmark fitur-fitur tersebut. Strategi uji dan DoD lengkap ada di [VALIDATION](VALIDATION.md).

## 8. Fase dan milestone

| Fase | Cakupan | Exit criteria | Tanggal / owner |
|---|---|---|---|
| P0: foundation / feasibility | Shell, media/status; spike clipboard/audio/shortcut/notification/Teams/Caffeine/scheduler; verifikasi jalur FX; Calendar lokal | Bukti kedua desktop, benchmark, capability matrix, dan keputusan teknis berdasarkan hasil. | TBD / engineering TBD |
| P1: MVP usable | Must berfase MVP; battery, clipboard/notes, controls/audio, AI remote, alerts, reminder, Caffeine, satu adapter agent | Workflow dan AC scope MVP stabil pada kedua target; integrasi unsupported diberi alasan; tidak ada popup ganda. | TBD / owner TBD |
| P1: v1 completion | Salat, brightness internal, Calendar, FX gated, Teams reminder/Join/call gated, adapters notifications, a11y/i18n/multi-monitor, packaging | Gate VALIDATION lulus dengan bukti atau exception owner, klaim support sesuai matriks uji. | TBD / release owner TBD |
| P2: optional | C-12 dan kandidat pasca-v1 | Proposal dan approval scope terpisah; tidak otomatis menahan v1. | Belum dijadwalkan |

Urutan teknis awal ada dalam ARCHITECTURE. Milestone ini merupakan rencana, bukan progres pengerjaan; lihat CURRENT-STATE.

## 9. Dependency dan batasan

C++20 + Qt6/QML dan lisensi kode `MIT OR Apache-2.0` tetap keputusan tercatat (OQ-03/14). Bentuk floating overlay tidak memodifikasi panel. Detail rancangan dan dependency dimiliki ARCHITECTURE dan TECH-STACK.

Kelayakan overlay, clipboard, shortcut, brightness dan Caffeine bergantung compositor/backend/permission yang diuji. AI membutuhkan provider remote dan credential milik pengguna. Sumber FX menggunakan endpoint pilihan pengguna, termasuk MoneyMatch; ketersediaan, ketentuan akses dan rate limit tetap perlu divalidasi; meeting otomatis membutuhkan sumber yang diotorisasi; direct call action membutuhkan API yang dapat diverifikasi; direct approval agent tidak termasuk scope v1. Tidak ada backend Pippo wajib untuk fungsi inti.

Budget, kapasitas tim, deadline, versi minimum dependency/distro, dan owner bernama masih TBD (OQ-37/39). Kandidat teknologi atau link dokumentasi bukan bukti dependency sudah dipasang.

## 10. Risiko dan mitigasi

| Risiko | Dampak | Mitigasi |
|---|---|---|
| Overlay/input/clipboard berbeda per compositor | UX atau fitur inti gagal | Spike KDE/Hyprland, capability matrix, unsupported dengan alasan; tanpa klaim parity belum diuji. |
| Qt/rendering/cache/polling menambah resource | Budget performance terlewati | Benchmark P0 dan per fitur, async I/O, bounded caches, lazy UI, event-driven/adaptive sampling. |
| Data clipboard/notes/meeting/agent sensitif | Kebocoran atau kehilangan data | Consent, redaction, keyring, quota, transactions/backup, detail lockscreen minimal; jelaskan belum ada enkripsi at rest. |
| API MoneyMatch/Teams/agent tidak tersedia atau berubah | Klaim fitur tidak terpenuhi / aksi salah | Verifikasi resmi per versi, gated actions, unavailable/deep-link/Open Agent fallback, tanpa scraping. |
| Kalender/salat/recurrence/clock berubah | Reminder salah atau spam | Validasi metode/timezone, dedupe occurrence, catch-up policy, suspend/resume tests. |
| Inhibitor ditolak/overlay hidden | Caffeine tidak efektif | Probe OS, konfirmasi state, timed expiry, unsupported reason; manual lock tetap dihormati. |
| Packaging/license/terjemahan melebihi kapasitas | Rilis tertunda atau distribusi tidak sesuai kewajiban | Audit per dependency/aset, paket per target teruji, English minimal, jadwal/owner OQ-39. |
| Scope P2 merembet ke MVP | P0/P1 tidak selesai | Server monitor dan extension tidak dibuat sebelum proposal/kontrak API/approval scope. |

Detail mitigasi per fitur ada dalam FEATURES, kontrak data/ownership dalam ARCHITECTURE, dan gate security dalam VALIDATION.

## 11. Rollout dan pengukuran

Urutan distribusi yang sudah dicatat: artefak unduhan RPM/DEB/Arch x86_64 pada beta/v1; managed repos dievaluasi setelah beta. Jangan menerbitkan klaim semua DE didukung hanya karena paket dapat dipasang.

Peserta beta, tanggal, komunikasi/support, pemilik rilis dan prosedur rollback/migration masih TBD (OQ-39). Sebelum beta publik, lengkapi petunjuk penggunaan/privacy/troubleshooting berdasarkan perilaku yang benar-benar diuji, paket/checksum, hasil uji dan matriks kombinasi yang diklaim. Belum ada data pengguna yang perlu dimigrasikan di workspace ini; kebijakan upgrade/backup tetap diperlukan sebelum rilis menyimpan data nyata.

Ukur G-1 dengan bukti tugas pengguna yang disepakati di OQ-35; ukur teknis menggunakan protokol OQ-38 dan VALIDATION. Telemetry tidak wajib dan harus opt-in bila dipilih. Tidak ada angka adopsi/retensi atau hasil benchmark yang dapat diklaim saat ini.

## 12. Keputusan, pertanyaan terbuka, dan perubahan

Register lengkap dengan ID, owner per peran, blocker dan rationale ada di [DECISIONS](DECISIONS.md). OQ-21 dan OQ-24 sampai OQ-33 sudah DECIDED; OQ-34 DEFERRED/P2 optional sampai owner menyediakan contoh API/auth. OQ-22/23 tetap ditutup karena public holiday dikeluarkan dari scope. Kondisi OQ-11/15/17 tetap berlaku.

OQ-32 lengkap: minimum interval 1 menit; interval awal form kosong/wajib diisi; catch-up maksimal satu ringkasan per reminder lalu jadwal normal; snooze 5/10/15 menit + custom default 10; DND sistem tanpa quiet-hours scheduler tambahan; akhir default terus sampai dimatikan dengan opsi tanggal/jumlah. Requirement lain tetap pada capabilities dan FEATURES, tanpa menyatakan integrasi telah diuji. Pertanyaan aktif tersisa OQ-35 sampai OQ-39.

Yang belum dapat difinalkan: kontrak akses/rate-limit FX, capability adapter notifikasi yang teruji, dukungan dan deteksi konflik shortcut per desktop, autentikasi/izin tenant kalender Microsoft, validasi scheduler meeting dan handler Teams, validasi direct call actions dan ketersediaan/privasi metadata caller, jalur event dan handoff Codex CLI, sisa default reminder dan validasi inhibitor Caffeine, serta metrik produk dan kesiapan rilis. Owner spesifik dan due date masih TBD. Pertanyaan P2 tidak menahan spike independen P0.

Riwayat PRD dan dokumen pendamping dipusatkan di [CHANGELOG](CHANGELOG.md). Versi dokumen bukan versi aplikasi. Restrukturisasi 0.6.0 memisahkan kontrak fitur, keputusan, validasi dan current state agar agent dapat membaca konteks produk tanpa menelusuri source untuk mengetahui status proyek.
