# Katalog Fitur Pippo

| Metadata | Nilai |
|---|---|
| Versi dokumen | 1.20 |
| Diperbarui | 2026-11-17 |
| Status | Spesifikasi rencana; seluruh fitur belum diimplementasikan di workspace ini |
| Asal | Rincian UX dan FR-01 sampai FR-22 dari PRD v0.5.2 |

[Indeks](README.md) · [PRD](PRD.md) · [Current state](CURRENT-STATE.md) · [Keputusan](DECISIONS.md)

Dokumen ini memiliki kontrak perilaku dan acceptance criteria fitur. Prioritas/fase mengacu pada PRD; status pengerjaan hanya dicatat di CURRENT-STATE. Kata “dukung”, “simpan”, dan “tampilkan” di bawah adalah requirement, bukan laporan implementasi. ID FR tetap dipertahankan agar perubahan dapat dilacak.

Untuk pertanyaan produk baca PRD. Untuk mengerjakan satu fitur, baca bagian FR terkait beserta UX shell di bawah, lalu boundary fitur di ARCHITECTURE, lokasi kode aktual di [CODEMAP](CODEMAP.md) dan gate verifikasi di VALIDATION. AC di sini belum mempunyai test executable.

## Peta fitur

| ID | Fitur | Target | Syarat / batas |
|---|---|---|---|
| FR-01 | CPU & RAM | P0 → MVP | Sampling adaptif |
| FR-02 | Battery | MVP | Perangkat/layanan tersedia |
| FR-03 | Now Playing | P0 → MVP | Player mengekspos MPRIS |
| FR-04 | Clipboard history | MVP | Backend KDE/Hyprland teruji; PNG/JPEG |
| FR-05 | Control panel | MVP; brightness internal v1 | Capability/permission OS |
| FR-06 | Quick notes | MVP | Lokal, autosave |
| FR-07 | Salat | v1 | Kalkulasi dan metode terverifikasi |
| FR-08 | Ask AI BYOK remote | MVP | Provider/model dipilih user |
| FR-09 | Provider SSO | P2 conditional | Program resmi; OQ-15 |
| FR-10 | Customization / mini menu | MVP | Full menu saat expanded |
| FR-11 | Notifikasi Pippo / recovery | MVP | Single instance; OS tetap bekerja |
| FR-12 | FX custom endpoint / MoneyMatch MYR→IDR | v1 conditional | OQ-21 DECIDED; JSON mapping atau angka polos, sumber/rate limit perlu validasi |
| FR-13 | Calendar lokal | v1 | Offline; tanpa public holiday |
| FR-14 | Audio mixer | MVP | OQ-25/26 DECIDED A: playback aktif; shortcut kosong/configurable, konflik ditampilkan |
| FR-15 | Notification hub | MVP basic → v1 adapters | OQ-24 DECIDED A; first-party + opt-in adapter tersedia, tanpa daemon takeover |
| FR-16 | Meeting reminder T−5 | v1 | OQ-27 A: kalender Microsoft berizin + manual fallback; OQ-28 A: at-start opsional/default OFF |
| FR-17 | Join meeting | v1 | OQ-28 handler DECIDED A: browser default OS, user bisa mengganti; link tervalidasi |
| FR-18 | Incoming call | v1 conditional / P2 PWA | OQ-29 DECIDED A: adapter teruji atau Open Teams; caller tersembunyi default, opt-in jika data tersedia |
| FR-19 | AI agent events / approval | MVP basic → P2 connectors | OQ-30/31 A: Codex CLI pertama; notifikasi + Open Agent, tanpa direct approval v1; event/handoff perlu verifikasi |
| FR-20 | Reminder berulang | MVP | OQ-32 A: minimum 1 menit, catch-up satu ringkasan/reminder, snooze 5/10/15 + custom default 10; DND sistem tanpa quiet hours khusus; akhir default tanpa batas dengan opsi tanggal/jumlah; interval awal kosong/wajib diisi |
| FR-21 | Server/VM/bare-metal monitor | P2 optional | OQ-34 DEFERRED, rekomendasi diterima; tunggu kontrak API |
| FR-22 | Caffeine | MVP | OQ-33 DECIDED: OFF, default aktivasi 30 menit/display idle; suspend opt-in, OS dapat menolak |

## UX shell dan alur pengguna

### Shell visual

- Posisi **floating top-center**, secara visual dekat tepi atas layar tanpa menjadi bagian panel sistem; offset Y dan pilihan monitor dapat diubah. Jangan memodifikasi panel KDE/Hyprland. Perilaku overlap panel/system notification harus diukur.
- Bentuk collapsed: capsule hitam/tema adaptif dengan sudut penuh membulat, tinggi sekitar 28-36 px; lebar dinamis sekitar 110-260 px tergantung konten.
- **Mini menu bar**: dalam **collapsed** hanya 1-3 indikator kontekstual prioritas (mis. media icon, baterai, jam, *notification count* bila diaktifkan) agar pill tidak melebar; **full mini bar** hanya tampil pada **expanded** dengan tab **Media, Audio, System, Clipboard, Calendar, FX, Notifications, Notes, Prayer, AI, Reminders**, plus akses **Caffeine** dan agent activity melalui quick action/overflow (overflow/responsive grouping). Bukan pengganti panel sistem. **Kurs dan kalender penuh bukan item permanen collapsed**.
- Expanded: panel dengan radius 16-24 px; target lebar adaptif ~320-520 px, dibatasi area layar; tinggi maksimum mengikuti work area. Layout satu kolom untuk layar sempit dan widget grid pada ruang cukup.
- Theme auto light/dark, skala sesuai fractional scaling/HiDPI, motion reduction mengikuti preferensi accessibility, keyboard navigation dan contrast cukup.

### State machine

| State | Masuk ketika | Perilaku keluar |
|---|---|---|
| `collapsed` | default/tidak ada interaksi | pill ringkas; click → expanded, hover → peek bila enabled |
| `peek` | hover setelah adjustable debounce (default usulan 80 ms) | info media/status singkat tanpa mengambil fokus; click → expanded; mouse leave → collapsed setelah grace 250 ms |
| `expanded` | click pill, shortcut global, action tertentu | panel interaktif dengan tab/widget; ESC, click outside, atau timeout inactivity → collapsed (kecuali sedang mengetik) |
| `live_activity` | track change, **per-app volume**, charging, prayer, **notifikasi terpilih**, dll. | animasi singkat/indikator nonblocking, lalu kembali ke state sebelumnya; hindari duplicate OS toast |
| `hidden` | fullscreen (default), DND, manual hide, screen lock | tanpa input interception; kembali ketika diizinkan |

Aturan prioritas tampilan: **aksi user > incoming call / approval AI berurgensi tinggi (sesuai DND/OS) > meeting & user reminder > agent completion > media/audio event > passive stats**. Tidak ada event yang boleh menghentikan input user pada notes/AI. Event bertubi-tubi di-*coalesce*; tidak ada animasi pop-up tanpa batas. Perubahan status tidak boleh mencuri keyboard focus. Pilihan **hover-to-expand** opsional; default hover hanya *peek* untuk mencegah *accidental opens*.

### Responsivitas & perangkat

- Perubahan ukuran panel menggunakan animasi transform/opacity pendek (~160-250 ms), dapat dimatikan.
- Mouse, trackpad, keyboard, dan layar sentuh bila tersedia; hit target >= 32 px, keyboard focus terarah.
- Saat browser/game fullscreen: **default `hide`**, termasuk ketika video fullscreen; user dapat override menjadi `show-on-hotkey` atau `always-show` jika compositor mengizinkan. Screen lock selalu mengikuti keamanan OS.
- Multi-monitor: pilih primary, specific output, atau follow active monitor (jika sistem menyediakan sinyal yang cukup); persist per output ID dengan fallback saat unplug.
- Tidak menutupi notifikasi/interaksi system UI secara permanen; tidak memakai keyboard grab global diam-diam.
- Menu **Audio** dapat dibuka dengan shortcut global yang user setujui; jika portal tidak menyediakan shortcut, tampilkan instruksi keybind compositor/manual, bukan merebut key events.
- Caffeine punya indikator *awake* saat aktif dan toggle/quick access yang dapat dikonfigurasi; indikator ringkas tidak harus tampil terus saat collapsed. Meeting reminder dapat menampilkan tombol hanya saat action tersedia dan user mengizinkannya. Permintaan approval agent pada v1 hanya menyediakan Open Agent; tidak ada tombol Approve/Reject di Pippo (OQ-31 A).
- Calendar menandai **hari ini** secara jelas. Navigasi bulan/tahun dan label hari mengikuti locale sistem, tanpa menampilkan penanda public holiday atau membutuhkan API internet.

### Contoh alur utama

**Mendengarkan musik:** YouTube/Spotify mulai → MPRIS detected → mini bar menunjukkan artwork/judul ringkas → animasi track change singkat → hover memperlihatkan play/pause & skip → click membuka controls dan progress (seek hanya bila supported).

**Copy gambar:** pengguna copy gambar → backend clipboard yang permitted mendeteksi MIME image → thumbnail tersimpan lokal → buka Clipboard → preview → click memulihkan entry ke clipboard → pengguna paste; *auto-paste* hanya pada backend yang memang mampu melakukannya.

**Jadwal salat:** pengguna memilih kota/koordinat, zona waktu dan metode → aplikasi menghitung 5 waktu offline → 10 menit sebelum jadwal (contoh konfigurasi) muncul reminder nonintrusif → tombol snooze/dismiss.

**Ask AI:** pengguna membuka Ask AI → memilih provider dan model → memasukkan prompt → jawaban di-stream → tersedia copy/stop/retry; memasukkan teks clipboard sebagai context membutuhkan tombol dan konfirmasi eksplisit.

**Kurs MYR→IDR:** user membuka FX → memilih MYR→IDR → memasukkan contoh `MYR 1.000` → melihat **rate, estimasi IDR, basis fee/net payout jika tersedia, provider MoneyMatch, "last updated"** → tombol **Open MoneyMatch**. Jika rate tidak boleh/ tidak bisa diambil, tampilkan `Rate unavailable` dengan tautan resmi, tanpa data palsu atau sumber alternatif yang diam-diam diberi label MoneyMatch.

**Calendar view:** user membuka Calendar → melihat grid bulan berjalan dengan tanggal hari ini ditandai → navigasi previous/next month dan tombol Today; memilih tanggal menampilkan tanggal dan hari terkait. Calendar dapat dipakai tanpa internet. Tidak ada holiday source picker, warna merah khusus hari libur, atau refresh provider.

**Volume per aplikasi:** user menekan shortcut `Open Audio Mixer` → daftar **stream playback aktif** (contoh Spotify, browser, media player) → mengatur volume/mute masing-masing dan memindahkan stream ke speaker/headset/output yang tersedia → sistem mengonfirmasi perubahan sebenarnya; device disconnect punya fallback terukur.

**Notifikasi native/PWA/terminal:** browser PWA, aplikasi native, atau terminal mengirim notifikasi melalui jalur desktop yang didukung → sistem notifikasi desktop tetap bekerja → Pippo dapat menampilkan indikator/riwayat ringkas **hanya jika ada API/adapter pengamatan yang sah/diaktifkan**; klik action diarahkan ke aplikasi sumber bila supported. Tidak mengintersep seluruh notification bus sebagai asumsi universal.

**Meeting reminder + Join:** pengguna menyambungkan kalender Microsoft dengan persetujuan scope baca atau menambahkan meeting URL manual → Pippo menghitung jadwal dengan timezone event → **default reminder 5 menit sebelum mulai** → pilih **Join** untuk membuka URL Teams melalui browser default OS, atau handler lain yang dipilih user di pengaturan (OQ-28 A). Notifikasi tidak membuat user masuk ke meeting otomatis.

**Incoming call Teams:** bila `teams-for-linux` komunitas mengaktifkan D-Bus control, `IncomingCallChanged` → Pippo menampilkan caller (hanya jika opted in) dengan **Accept Audio**, **Accept Video**, atau **Decline** dan meneruskan method terkait; jika adapter tidak tersedia, tampilkan **Open Teams** saja. Untuk Teams PWA web biasa, jangan klaim bisa menjawab panggilan secara langsung.

**AI Agent:** user mendaftarkan satu sumber agent yang didukung → agent melaporkan `completed`, `failed`, atau `approval_requested` melalui adapter terotorisasi → Pippo memberi notif + ringkasan terbatas → click **Open Agent** membawa user kembali ke agent untuk review/approval. V1 tidak menampilkan tombol atau mengirim request **Approve/Reject** dari Pippo (OQ-31 A).

**Reminder repeatable:** user membuat contoh reminder `Stretch every 45 minutes`, mengatur interval 45 menit, waktu aktif, dan kebijakan snooze; reminder aktif selama jendela jadwal, tidak menumpuk saat suspend/resume, dan dapat di-pause/edit/delete.

**Caffeine:** user klik ikon cangkir → Pippo mengaktifkan idle-inhibit selama 30 menit atau hingga dimatikan → layar tidak timeout melalui kebijakan idle yang didukung → timer selesai/manual off melepaskan inhibitor. Lock manual, kebijakan admin, dan kondisi sleep kritis tetap dihormati.

**Server monitor (P2):** user nanti memasukkan endpoint liveness/readiness yang disediakan sendiri + auth → Pippo menampilkan `healthy`, `not ready`, `unreachable`, `unknown` untuk host yang didaftarkan, menggunakan polling berbatas dan backoff. Fitur ini **belum ditentukan detailnya** dan tidak menjadi P1 acceptance.

## Kontrak fitur dan acceptance criteria

Semua ID harus ditautkan ke bukti pengujian ketika implementasi tersedia. P0/P1 adalah rencana scope rilis 1.0; P2 disebut eksplisit. Label fase ringkas pada heading lama diperjelas oleh peta fitur di atas dan fase PRD.

### FR-01: CPU & Memory Monitor [P0]

- Tampilkan persentase total CPU dan RAM used/total, opsi grafik kecil 30-60 detik.
- `/proc` via reusable C++/Qt sampler; polling adaptif: sekitar 2 detik ketika widget terlihat, 10-15 detik ketika collapsed, off ketika fitur dinonaktifkan.
- **AC:** angka update tanpa freeze; beda terhadap tool referensi terukur dalam toleransi sampling; saat hidden tidak melakukan sampling frekuensi tinggi; multi-core dihitung konsisten.

### FR-02: Battery [P1]

- Battery percentage, charging/discharging/full/unknown, estimasi waktu bila UPower menyediakannya; tidak menampilkan 0% palsu ketika perangkat desktop tidak memiliki baterai.
- Tampilkan low battery alert opsional (contoh default 20% dan 10%) dengan anti-spam.
- **AC:** perubahan UPower tercermin <= 3 detik setelah sinyal diterima; status absent/unavailable ditampilkan secara tepat; multi-battery diaggregasi hanya bila valid.

### FR-03: Now Playing & Animation [P0]

- Temukan media player melalui **MPRIS2 D-Bus** (Spotify, Firefox/Chromium/YouTube, VLC, dan lainnya **jika** expose MPRIS).
- Metadata title, artist, album, cover art, duration, playback state; tombol play/pause/previous/next/seek sesuai kemampuan MPRIS.
- Deteksi banyak player: prioritas player yang sedang `Playing`, terakhir aktif, pilihan pinned player; user bisa switch.
- Transisi artwork crossfade/scale, progress, compact music pulse berbasis state playback. **Tidak** mengklaim FFT atau audio-reactive real-time tanpa capture audio khusus.
- Artwork dimuat async, cache ukuran terbatas, batasi protocol/ukuran file dan URL, fallback icon bila metadata tidak tersedia.
- **AC:** ganti lagu diperbarui melalui sinyal DBus tanpa polling agresif; media tidak compliant menampilkan unsupported state; animasi otomatis berhenti saat paused/hidden/reduced motion; controls nonblocking dan error surfaced.

### FR-04: Clipboard History, termasuk Image [P1]

- Simpan teks/URL dan gambar **PNG/JPEG** (`image/png`, `image/jpeg` maupun konversi aman dari format clipboard lain jika didukung) saat backend memberi izin; GIF dan SVG bukan v1. Search text, preview thumbnail image, pin/unpin, delete, clear, **restore-to-clipboard** dan retention settings.
- Default **diputuskan**: maksimum **500 item**, maksimum **30 hari**, dan kuota gabungan **250 MiB**; maksimal **20 MiB per item** sebagai guardrail awal yang bisa dikonfigurasi. Pinned items tidak terhapus karena umur tetapi tetap dihitung terhadap quota; setelah di-unpin, item yang melewati retention eligible untuk dihapus. Saat item baru membuat batas terlampaui, hapus item unpinned yang sudah ada, tertua dulu, sampai item baru muat. Jika tidak ada item lama yang dapat dihapus, tolak item baru dengan feedback agar user menghapus/unpin item. Jangan menghapus pinned diam-diam. Lazy-load image thumbnail; dedupe salinan identik berturut-turut.
- Riwayat *local only* secara default, `pause recording`, `clear history`, optional incognito, dan upayakan mengabaikan konten dengan sensitive/password-manager hints; tidak menjamin semua password dapat dideteksi.
- Backend utama: KDE Wayland dan Hyprland melalui data-control/desktop integration yang tersedia; capability diprobe pada runtime dan harus di-benchmark tanpa polling agresif. X11 dan GNOME adalah **future candidates**. Jika background clipboard tidak tersedia, tampilkan unsupported/error yang jujur; **restore-to-clipboard adalah standar v1**, auto-paste hanya optional per backend yang dapat melakukannya aman dan tidak menginjeksi input diam-diam.
- **AC:** teks, PNG, dan JPEG muncul dengan preview benar di KDE/Hyprland supported configurations; survive reboot sampai batas retention; cap item/day/bytes ditegakkan; restore-to-clipboard valid; auto-paste **disabled kecuali** backend aman dan user mengaktifkannya; tidak ada konten otomatis terkirim ke AI; clear/pause menghentikan capture; sensitive hints yang tersedia dihormati.

### FR-05: Control Panel [P1]

- Panel berisi Bluetooth ON/OFF dan status perangkat (BlueZ), Wi-Fi ON/OFF dan koneksi aktif (NetworkManager), volume/mute/output device (PipeWire/WirePlumber dengan fallback PulseAudio bila relevan), brightness percentage **display internal/laptop terlebih dahulu** (backend perangkat/DE dan permission; monitor eksternal opsional P2), jam/tanggal/timezone.
- Slider mendukung wheel/keyboard; status final harus dibaca dari OS, bukan hanya hasil UI optimistis. Tidak menjalankan `sudo` atau mengubah permission system secara diam-diam.
- Jika fitur tidak ada (mis. monitor tanpa backlight control), tampilkan `Unavailable` dan sebab singkat, bukan slider palsu.
- **AC:** setiap kontrol berhasil mengubah *actual OS state* pada test fixture yang supported; roundtrip reflected; kesalahan permission/sink disconnected ditangani; jam timezone berubah saat event OS.

### FR-06: Quick Notes [P1]

- Buat/edit/hapus note, pin, search; plain text dengan dukungan Markdown ringan opsional, autosave debounce (~500 ms), tanpa network.
- Simpan di SQLite lokal; kerusakan autosave tidak menghapus versi terakhir. Batasi jumlah note besar secara UI lazy rendering.
- **AC:** setelah restart catatan tetap ada; typing tidak lag di 10.000 karakter; ESC saat sedang mengetik tidak menutup panel tanpa aturan focus; hasil search tidak memblokir input.

### FR-07: Shalat Reminder [P1]

- Jadwal Subuh, Zuhur, Asar, Magrib, Isya; optional sunrise dan next-prayer countdown. Perhitungan **offline** menggunakan koordinat, timezone IANA, metode kalkulasi pilihan, mazhab/rule Asar, koreksi menit per waktu.
- Onboarding: lokasi **manual** (kota atau koordinat) sebagai default; geolocation **opt-in** dan terpisah dari kalkulasi offline. User dapat memilih metode kalkulasi; opsi "default Indonesia" hanya dipakai jika mapping parameter dan selisih terhadap sumber rujukan lokal telah **terverifikasi**. Jika belum, minta user memilih metode, jangan diam-diam melabeli hasil sebagai jadwal resmi Kemenag.
- Reminder default **silent visual** (in-app + OS notification jika diizinkan); audio adzan/suara dan auto-location **off by default**, tersedia hanya sebagai opt-in user. Pre-reminder, at-time reminder, snooze, mute, DND policy dan countdown harus menghormati suspend/resume, perubahan jam/timezone, DST dan high-latitude.
- **AC:** kalkulasi diuji pada tanggal/lokasi/metode representatif terhadap referensi terverifikasi; notifikasi muncul maksimal 60 detik dari jadwal saat app aktif; setelah resume dari suspend tidak men-trigger reminder lama secara massal; tidak membutuhkan jaringan pada perhitungan offline.

### FR-08: Ask AI: custom provider dan model [P1]

- Pada **MVP**, AI menggunakan **non-local / remote inference** melalui profil `OpenAI API`, `Anthropic API`, `OpenAI-compatible`, dan `Anthropic-compatible`. Tidak perlu mendownload/menjalankan model lokal. Endpoint lokal (mis. Ollama) adalah opsi eksplorasi P2, bukan acceptance criterion MVP.
- Konfigurasi endpoint/base URL, API key, model ID manual, optional discovery bila provider mendukung, temperature/max output yang valid sesuai provider, dan timeout. Streaming response, stop, regenerate, copy; history local-only bisa diaktifkan/dimatikan.
- Native adapter untuk OpenAI Responses dan/atau Chat Completions dengan capability check; Anthropic Messages API dengan header/version sesuai spesifikasi. Jangan menganggap semua endpoint "compatible" mendukung semua fitur.
- Credentials disimpan via Secret Service/keyring (libsecret jika dipilih), **bukan** plaintext config/log; PRD §4 mewajibkan keyring. Jika keyring tidak tersedia, jangan diam-diam menyimpan di tempat lain; penyimpanan alternatif memerlukan keputusan owner baru. SSL certificate diverifikasi; endpoint HTTP yang bukan localhost perlu peringatan jelas.
- Permintaan ke AI hanya terkirim setelah user submit; prompt/clipboard/note tidak dikirim ke luar secara otomatis. Tampilkan provider/model aktif dan status error (401/429/timeouts) secara jelas; no hidden retry of billable requests.
- **AC:** pada MVP, minimal satu model API remote OpenAI dan Anthropic serta satu custom-compatible endpoint teruji; stream dapat dihentikan, profil/model dipilih manual; tanpa local inference requirement; kunci tidak tampil di log; offline/error AI tidak memengaruhi widget lain.

### FR-09: Login provider via SSO [P2, conditional]

- **OpenAI:** evaluasi **hanya pada P2** integrasi resmi **Sign in with ChatGPT** untuk open-source/local apps, jika program dan dokumentasi resmi mengizinkan, dengan OAuth/OIDC + PKCE dan consent. Jalur ini berbeda dari API-key billing dan tidak otomatis tersedia bagi semua model/app. Validasi kompatibilitas SDK dengan aplikasi C++ sebelum komitmen rilis.
- **Anthropic:** **jangan** menggunakan cookies, session token, reverse-engineered Claude login, atau subscription Claude.ai untuk AI request pihak ketiga. Gunakan API key; tambahkan SSO hanya bila Anthropic merilis flow pihak ketiga resmi yang mengizinkan use case ini.
- **AC:** SSO yang disertakan selalu berdasar docs/izin provider terbaru; token refresh/revoke/logout dan consent ditangani aman; jika tidak eligible tombol SSO disembunyikan dan opsi API key tetap tersedia.

### FR-10: Rounded Small Menu Bar dan Customization [P1]

- User dapat mengatur: **offset floating top-center**, ukuran, theme, widget order expanded, **collapsed indicators terbatas**, enable/disable feature, hover behavior, animation speed, autostart, shortcut, fullscreen policy (default hide), tray icon optional. Full minibar hanya ketika expanded; Pippo tidak mengubah konfigurasi panel sistem.
- *Settings* memakai UI satu halaman/panel; konfigurasi disimpan per user di lokasi XDG.
- **AC:** konfigurasi bertahan setelah restart; tidak menempatkan pill di luar layar setelah monitor unplug; mematikan widget benar-benar menghentikan workload background-nya.

### FR-11: Pippo Notifications, Interaction, Recovery [P1]

- In-app live activity ditampilkan singkat; Pippo sebagai **notification client** mengirim notification miliknya via `org.freedesktop.Notifications` (atau XDG Notification portal jika tepat); menghormati DND bila dapat dideteksi. **Pippo tidak otomatis menjadi notification server** atau menggandakan toast dari OS.
- Single instance guard, startup at login via XDG Autostart, update notification melalui package manager (tanpa self-updater root), graceful shutdown, crash recovery config/storage.
- **AC:** launch kedua memfokuskan/memicu instance yang ada; restart tidak menggandakan timers/notifications; unexpected provider exception tidak mematikan core UI.

### FR-12: FX Custom Endpoint dan MYR→IDR MoneyMatch [P1 v1, source-gated]

**Keputusan owner OQ-21 (2026-11-17):** FX mendukung provider pilihan pengguna melalui endpoint custom dan mapping respons JSON. Untuk MoneyMatch, owner memilih [endpoint MYR→IDR](https://transfer.moneymatch.co/utility/rate/MYR/IDR) yang merespons angka polos walaupun Content-Type menyebut HTML. Tambahan mode angka polos mengikuti bentuk respons yang diberikan; rincian validasi/parsing di bawah adalah **(agent's call)**, belum implementasi.

- User dapat mengisi nama sumber, pasangan base/quote currency, URL endpoint GET, autentikasi bila diperlukan (secret di keyring), dan mode respons `json` atau `number`. Jangan menampilkan nama MoneyMatch untuk data sumber lain.
- Mode `json`: mapping field kurs dan timestamp provider opsional memakai path field sederhana (contoh `data.rate`, `data.updated_at`). Field yang tidak ada, tipe salah atau hasil bukan angka valid menghasilkan mapping error. Tidak menyediakan script/eval, ekstraksi HTML, template atau transform arbitrer.
- Mode `number`: gunakan seluruh body setelah trim whitespace sebagai satu angka desimal positif yang valid, terlepas dari MIME `text/html` pada profil ini. Jangan mengekstrak angka dari markup/error page. HTML nyata, body kosong, trailing junk, nol/negatif, NaN/Infinity dan HTTP error ditolak; jangan mengganti hasil menjadi kurs 0 atau contoh hardcoded.
- Sediakan **Test Endpoint** untuk preview hasil, nama sumber/pasangan mata uang dan error transport/parsing sebelum profil digunakan. Batas mode awal adalah GET + JSON mapping / angka polos; bukan framework adapter generik.
- Panel FX menampilkan rate, input MYR, estimasi IDR, sumber, state `live / cached / stale / unavailable`, dan waktu pengambilan. Timestamp provider hanya ditampilkan jika memang tersedia dan valid. Jika respons hanya angka, label **Diambil pada / fetched at**, bukan mengarang **provider updated at** atau menjamin data provider real-time.
- Pisahkan exchange rate dan net recipient payout. Respons angka tidak memuat fee/net payout; jangan menganggap fee nol atau hasil perkalian sebagai payout transaksi final. Pippo tidak login/transfer uang atau mengambil credential akun MoneyMatch.
- Pertahankan presisi angka desimal sumber sampai kalkulasi decimal-safe/fixed-point; pembulatan hanya mengikuti aturan tampilan mata uang. Detail precision/range ditetapkan pada implementasi dan diuji, bukan konversi diam-diam melalui float.
- Refresh on-open/manual; background refresh hanya opt-in setelah rate limit/ketentuan akses ditetapkan. Gunakan async networking, TLS, timeout/backoff, response size limit dan last-known-good cache yang terlabel stale. Jangan mengirim nominal pengguna jika endpoint tidak memerlukannya. Secret tidak ikut log/preview.
- Ketika endpoint gagal/format berubah, tampilkan stale/unavailable dengan alasan dan tautan sumber; jangan mengganti provider diam-diam. URL yang dapat diakses tidak otomatis merupakan API publik dengan kontrak stabil/izin reuse; verifikasi ketentuan sumber dan rate limit sebelum klaim rilis, tanpa bypass login/anti-bot atau peminjaman token aplikasi lain.

#### Profil MoneyMatch yang dipilih

Contoh konfigurasi berikut adalah rancangan dokumentasi, bukan skema config/CLI yang sudah diimplementasikan:

```json
{
  "source": "MoneyMatch",
  "base_currency": "MYR",
  "quote_currency": "IDR",
  "endpoint": "https://transfer.moneymatch.co/utility/rate/MYR/IDR",
  "method": "GET",
  "response_mode": "number"
}
```

Mode ini tidak membutuhkan `rate_path` atau `timestamp_path`, dan tidak membuat timestamp provider. Angka `4442.1491` di bawah hanya hasil pengamatan sesaat; tidak boleh dipakai sebagai fallback/hardcoded rate.

#### Pemeriksaan endpoint MoneyMatch

GET langsung tanpa credential/bypass pada **2026-11-17 11:26:08 WIB (04:26:08 UTC, header Date respons)** memberikan:

```text
HTTP status: 200
Final URL: https://transfer.moneymatch.co/utility/rate/MYR/IDR
Content-Type: text/html; charset=utf-8
Body: 4442.1491
```

Web reader gagal membuka URL; request GET biasa melalui Python urllib berhasil membaca metadata dan body tersebut. Context7 tidak menemukan library MoneyMatch; belum ditemukan kontrak API, izin reuse atau batas refresh dalam pemeriksaan ini. Hasil ini membuktikan respons HTTP saat itu, bukan kestabilan/dukungan provider atau fitur Pippo sudah bekerja. Nilai diinterpretasikan sesuai pasangan MYR→IDR pada URL/profil; tidak memuat jaminan settlement, fee atau waktu update provider.

#### Acceptance criteria

1. Profil JSON dapat mengambil kurs pada path yang dipilih dan menampilkan missing-field/type error dengan jelas; perubahan mapping dapat diuji lewat Test Endpoint.
2. Fixture respons MoneyMatch HTTP 200, MIME `text/html; charset=utf-8`, body `4442.1491` menghasilkan angka desimal yang sama tanpa HTML parser dan tanpa kehilangan presisi sebelum perhitungan.
3. Body dengan whitespace luar dapat diterima; body HTML/error, kosong, trailing junk, NaN/Infinity, nonpositive, field JSON invalid dan HTTP non-success ditolak tanpa menimpa last-known-good dengan nilai palsu.
4. Bila provider tidak memberikan timestamp, hanya fetched-at ditampilkan; kalkulasi MYR/IDR tidak mengklaim fee/net payout atau freshness provider yang tidak tersedia.
5. Timeout/offline menampilkan stale/unavailable yang jujur; shell tetap responsif, tidak polling agresif saat collapsed/disabled, credential tidak bocor dan tautan sumber dapat dibuka.
6. Source/endpoint, ketentuan akses dan rate limit diverifikasi sesuai klaim sebelum rilis. Pemeriksaan GET dokumentasi ini bukan pengganti unit/integration/E2E test implementasi.

### FR-13: Local Calendar View [P1 v1]

**Keputusan scope:** Pippo menampilkan **kalender bulanan sederhana** yang berjalan **sepenuhnya lokal**. Tidak ada integrasi API/feed/ICS public holiday, pilihan sumber Indonesia/Malaysia, pengunduhan data libur, atau kalender hari libur yang perlu dipelihara. Tanggal diperlakukan sebagai tanggal kalender lokal tanpa network request.

#### Behavior dan UX

- Calendar tab menampilkan **month grid**, nama hari, bulan/tahun, dan tanggal sesuai locale pengguna (English/Indonesia saat tersedia).
- **Today** diberi highlight visual yang dapat diakses (tidak bergantung pada warna semata); tersedia tombol **Previous month**, **Next month**, dan **Today**.
- Pilih tanggal untuk melihat detail sederhana: nama hari dan tanggal yang dipilih. Tidak membuat event/reminder otomatis saat tanggal ditekan.
- First day of week mengikuti locale sistem atau pengaturan kalender ringan bila memang diperlukan; tidak membuat settings panel kompleks sejak P0.
- Gunakan `QDate`, `QCalendar`, `QLocale` dan QML untuk data tanggal/tampilan. Tidak perlu `QNetworkAccessManager`, SQLite, provider adapter, atau background scheduler untuk FR-13.
- **Out of scope:** public holidays, status libur nasional/negeri, tanggal merah holiday, daftar event eksternal, sinkronisasi kalender umum, pemilihan negara/provider. **Fitur Teams meeting reminder (FR-16/17) tetap terpisah** dan tidak dianggap sebagai kemampuan Calendar view FR-13.

#### Acceptance criteria

1. Bulan berjalan ditampilkan saat Calendar pertama dibuka; tanggal hari ini akurat sesuai zona waktu lokal sistem.
2. Previous/Next/Today bekerja melewati batas bulan dan tahun, termasuk tahun kabisat.
3. Pemilihan tanggal dan navigasi dapat digunakan dengan mouse serta keyboard; tetap terbaca pada light/dark mode, HiDPI, dan reduced motion.
4. Locale yang didukung menampilkan nama bulan/hari dengan benar; minggu pertama ditata secara konsisten dengan locale.
5. Kalender berfungsi **offline**, tanpa request jaringan atau timer polling saat collapsed; komponen diinisialisasi lazy saat tab dibuka.
6. Tidak ada menu public holiday source, data provider, state/region filter, holiday marker merah, atau dependensi hari libur Indonesia/Malaysia dalam release v1.

### FR-14: Dedicated Audio Mixer: Per-App Volume, Output Device, Shortcuts [P1 MVP]

**Keputusan owner OQ-25 (2026-11-17, A):** v1 mencakup volume, mute dan output untuk playback stream aplikasi yang aktif. Microphone per aplikasi dan aturan routing permanen milik Pippo ditunda ke fase berikutnya. Pemilihan device untuk aplikasi tanpa active playback stream tidak menjadi kewajiban v1; kontrol global/master tetap sesuai scope sebelumnya.

- Sediakan **tab/menu Audio khusus** dalam expanded minibar dan quick access **global shortcut** yang user dapat konfigurasikan. Submenu: **master output**, output device default, input device/microphone master (jika didukung), dan daftar **per-app playback streams aktif** dengan icon/name, volume %, mute/unmute, pilihan **output sink** dan status route. Capture streams/microphone per aplikasi tidak ditambahkan pada v1 sesuai OQ-25; microphone master conditional tidak berarti kontrol per-app capture.
- Backend utama **PipeWire + WirePlumber**, prefer event/subscription API (PipeWire/SPA/WirePlumber atau kompatibel libpulse/PulseAudio abstraction bila memenuhi semua kebutuhan). `wpctl` dapat digunakan untuk troubleshooting, bukan syarat shelling tiap frame. *Stream/node identification* berdasarkan metadata aplikasi yang disediakan backend, fallback label proses bila aman. **Per-app = stream yang audio backend expose**, bukan semua proses yang berjalan atau setiap PWA tab. Browser/PWA mungkin hanya satu stream untuk beberapa tab.
- Operasi volume/mute serta memindahkan **stream aktif** ke device tersedia via media graph; `set-default` global **bukan** pengganti stream move. Jelaskan apakah user men-set **default output for new streams** vs **move this app's current stream**. Pippo tidak membuat aturan routing permanen/custom persistence pada v1. Policy/persistence yang sudah dikelola backend OS tetap dihormati; Pippo tidak menjamin route atau stream identity bertahan setelah restart.
- Perubahan device/hotplug/headset unplug ditangani async; app/stream hilang → row hilang dengan transisi halus; volume OS yang berubah dari tempat lain segera dipantulkan; batasi volume UI default 0-100% untuk safety (amplification >100% harus opt-in dan warning).
**Keputusan owner OQ-26 (2026-11-17, A):** shortcut Audio Mixer awalnya kosong; user memilih kombinasi sendiri dan mendapat pemberitahuan konflik. Tidak ada binding default yang dipasang diam-diam. Konflik tidak diatasi dengan menimpa binding lain otomatis; jika backend tidak bisa memeriksa konflik, jelaskan keterbatasan dan arahkan verifikasi manual.

- Shortcuts: `Open Audio Mixer`, `Master Volume Up/Down`, `Mute`, dan opsional `Next/Previous Output Device`. Tidak mengambil alih tombol hardware tanpa izin. Gunakan **XDG GlobalShortcuts portal** jika backend tersedia; jika tidak, manual keybind KDE/Hyprland melalui action/DBus/CLI yang terdokumentasi, dengan verifikasi konflik dan tanpa injeksi input.
- **AC:** dengan ≥2 stream dari aplikasi berbeda, slider/mute A tidak mengubah B; memilih output untuk A tidak memindahkan B; master volume terpisah; audio device disconnect tertangani tanpa crash; source/app UI tidak freeze saat graph berubah; shortcut awalnya kosong, binding pilihan user dapat dipicu/fallback jelas di KDE dan Hyprland; konflik yang terdeteksi ditampilkan tanpa mengganti binding lain, keterbatasan deteksi dijelaskan; app tanpa active stream tidak diberi slider palsu.

### FR-15: Notifications: Native, PWA, Terminal [MVP basic / v1 adapters, capability-based]

**Keputusan owner OQ-24 (2026-11-17, A):** notifikasi Pippo dan sumber eksternal yang integrasinya tersedia serta diaktifkan user. Inbox notifikasi lintas aplikasi yang lengkap bukan kewajiban v1. Adapter/cakupan sumber harus tetap diuji; ini keputusan scope, bukan laporan integrasi sudah berjalan.

- **Desain:** Pippo punya **notification hub**, compact indicator/count opsional, list recent events yang berhasil didapat, grouping per sumber, severity, timestamp, dismiss, serta action/open-source **hanya jika protokol sumber mendukung**. Notifikasi masuk sebagai *peek/live activity* sesuai rules tanpa mencuri focus, menduplikasi popup sistem, atau mengganggu fullscreen/DND.
- **Native apps:** dukung aplikasi yang memakai freedesktop Notifications atau XDG Notifications portal. **Mengirim notifikasi sendiri** ≠ **dapat menerima notifikasi semua aplikasi**. Dalam spesifikasi freedesktop, satu notification server memegang D-Bus service session; **jangan mengambil alih `org.freedesktop.Notifications` default**. Observasi lintas aplikasi memerlukan adapter/forwarding eksplisit yang diizinkan desktop/daemon dan diuji; bila tidak ada, tampilkan `System notification integration unavailable` dan biarkan OS notifikasi berjalan normal.
- **PWA/browser:** dukung notifikasi browser/installed PWA jika memang diteruskan sebagai desktop notifications dan jalur observasinya tersedia. Metadata asal PWA, judul situs, tab, URL, tombol aksi, dan identitas app **tidak selalu terekspos**: jangan menjanjikan semua browser/PWA dapat dipisah atau diluncurkan kembali secara otomatis.
- **Terminal:** dukung `notify-send` dan aplikasi CLI yang memicu desktop notifications; terminal emulator yang mendukung notification escape sequence (mis. OSC khusus) hanya jika terminal mengirim ke server desktop atau menyediakan adapter dokumentasi. **Tidak membaca output terminal atau shell history** demi mendeteksi event.
- **Mode kerja**: (a) Pippo-owned alerts selalu dapat muncul di hub; (b) desktop-native **observe/forward adapter** apabila kompatibel & user mengaktifkan; (c) external notification source unavailable → OS-only fallback. Tidak ada klaim universal `native/PWA/terminal full interception` di v1. Integrasi daemon khusus (SwayNC, mako, KDE Plasma) ditentukan *backend capability testing*, bukan asumsi API sama.
- Privacy default: tidak menyimpan konten notif pihak ketiga ke disk tanpa opt-in. Opt-in history jika tersedia memakai retention terpisah, per-app mute/allowlist, clear history; `secret`/lockscreen/private/Do-Not-Disturb dihormati semampu protokol. Izin dan aksesibilitas per aplikasi transparan, no hidden D-Bus eavesdropping or escalated permission.
- **AC:** Pippo mengirim notif miliknya dengan benar; jika adapter diaktifkan, native/PWA/`notify-send` fixture yang didukung muncul sekali pada hub (tanpa duplicate toast), sesuai source/capability; DND/fullscreen/mute dipatuhi; unsupported origin dilabeli `Browser` atau `Unknown` bukan identitas yang dibuat-buat; uninstall/exit Pippo tidak memutus notification server default; CPU/RAM idle tetap dalam budget.

### FR-16: Microsoft Teams / PWA Meeting Reminder (T−5 Menit) [P1 v1, source-gated]

**Keputusan owner OQ-27 (2026-11-17, A):** v1 mendukung sinkronisasi kalender Microsoft setelah user mengizinkan, plus input waktu/link meeting manual sebagai fallback lokal. Keputusan scope ini tidak membuktikan tenant sudah mengizinkan aplikasi atau autentikasi/sinkronisasi telah berhasil diuji.

- Pengguna dapat menyambungkan **Microsoft 365 calendar melalui Microsoft Graph delegated sign-in yang diotorisasi** dan scope baca minimum yang cukup untuk event + `joinUrl`, atau memasukkan **meeting link + start time** secara manual sebagai fallback lokal. **Tidak membaca kalender Teams PWA/browser/Teams-for-Linux lewat scraping, cookies, atau token yang diekstrak dari aplikasi lain.**
- Default alert **5 menit sebelum meeting**; konfigurasi offset menit/jam, toggle per calendar dan per meeting, dan opsi tambahan `at start` yang default OFF serta hanya aktif ketika user mengaktifkannya (OQ-28 A). Meeting time berasal dari event source dengan timezone/DST yang benar; tampilkan judul, waktu lokal, provider/source, dan aksi Join bila link valid.
- Sync menggunakan API resmi dan izin organisasi sesuai tenant policy; backoff, refresh token tersimpan di keyring, offline cached schedule tetap berlaku dengan indikator freshness. Recurring events/exceptions/reschedules/cancellations dideduplikasi dengan ID occurrence stabil; offline/clock-change/restart/resume tidak boleh menghasilkan spam.
- Target client: **Teams PWA (Edge/Chrome) dan Microsoft Teams for Web**; **teams-for-linux** komunitas (jika terpasang) adalah adapter terpisah. Notification daemon tidak diambil alih. Browser desktop notifications saja **tidak dijadikan sumber kalender yang andal**.
- **AC:** fixture meeting jam 10.00 di `Asia/Kuala_Lumpur` menghasilkan event jam 09.55 Malaysia dengan tampilan waktu setempat yang benar; recurring meeting exception dan cancellation diperbarui; pengaturan offset 5→15 menit efektif; user yang belum authorize diberi setup/deep-link manual, bukan notifikasi palsu; DND/meeting mute dan timezone ditaati; default hanya reminder T−5, reminder saat mulai tidak muncul sampai diaktifkan user, dan mematikannya kembali menghentikan alert tambahan tanpa menghapus T−5.

### FR-17: Join Meeting Action [P1 v1, link-gated]

**Keputusan owner OQ-28, bagian handler (2026-11-17, A):** default Join membuka browser default OS; user bisa mengganti aplikasi pembuka melalui pengaturan. Bagian reminder pada OQ-28 juga sudah dipilih A: tambahan reminder saat meeting mulai opsional dan default OFF, dapat diaktifkan user (FR-16).

- Notifikasi sebelum/ketika meeting punya tombol **Join Meeting** jika `joinUrl` tersedia dan tervalidasi; handler awal adalah browser default OS dan mengikuti perubahan pilihan user melalui pengaturan (Teams PWA atau `teams-for-linux` hanya bila mendukung URL/deep link tersebut). Aksi **membuka meeting join flow** dan **tidak otomatis menyetujui mikrofon/kamera atau masuk call**.
- Deep link `teams.microsoft.com` / scheme `msteams:` hanya dipakai bila telah diverifikasi sebagai URL yang aman dan dapat di-handle app terpilih; fallback ke tautan HTTPS atau **Open Calendar**, bukan silent failure. Jangan menjalankan string URL sebagai command shell.
- Perbedaan "Join" dan "Open Meeting" tetap transparan; bila URL tidak tersedia, tombol Join disembunyikan atau disabled dengan alasan jelas.
- **AC:** tanpa override, click Join membuka meeting yang benar pada browser default OS; perubahan handler melalui pengaturan dihormati; tidak memicu meeting lain; meeting tanpa link tidak menampilkan Join aktif; invalid/malicious URL diblok dan error surfaced; notifikasi sistem tetap normal.

### FR-18: Incoming Microsoft Teams Call + Accept/Decline [P1 v1 conditional adapter, P2 untuk PWA tanpa action API]

**Keputusan owner OQ-29 (2026-11-17, A untuk kedua bagian):** Accept/Decline masuk v1 hanya melalui adapter yang mendukung dan teruji; jika tidak tersedia, tampilkan Open Teams. Nama/nomor caller default disembunyikan, tampil setelah user mengaktifkan dan hanya jika datanya tersedia dari adapter. Keputusan ini tidak mengonfirmasi backend atau metadata caller telah teruji.

- **Target nyata v1:** integrasi **unofficial community `teams-for-linux`** melalui **opt-in local session D-Bus control API** jika versi terpasang mengekspos `IncomingCallChanged`, `IncomingCallCallerChanged`, dan method `AcceptAudio`, `AcceptVideo`, `DeclineCall`. Pippo hanya berkomunikasi dengan instance yang disetujui user; probe bus name, object path, method, permissions, dan reconnect. **D-Bus `true` berarti action dispatched, bukan jaminan Teams sudah menerima call**; observasi state untuk konfirmasi.
- UI saat ringing: default label generik **Panggilan masuk**; nama/nomor caller hanya ditampilkan setelah opt-in user dan tersedia dari adapter. Tanpa metadata, tetap gunakan label generik. Kontrol yang tersedia: tombol **Accept Audio**, **Accept Video**, **Decline**, **Open Teams**. **Tidak pernah auto-answer**; tindakan bersifat eksplisit, boleh dibatalkan sebelum dikirim. Setelah call terjawab/berakhir/timeout, banner ditutup dan tombol dinonaktifkan. Respons `rejected`, `unavailable`, dan timeout ditangani tanpa false success.
- Untuk **Teams PWA/Teams for Web biasa**, browser notification tidak memberikan API umum untuk "accept call" lintas proses. **Fallback: banner/open app only** bila sumber ringing dapat dipantau dengan sah; tombol langsung Accept/Decline **disembunyikan** jika tidak ada API tindakan. Microsoft Graph `call: answer` ditujukan untuk **bot/application calling**, bukan kontrol panggilan user Teams personal tanpa izin.
- **AC:** mock D-Bus + live opt-in compatible teams-for-linux menampilkan ringing sekali, `AcceptAudio` dan `AcceptVideo` invoke method yang tepat, dialog lain tidak terdampak; reconnect/instance mismatch tidak mengeksekusi aksi salah; pada PWA unsupported tampil hanya Open Teams; identitas caller tidak muncul secara default, hanya tampil jika opt-in dan metadata tersedia, serta kembali generik ketika opt-in dimatikan/metadata tidak ada; tidak meminta akses admin atau membaca browser secrets.

### FR-19: AI Agent Execution & Approval Notifications [P1 basic, P2 additional connectors]

**Keputusan owner OQ-30 (2026-11-17, A):** Codex CLI adalah integrasi agent pertama untuk MVP. Jalur event/hook/IPC, format payload dan kemampuan completion/approval masih perlu diverifikasi untuk versi CLI yang akan dipakai; pemilihan produk ini tidak membuktikan API/event tertentu tersedia. Connector agent lain tetap kandidat fase lanjutan, bukan kewajiban integrasi bersamaan.

- User bisa mendaftarkan **agent source** lokal/remote yang menyediakan event adapter jelas (misalnya user-configured CLI completion hook atau authenticated localhost IPC; provider-specific webhook hanya setelah security/design review). Jenis event minimum: `started` (opsional untuk UI), **`completed`**, **`failed`**, **`approval_requested`**, `cancelled`; status, agent name, task label dan time. Tidak men-scan proses/terminal output secara diam-diam.
- **Keputusan owner OQ-31 (2026-11-17, A):** notification untuk `completed` dan `approval_requested` dengan prioritas sesuai pengaturan, plus **Open Agent/Open Task** untuk kembali ke agent. Review, Approve dan Reject dilakukan di agent; v1 tidak menyediakan tombol atau mengirim request approval/rejection dari Pippo, walaupun suatu source memiliki action API. Membuka agent bukan tindakan approval; pending tetap pending sampai source mengonfirmasi perubahan status. Tidak ada auto-approval, termasuk saat DND, offline, atau timeout.
- Agent adapters melakukan autentikasi, validasi payload, dedupe `source_id + task_id + event_id`, pembatasan ukuran konten, redaction secrets, dan timeout. Local socket/private IPC lebih disukai untuk agent lokal; remote callback perlu mutual/authenticated origin dan bukan membuka listener publik default.
- **AC:** satu `completed` menghasilkan satu notif, `approval_requested` hanya menampilkan **Open Agent/Open Task** tanpa direct Approve/Reject atau request aksi approval, pending approval tetap pending sampai source mengonfirmasi; duplicate/out-of-order/expired event tidak memicu approval atau mengubah status secara keliru; secrets tidak tercatat di log/history default; nonaktifkan adapter menghentikan event tanpa mengganggu fitur lain.

### FR-20: Configurable, Repeatable Reminders [P1 MVP]

**Keputusan owner OQ-32, minimum dan catch-up (2026-11-17, A untuk kedua bagian):** interval minimum reminder berulang 1 menit. Setelah laptop bangun dari sleep/reboot atau aplikasi aktif kembali, tampilkan maksimal satu ringkasan per reminder yang terlewat, lalu lanjut jadwal normal. Snooze dipilih A: preset 5, 10 dan 15 menit plus durasi custom, default 10 menit. Quiet hours dipilih A: cukup mengikuti DND sistem, tanpa jadwal quiet hours tambahan di Pippo. Akhir reminder dipilih A: default terus berulang sampai dimatikan, user dapat memilih tanggal akhir atau jumlah pengulangan. Interval awal form dipilih A: kosong dan wajib diisi user, minimum 1 menit; form tidak boleh mengisi 1 atau 30 menit secara otomatis. Seluruh bagian OQ-32 sudah diputuskan, bukan laporan implementasi selesai.

- **Reminder center** untuk one-shot dan repeatable: title, description opsional, waktu mulai, timezone, **offset dalam menit/jam**, pilihan repeat **setiap N menit/jam** (N awal kosong, wajib diisi user, minimum 1 menit; OQ-32 A), daily, weekdays atau selected days; end date/occurrence count/never, dengan **never/terus sampai dimatikan sebagai default** (OQ-32 A); enable/pause/edit/delete. Reminder meeting dan salat boleh menggunakan scheduler yang sama namun mempertahankan policy dan owner masing-masing (jangan menyatukan semua business logic dalam god service).
- **Default meeting offset 5 menit**, default user reminder visual silent sesuai OQ-12. `Snooze` preset **5, 10 dan 15 menit**, plus **durasi custom** dalam menit/jam, default **10 menit** (OQ-32 A); dismiss satu occurrence, stop repeating, dan notification priority dapat diatur; validasi interval minimum 1 menit (OQ-32 A); nilai di bawahnya ditolak, mencegah rule tanpa batas yang menyebabkan event flood.
- Quiet hours (OQ-32 A): ikuti DND sistem sesuai capability desktop, tanpa scheduler/settings jam tenang khusus Pippo. Jika DND tidak dapat dibaca backend, jelaskan keterbatasan; jangan mengklaim DND terdeteksi atau mengubah setting OS. Waktu aktif/rule reminder yang dipilih user tetap terpisah dari quiet hours global.
- Persist lokal (SQLite), wake menggunakan **single next-due timer**/event-driven scheduler, bukan timer polling per reminder. Setelah reboot/suspend atau aplikasi aktif kembali, recalculate dan tampilkan maksimal satu ringkasan per reminder yang terlewat (OQ-32 A), kemudian lanjutkan jadwal normal. Tidak mereplay seluruh occurrence lama atau menampilkan ringkasan sama berulang pada resume/restart berikutnya. Wall-clock/timezone/DST dan DND tetap dihormati; queue/coalescing lintas reminder tetap bounded agar banyak reminder tidak membanjiri popup.
- **AC:** pada form interval berulang, nilai awal kosong dan wajib diisi sebelum disimpan; tidak ada default 1/30 menit otomatis; interval 1 menit dapat disimpan dan dijalankan, interval di bawah 1 menit ditolak dengan alasan; repeat 30 menit menghasilkan pengingat pada waktu tepat dalam toleransi OS; recurring daily & across DST tepat sesuai timezone configured; preset snooze 5/10/15 menit dan durasi custom bekerja, nilai default snooze 10 menit; pause/stop mencegah notif berikutnya; tanpa akhir eksplisit reminder tetap berulang sampai dimatikan, sedangkan tanggal akhir/jumlah pengulangan menghentikan pengulangan baru setelah batas tercapai; restart/resume memberi maksimal satu ringkasan per reminder yang terlewat dan melanjutkan jadwal normal tanpa replay/duplikasi pada resume berikutnya; ada tes coalescing burst dan 1000 jadwal tersimpan tanpa CPU idle tinggi; DND sistem dihormati pada backend yang mendukung, unavailable dilaporkan dengan jujur, tidak ada jadwal quiet hours khusus Pippo.

### FR-21: Remote Server / VM / Bare-Metal Health Monitor [P2 OPTIONAL, NOT MVP]

**Keputusan owner OQ-34 (2026-11-17, rekomendasi diterima):** detail server monitor ditunda ke P2 opsional sampai owner menyediakan contoh endpoint liveness/readiness dan autentikasi. Tidak membuat scaffold, adapter generik, dependency atau polling untuk P1. Seluruh proposal/acceptance di bawah bersifat bahan evaluasi P2, bukan kontrak API final atau gate v1.

- **Explicitly out of MVP/v1 scope**. Ide backlog: daftar host/server/VM/bare-metal yang user daftarkan manual dengan label dan API endpoint **liveness**/**readiness** yang **nanti diberikan owner**. Pippo adalah lightweight viewer; bukan server agent, Prometheus replacement, infrastructure discovery, atau deployment orchestrator.
- **Proposal tanpa mengunci skema:** polling `GET` authenticated HTTPS ke endpoint yang disepakati; mapping `alive/not-alive`, `ready/not-ready`, `unreachable`, `unknown` berdasar payload/HTTP semantics yang akan diputuskan, last checked, latency sederhana, consecutive failure threshold dan alert opsional. **Liveness ≠ readiness**: proses dapat hidup tetapi belum layak menerima traffic.
- Credentials pada OS keyring; TLS validation; timeout dan backoff; concurrency limit; rate limit; no polling default ketika tidak configured/enabled; tidak menyalin URL/token sensitif ke diagnostics. Jangan membangun adapter generic untuk semua API atau menyimpan arbitrary scripts sebelum kontrak API tersedia.
- **P2 acceptance (provisional, bukan release gate v1):** stub API liveness=200/readiness=503 menampilkan alive-but-not-ready; timeout status unreachable; alert dedupe; disabled berarti nol request; 100 endpoint tidak merusak responsiveness, batas interval dan resource ditentukan setelah proof of concept.

### FR-22: Caffeine / Keep Awake [P1 MVP]

**Keputusan owner OQ-33 (2026-11-17, rekomendasi diterima):** Caffeine default OFF; durasi default saat diaktifkan 30 menit, hanya display idle. Suspend inhibition terpisah dan hanya aktif jika user secara eksplisit mengaktifkan serta backend mendukung. Mode manual/indefinite memerlukan status aktif terlihat dan kontrol OFF; manual lock, kebijakan OS/admin dan critical battery tetap dihormati. Q&A ini tidak menetapkan threshold auto-stop baterai baru.

- Tombol **Caffeine** di quick controls/overflow + shortcut opsional; menahan **automatic idle display dimming/blanking/lock** sejauh yang diizinkan compositor/OS, dengan mode **on/off** dan durasi **15m, 30m, 1h, 2h, custom**; tampilkan status dan remaining time. Default OFF, durasi awal saat diaktifkan **30 menit**; user dapat memilih durasi/mode lain dan stop kapan saja; auto-expire wajib untuk timed mode. Mode indefinite harus mempunyai status aktif terlihat dan kontrol OFF. **Tidak menggerakkan kursor atau memalsukan activity/presence**.
- Prefer **XDG Desktop Portal `org.freedesktop.portal.Inhibit` untuk idle flag** atau D-Bus/system integration yang sesuai; fallback ke compositor-specific Wayland idle-inhibit **hanya bila efektif** (inhibitor terikat surface yang visible; floating pill yang hidden/occluded tidak menjamin efektif). Capability probe KDE Plasma Wayland dan Hyprland; bedakan idle/screen lock dari suspend inhibition. **Suspend inhibit** hanya jika secara eksplisit diaktifkan dan didukung, bukan default; lock manual, kebijakan perusahaan, critical battery, dan system shutdown tidak boleh dibypass.
- RAII owner untuk inhibitor handle: dilepas saat off/expiry/exit/crash-recovery semampu OS; UI tidak menampilkan active sebelum OS mengonfirmasi. Jika backend/desktop menolak, tampilkan unsupported/failure; no root, no autoconfig untuk idle daemon user.
- **AC:** saat awal Caffeine OFF, aktivasi default meminta display-idle inhibition selama 30 menit tanpa suspend inhibition; pada KDE Wayland dan Hyprland yang mendukung, 30m keep-awake mencegah idle screen timeout selama aktif, lalu behavior idle normal pulih ketika expire/off; manual lock tetap bisa; inhibit tidak mengubah Teams presence; kekurangan portal/compositor menghasilkan disabled/fallback state; Caffeine tidak memicu CPU poll busy-loop dan tetap aman setelah resume; mode indefinite memiliki status terlihat dan kontrol OFF; suspend inhibition tidak aktif tanpa pilihan eksplisit user.

## Referensi integrasi

Daftar berikut diwarisi dari PRD v0.5.2 sebagai bahan verifikasi. Keberadaan link tidak membuktikan API/izin atau kompatibilitas telah diuji. Method dan nama service adapter di kontrak fitur adalah target probe; verifikasi dokumentasi resmi serta versi terpasang sebelum implementasi. Catat hasilnya pada laporan validasi.

- Notchy (referensi pola UX): https://notchy.dev/id/
- Qt 6 Quick: https://doc.qt.io/qt-6/qtquick-index.html
- Layer Shell Qt: https://invent.kde.org/plasma/layer-shell-qt
- Wayland layer-shell / exclusive zone: https://wayland.app/protocols/wlr-layer-shell-unstable-v1
- MPRIS2: https://specifications.freedesktop.org/mpris/latest/
- PipeWire/WirePlumber `wpctl` dan per-app stream state: https://pipewire.pages.freedesktop.org/wireplumber/man/wpctl.html ; https://pipewire.pages.freedesktop.org/wireplumber/daemon/configuration/stream.html
- XDG GlobalShortcuts portal: https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.GlobalShortcuts.html
- Hyprland portal (termasuk shortcut capability): https://wiki.hypr.land/hypr-ecosystem/user/xdg-desktop-portal-hyprland/
- Desktop Notifications specification: https://specifications.freedesktop.org/notification/latest-single/
- KDE KNotification developer docs: https://develop.kde.org/docs/features/knotification/
- Microsoft Teams for Linux / PWA official client support: https://learn.microsoft.com/en-us/microsoftteams/teams-client-system-requirements
- Community **teams-for-linux** (unofficial) configuration and opt-in D-Bus calls control: https://github.com/IsmaelMartinez/teams-for-linux/blob/main/docs-site/docs/configuration.md ; https://github.com/IsmaelMartinez/teams-for-linux/blob/main/docs-site/docs/development/dbus-control.md
- Microsoft Graph calendarView (delegated permissions), Teams meeting join link: https://learn.microsoft.com/en-us/graph/api/calendar-list-calendarview?view=graph-rest-1.0
- Microsoft Graph `call: answer`: **bot-only API, not personal user-call control**: https://learn.microsoft.com/en-us/graph/api/call-answer?view=graph-rest-1.0
- XDG Desktop Portal Inhibit API: https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.Inhibit.html
- Wayland idle inhibit protocol (surface may need visible/non-occluded surface): https://wayland.app/protocols/idle-inhibit-unstable-v1
- Hypridle policy and D-Bus inhibitors: https://github.com/hyprwm/hypridle

### Data & compliance

- MoneyMatch Transfer: https://transfer.moneymatch.co/
- MoneyMatch Transfer Terms (kurs transaksi berubah dan quote dipengaruhi waktu/order): https://transfer.moneymatch.co/terms-and-conditions
- MoneyMatch official public site, API enterprise information **tidak sama dengan izin API publik untuk kurs konsumen**: https://moneymatch.co/enterprise

**Source governance note:** Referensi di atas untuk pengambilan keputusan produk. Tidak semua situs menyediakan API umum, izin scraping, data reuse, atau API pembayaran yang boleh digunakan aplikasi open-source. Sebelum ship, kontrak/ToS, source permission, data freshness, dan lisensi harus diverifikasi kembali.
