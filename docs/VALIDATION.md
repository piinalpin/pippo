# Validasi dan Kesiapan Rilis Pippo

| Metadata | Nilai |
|---|---|
| Versi dokumen | 1.15 |
| Diperbarui | 2026-11-17 |
| Status | Rencana pengujian; belum ada build, test executable, benchmark, atau paket |
| Asal | Gate security, compatibility, packaging, dan testing PRD v0.5.2 |

[Indeks](README.md) · [PRD](PRD.md) · [Fitur](FEATURES.md) · [Current state](CURRENT-STATE.md)

Requirement dan checklist di dokumen ini belum dilaksanakan. Status aktual dan lokasi bukti dicatat di CURRENT-STATE; jangan mengartikan target sebagai hasil.

## Compatibility matrix dan strategi platform

**Target resmi v1: KDE Plasma Wayland dan Hyprland (OQ-02).** Tabel berikut adalah rencana, **bukan** hasil implementasi atau pengujian.

| Lingkungan | Status scope | Floating top pill | Clipboard history | Risiko penting |
|---|---|---|---|---|
| **KDE Plasma Wayland** | **Tier 1 / v1 blocker** | Qt Wayland + Layer Shell Qt | integrasi Wayland/per-desktop; perlu validasi runtime | stacking atas panel, focus dan fullscreen |
| **Hyprland Wayland** | **Tier 1 / v1 blocker** | layer-shell supported pada protokol compositor yang sesuai | backend data-control/Hyprland; perlu validasi | input, portal hotkey, monitor/scale |
| Sway/wlroots Wayland | Tier 2 (best effort) | umumnya layer-shell | dapat berbeda per compositor | jangan otomatis menyatakan supported |
| GNOME Wayland | **Out of scope v1** | tidak mendukung protokol Layer Shell umum; perlu extension/alternatif terbatas | background clipboard dan overlays perlu jalur tersendiri | GNOME Shell Extension GJS menjadi kandidat P2 |
| X11 KDE/GNOME/XFCE | **Out of scope v1** | memerlukan backend EWMH/WM-specific, bukan layer-shell | perlu backend X11 | stacking, fokus, tray/window rules |

**Aturan produk:** fitur yang tidak didukung backend harus disabled dengan reason, bukan silently fail. Saat startup probe Wayland globals, compositor, portal, D-Bus services, display scaling, dan privileges. `exclusive zone=0` dan anchor/stacking dipakai sebagai target floating overlay yang **tidak menggeser layout atau mengubah panel sistem**, tetapi perlu dibuktikan pada KDE dan Hyprland.

**Perluasan lintas DE:** memilih C++ + Qt6/QML **tidak** memberi akses overlay GNOME secara otomatis. Hambatan utamanya protokol dan kebijakan compositor. **OQ-03 DECIDED**; [DECISIONS](DECISIONS.md#oq-03-stack) memuat trade-off. **Audio PipeWire, global shortcuts dan notification observer diperiksa sebagai capability independen dari overlay.**

## Security, reliability, dan accessibility

- Beroperasi sebagai user biasa; tanpa root/sudo/polkit otomatis. Setiap permintaan izin harus jelas, terarah dan bisa dibatalkan.
- Tidak mencatat isi clipboard, notes, prompt, API key, token, HTTP auth header di log; dukung redaction; diagnostics opt-in.
- AI endpoint dan artwork yang remote divalidasi sesuai policy. Network timeout, size limit, dan TLS verification wajib; hindari local file path traversal dan decompression bomb pada image.
- Clipboard capture dimatikan pada private mode; sensitive hints dihormati saat tersedia, tapi perlindungan password tidak dianggap sempurna; enkripsi at rest untuk private data **bukan kewajiban scope v1** (OQ-19).
- Keyboard-only, screen reader label bila backend mendukung, high contrast, reduced motion. **English primary; Indonesian target kedua jika kapasitas memungkinkan**. Semua UI strings memakai resource i18n sejak awal, bukan hardcoded.
- Recovery: SQLite transaction + migrations dengan backup skema, corruption handling tanpa overwrite diam-diam; worker error tidak menyebabkan process-wide crash.
- **FX data governance:** provenance/timestamp/fee semantics wajib untuk FX; **jangan mencantumkan data MoneyMatch sebagai milik Pippo atau membangun scraper private API**. Calendar view lokal tidak mengambil data eksternal.
- **Notification privacy:** pengamatan konten app lain dibatasi adapter + consent; tidak log notifikasi browser/pesan/email/OTP/terminal, tidak simpan ke disk by default; no daemon takeover atau privilege escalation diam-diam.
- **Meeting/Teams security:** Microsoft Graph memakai delegated OAuth2 + PKCE atau official library flow yang valid untuk installed desktop app, scope minimum, token di keyring, tenant consent; jangan reuse session cookie token Teams PWA. Link join tervalidasi dan tidak auto-launch tanpa click; caller info default tersembunyi, tampil hanya setelah opt-in dan jika metadata adapter tersedia; fallback label generik tanpa data/consent dan nonaktifkan opt-in harus diuji (OQ-29 A); aksi call dari D-Bus hanya pada instance/app yang diizinkan.
- **Agent approval safety (OQ-31 A):** input notifikasi agent dianggap untrusted; v1 hanya notifikasi + Open Agent, tanpa tombol atau request Approve/Reject dari Pippo. Review/approval tetap di agent. Event text maupun membuka agent tidak mengubah pending menjadi approved; source/event identity, validasi payload, dedupe, expiry dan handoff target harus diuji.
- **Reminder/Caffeine safeguards:** reminder tidak menjalankan command secara otomatis; DND serta lockscreen meminimalkan detail meeting/agent; Caffeine tidak bypass manual lock atau policy perusahaan dan default no suspend-inhibit.
- **Server Monitor P2:** endpoint user-owned saja, jangan scan network otomatis; API credentials disimpan aman, SSRF/network safety review untuk konfigurasi endpoint, TLS enforced, no default periodic network requests.
- **Audio privacy & safety:** audio stream metadata saja; **tidak merekam/meng-capture audio**, tidak mengintip mic/input tanpa izin, jangan menaikkan gain >100% tanpa opt-in.

## Distribution, packaging, dan updates

- Tahap v1/beta: **downloadable release artifacts** `.rpm` (Fedora/openSUSE yang diuji), `.deb` (Debian/Ubuntu yang diuji), paket Arch/PKGBUILD/AUR bila workflow siap; x86_64. Tidak menjalankan repo APT/DNF sendiri sebelum beta selesai.
- **Post-beta / P2:** managed repository APT/DNF dan distribusi terkurasi opsional setelah kapasitas signing, hosting, security update dan maintenance dinilai; AUR/package index tidak otomatis sama dengan repository yang dikelola developer.
- `desktop` entry, app icon, XDG autostart toggle, README compatibility KDE/Hyprland, license notices, CHANGELOG, manajemen Qt6/QML runtime dependencies, dan clean uninstall.
- CI menghasilkan artefak + checksum; signatures direkomendasikan untuk rilis publik. dependensi Qt/CMake dipin; SBOM/license audit dan supply-chain check sebelum release.
- Tidak ada self-update yang menjalankan `sudo`/elevasi diam-diam; user memasang paket baru melalui package manager sesuai distro.

## Test strategy dan definition of done

### Required test matrix v1

- **KDE Plasma Wayland**: setidaknya satu release Fedora KDE dan satu Arch-based KDE yang aktual pada saat beta.
- **Hyprland Wayland**: setidaknya satu Arch-based dan satu RPM/Debian-based installation yang layak diuji (jika paket tersedia); versi compositor/portal harus dicatat.
- Skenario: 1 monitor 1080p baseline + multi-monitor/HiDPI/fractional scaling; laptop dengan baterai dan desktop tanpa baterai; Wi-Fi/Bluetooth absent; laptop brightness, PipeWire/WirePlumber; **2-10 simultaneous app audio streams, device unplug/hotplug, USB/Bluetooth headsets**; YouTube/Spotify/VLC jika MPRIS aktif; **native notification, PWA pada Firefox/Chromium-supported mode, notify-send terminal test, DND/lockscreen/fullscreen; Microsoft 365 calendar mock / manual meeting URL, community teams-for-linux with opt-in D-Bus where available, agent hook fixture, 30m caffeine/idle policy**.
- GNOME Wayland dan X11: exploratory smoke test jika memungkinkan, **tidak menjadi v1 release gate**.

### Automated tests

- Unit: state machine, fullscreen default hide/override, display selection, priority queue, capability flags, retention 30/500/250, clipboard image sanitizer, prayer method/timezone, provider error mapping; QDate/QCalendar month navigation, locale first-day-of-week, leap-year handling, date selection, FX JSON field mapping/raw-decimal parsing (termasuk MIME text/html), invalid/nonpositive/HTML/error responses, decimal precision, fetched-at vs provider timestamp, staleness/fee distinction, audio per-stream ID mapping, notification dedupe/DND/redaction; reminder minimum 1 menit diterima/diuji dan interval di bawahnya ditolak (OQ-32 A), repeating 5/30/60-min + timezone/DST + sleep/resume/restart catch-up maksimal satu ringkasan per reminder tanpa replay/duplikasi, jadwal berikutnya tetap normal + snooze preset 5/10/15 menit/custom dan default 10 menit/skip, meeting T−5 with cancellation, safe URL handlers, incoming call state + action gating, agent event dedupe/approval expiry, idle-inhibit lifecycle.
- Integration: D-Bus mocks (MPRIS/UPower/BlueZ/NetworkManager), SQLite migrations, clipboard Wayland backend, media multi-player, HTTP mock streaming/stop/401/429, keyring failure, note autosave; PipeWire dummy streams + sink moves, shortcut portal mock, freedesktop notification publisher/allowed observer, mock FX JSON dan MoneyMatch-style text/html berisi angka, transport/parsing error atau unavailable state, Calendar view locale/date tests without HTTP or provider fixtures; Graph delegated auth/mock calendarView, meeting link handoff, compatible Teams-for-Linux D-Bus API mock, Codex CLI event-adapter fixture sesuai jalur/payload yang nanti diverifikasi (belum ada hook/IPC contract final), authenticated event validation, portal inhibit mock and release/expiry.
- E2E (KDE + Hyprland): hover/peek/expand/ESC/click-outside, focus, fullscreen, top-center stacking without changing desktop panels, clipboard text/png/jpeg restore, AI remote API key, prayer visual reminder, monitor hotplug, sleep/resume; FX unavailable/success if authorized, local Calendar month navigation, Today and selected-date behavior, app A/B audio levels and independent sink, PWA/native/notify-send notification workflows with compatible adapters, OS notifications survive Pippo shutdown; meeting T−5 + manual join, incoming-call D-Bus accept/decline where capable, PWA fallback, agent complete/approval notification + Open Agent tanpa tombol/request Approve/Reject, repeated reminders, Caffeine timed expiry/manual lock.
- Benchmark P0 pada 8 GiB/iGPU/1080p: CPU/RSS warm/cold, 10-minute idle, click p95, hover p95, 60 FPS animation, 24-hour soak, widget-specific memory growth; FX auto-refresh off/on, Calendar lazy-load/open-close 100 cycles, 10 audio streams with hotplug, burst 100 notifications with dedupe, notification observer off/on; 1000 persisted reminders, 100 burst agent notifications, Teams D-Bus observer active/idle, Caffeine active/inactive; server monitor P2 excluded from v1 acceptance.

### Definition of done (v1)

1. Scope P0/P1 lulus acceptance pada **KDE Plasma Wayland dan Hyprland**, atau exception terukur ditetapkan secara eksplisit sebelum release.
2. Tidak ada widget yang menggeser panel atau mengubah konfigurasi panel sistem; fullscreen **hide by default** berhasil.
3. Clipboard retention (30 hari/500 item/250 MiB), PNG/JPEG, restore-to-clipboard, local filesystem access controls, dan manual purge teruji.
4. Ask AI **remote** multi-provider BYOK dan notes berfungsi tanpa meminta local model runtime; tidak ada prompt/clipboard send tanpa aksi user.
5. Paket RPM/DEB/Arch teruji instalasi/upgrade/uninstall pada kombinasi distro+DE yang diklaim, dari **release files**.
6. Perf P0 terpenuhi atau ada keputusan exception tertulis; dokumentasi privacy, troubleshooting, compatibility, disclaimer metode salat, dan lisensi terbit.
7. **Audio Mixer** teruji volume/mute/route per active app stream; shortcut awalnya kosong, binding configurable dan pemberitahuan konflik/tidak menimpa binding lain, keterbatasan deteksi serta fallback KDE/Hyprland jelas; default sink/source dan hotplug; browser tab granularity dinyatakan jika tidak tersedia.
8. **Local Calendar View** menampilkan month grid, Today, previous/next month, dan tanggal terpilih; tidak membutuhkan jaringan atau data public holiday. **FX custom endpoint/MoneyMatch** mendukung JSON mapping atau angka polos dengan Test Endpoint, sumber dan fetched-at; timestamp provider hanya jika tersedia. Validasi akses/rate limit dan fallback stale/unavailable wajib, tanpa rate palsu.
9. **Notification hub** tidak mengambil alih notification daemon sistem, memiliki jalur observasi yang didukung untuk kasus yang diklaim, tidak duplikasi OS popup dan menghormati privacy/DND; capture pihak ketiga hanya atas opt-in/izin.
10. **Reminders** mendukung one-shot dan repeat minutes/hours dengan minimum 1 menit serta snooze/pause; catch-up setelah aktif kembali maksimal satu ringkasan per reminder terlewat, lalu jadwal normal tanpa replay/duplikasi (OQ-32 A); snooze preset 5/10/15 menit + custom dengan default 10 menit (OQ-32 A); quiet hours mengikuti DND sistem tanpa jadwal tambahan Pippo, uji DND aktif/nonaktif dan capability unavailable; akhir default terus sampai dimatikan, opsi tanggal akhir/jumlah menghentikan pengulangan baru setelah batas tercapai (OQ-32 A); interval awal form kosong/wajib diisi user, tanpa autofill 1/30 menit dan tidak dapat disimpan kosong; **Caffeine** default OFF, aktivasi default 30 menit/display idle, suspend inhibition hanya opt-in, mode indefinite memiliki status terlihat/kontrol OFF, dapat dimatikan dan auto-expire tanpa spoofing aktivitas/Teams presence (OQ-33); uji kedua compositor.
11. **Teams meeting** mendukung sinkronisasi kalender Microsoft setelah consent user dan sesuai izin tenant, ditambah input waktu/link manual sebagai fallback lokal (OQ-27 A); kedua jalur diuji untuk reminder T−5 dan **Join**, termasuk consent ditolak/dicabut atau sinkronisasi gagal tanpa notifikasi palsu; Join menggunakan browser default OS tanpa override dan menghormati handler yang diganti user melalui pengaturan (OQ-28 handler A); reminder tambahan saat mulai default OFF, hanya muncul setelah user mengaktifkan dan berhenti jika dimatikan tanpa menghapus T−5; incoming call action diuji **hanya pada supported opt-in adapter** (mis. `teams-for-linux` D-Bus), PWA fallback tidak mengklaim accept-call.
12. **AI Agent** Codex CLI yang didaftarkan dapat melaporkan completion/approval melalui trusted adapter; Pippo hanya notifikasi + Open Agent, review/approval dilakukan di agent, tanpa tombol/request Approve/Reject atau status approved lokal. Event transport/handoff harus diverifikasi; tidak ada auto-approval. **Server monitor adalah P2 optional** dan bukan release blocker v1.

## Merekam bukti dan menyatakan selesai

Untuk setiap FR/C/Q yang dikerjakan, catat ID requirement, revisi source (commit jika tersedia), perintah/skenario, hasil pass/fail, tanggal, environment (distro, Qt, compositor, portal, hardware), serta lokasi laporan. Gunakan nama laporan stabil/deskriptif dalam `docs/` saat bukti tersedia; jangan membuat file laporan kosong.

- Unit test lulus tidak membuktikan overlay, clipboard, shortcut, atau inhibitor bekerja pada compositor nyata.
- `implemented` berarti perilaku telah dibuat tetapi verifikasi belum lengkap; `verified` membutuhkan bukti sesuai scope klaim; `released` membutuhkan artefak dan versi yang dapat dirujuk.
- Fitur conditional dinyatakan unavailable dengan alasan dan fallback yang telah diuji apabila gate belum terpenuhi. Jangan menandai integrasi live/Accept Call sebagai verified hanya karena fallback bekerja.
- Release blocker yang belum lulus harus tetap terbuka atau mempunyai exception yang disetujui owner. Catat exception, dampak, dan batas klaim dalam CURRENT-STATE dan CHANGELOG.
- Protokol benchmark yang belum jelas (warm-up, ukuran sampel, ambang growth, periode crash-free) harus ditetapkan melalui OQ-38 sebelum hasil disebut memenuhi seluruh target.
