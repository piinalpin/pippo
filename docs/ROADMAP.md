# Roadmap Pippo

| Metadata | Nilai |
|---|---|
| Versi dokumen | 1.0 |
| Diperbarui | 2026-11-20 |
| Status | planned; urutan milestone adalah (agent's call), belum disetujui owner |
| Tanggal / owner | TBD (OQ-39) |

[Indeks](README.md) · [Current state](CURRENT-STATE.md) · [PRD](PRD.md) · [Fitur](FEATURES.md) · [Validasi](VALIDATION.md)

## 1. Fungsi dokumen

Roadmap ini memecah fase PRD §8 menjadi milestone kerja yang berurutan. Isinya adalah urutan, dependency antar-milestone, blocker keputusan dan exit check per milestone.

Hal berikut bukan milik roadmap:

- Scope, prioritas dan exit criteria fase dimiliki [PRD](PRD.md) §6 dan §8.
- Kontrak perilaku dan acceptance criteria dimiliki [FEATURES](FEATURES.md).
- Gate dan definition of done dimiliki [VALIDATION](VALIDATION.md).
- Progres dan bukti dimiliki [CURRENT-STATE](CURRENT-STATE.md). Milestone di sini tidak diberi tanda selesai.
- Lokasi kode dimiliki [CODEMAP](CODEMAP.md).

Tidak ada tanggal karena kapasitas, budget dan owner masih TBD (OQ-39). Jangan menambahkan estimasi waktu sampai owner menetapkannya.

## 2. Ringkasan urutan

```text
P0  M0.1 Fondasi repo
     └─ M0.2 Shell minimal ─┬─ M0.3 Benchmark baseline   (butuh OQ-38)
                            └─ M0.4 Spike risiko         (paralel per spike)
        ── Gate P0 (PRD §8) ──
P1  M1.1 Layanan inti
     ├─ M1.2 Sistem & audio
     ├─ M1.3 Data lokal         (butuh OQ-36)
     │    └─ M1.4 AI remote
     ├─ M1.5 Reminder & Caffeine
     └─ M1.6 Notifikasi & agent
        ── Gate MVP (PRD §8) ──
    M2.1 Salat, Calendar, brightness   (butuh OQ-11)
    M2.2 FX gated
    M2.3 Meeting & call gated
    M2.4 Adapter notifikasi eksternal
    M2.5 A11y, i18n, multi-monitor     (OQ-17)
    M2.6 Packaging & kesiapan rilis    (butuh OQ-37, OQ-39)
        ── Gate v1 (VALIDATION DoD) ──
P2  Backlog: proposal terpisah per item
```

M2.1 sampai M2.5 dapat berjalan paralel setelah gate MVP. M2.6 berjalan terakhir karena butuh hasil semua milestone v1.

## 3. P0: foundation / feasibility

Tujuan P0 adalah bukti, bukan fitur siap pakai. Hasil spike yang mengubah desain dicatat di DECISIONS atau ADR sebelum P1 dimulai.

### M0.1 Fondasi repo

- **Isi:** CMake + Ninja sesuai contoh [TECH-STACK](TECH-STACK.md), target executable QML minimal, target test kosong, berkas `LICENSE-MIT` dan `LICENSE-APACHE` (OQ-14), CI yang build dan menjalankan test.
- **Bergantung pada:** kandidat minimum Qt untuk lingkungan uji. Kandidat 6.8 boleh dipakai untuk mulai; minimum final tetap OQ-37.
- **Exit check:** build dan test kosong lulus di CI; [CODEMAP](CODEMAP.md) §4 dan §5 terisi perintah yang benar-benar berjalan.

### M0.2 Shell minimal

- **Isi:** bootstrap, overlay Layer Shell top-center, state collapsed/peek/expanded, input/focus/ESC/click-outside, fullscreen hide default, pilot FR-03 Now Playing (MPRIS) dan FR-01 CPU/RAM.
- **Bergantung pada:** M0.1.
- **Exit check:** shell berjalan di KDE Plasma Wayland dan Hyprland; panel sistem tidak bergeser; fokus tidak direbut. Bukti per desktop dicatat terpisah. Kartu modul pertama ada di CODEMAP.

### M0.3 Benchmark baseline

- **Isi:** harness pengukuran Q-1 sampai Q-3 pada hardware baseline OQ-16, Release build.
- **Bergantung pada:** M0.2 dan protokol OQ-38 (warm-up, jumlah sampel, toleransi).
- **Exit check:** laporan benchmark per desktop dengan environment lengkap. Hasil di bawah target dicatat sebagai temuan, bukan disembunyikan.

### M0.4 Spike risiko

Setiap spike menghasilkan baris capability matrix (didukung / tidak / dengan syarat, per desktop) dan keputusan teknis. Spike tidak menghasilkan adapter produksi untuk API yang belum diizinkan. Spike saling independen dan bisa dikerjakan paralel setelah M0.2.

| Spike | FR | Pertanyaan yang dijawab |
|---|---|---|
| Clipboard Wayland | FR-04 | Backend baca/tulis text dan gambar di kedua compositor tanpa mencuri fokus. |
| Audio per stream | FR-14 | Volume/mute/pindah output stream aktif lewat WirePlumber `target.object`; efek `stream.restore-target`. |
| Shortcut | FR-14, OQ-26 | Portal GlobalShortcuts dan fallback per desktop; deteksi konflik yang mungkin. |
| Observasi notifikasi | FR-15, OQ-24 | Jalur observasi yang didukung tanpa takeover daemon dan tanpa popup ganda. |
| Scheduler + inhibitor | FR-20, FR-22 | Timer tahan suspend/resume; portal Inhibit dan pelepasan saat expiry. |
| Calendar offline | FR-13 | Month grid QDate/QCalendar dan locale tanpa jaringan. |
| Jalur FX | FR-12, OQ-21 | Mapping JSON / angka polos, MIME text/html, izin akses dan rate limit endpoint. |
| Microsoft calendar | FR-16, OQ-27 | Auth delegated dan izin tenant untuk sinkronisasi kalender. |
| Teams action | FR-18, OQ-29 | Adapter yang benar-benar mendukung Accept/Decline; metadata caller. |
| Event Codex CLI | FR-19, OQ-30 | Jalur event completion/approval dan handoff Open Agent. |

**Gate P0** sesuai PRD §8: bukti kedua desktop, benchmark, capability matrix dan keputusan teknis berdasarkan hasil.

## 4. P1: MVP usable

Urutan mengikuti dependency teknis. Milestone setelah M1.1 dapat paralel kecuali disebut lain.

| Milestone | Isi | Bergantung pada | Exit check |
|---|---|---|---|
| M1.1 Layanan inti | Storage SQLite + migration, QSettings, keyring, single instance, FR-11 alerts/recovery, FR-10 preferences dan menu | Gate P0 | Data bertahan setelah restart; migration teruji; secret tidak masuk log (Q-5). |
| M1.2 Sistem & audio | FR-02 battery, FR-05 controls tanpa brightness, FR-14 audio mixer | M1.1; spike audio dan shortcut | Stream A berubah tanpa mengubah B; shortcut kosong secara default; unavailable diberi alasan. |
| M1.3 Data lokal | FR-04 clipboard, FR-06 notes | M1.1; spike clipboard; keputusan OQ-36 | Retention dan quota ditegakkan tanpa kehilangan diam-diam; konten bertahan setelah restart. |
| M1.4 AI remote | FR-08 OpenAI, Anthropic, custom-compatible | M1.1 (keyring); M1.3 untuk konteks clipboard/notes | Submit eksplisit, stop, error 401/429 teruji; tidak ada pengiriman otomatis. |
| M1.5 Reminder & Caffeine | FR-20, FR-22 | M1.1; spike scheduler + inhibitor | Kontrak OQ-32 dan OQ-33 lulus di kedua desktop. |
| M1.6 Notifikasi & agent | FR-15 jalur first-party, FR-19 adapter Codex CLI | M1.1; spike notifikasi dan Codex CLI | Notifikasi + Open Agent tanpa Approve/Reject; tidak ada popup ganda. |

**Gate MVP** sesuai PRD §8: workflow dan acceptance criteria scope MVP stabil di kedua target, integrasi unsupported diberi alasan, tidak ada popup ganda. Ulangi benchmark M0.3 untuk melihat regresi.

## 5. P1: v1 completion

| Milestone | Isi | Bergantung pada | Exit check |
|---|---|---|---|
| M2.1 Salat, Calendar, brightness | FR-07, FR-13, brightness internal FR-05 | Gate MVP; OQ-11 (metode terverifikasi atau pilihan eksplisit) | Salat offline tervalidasi; Calendar tanpa holiday provider. |
| M2.2 FX gated | FR-12 | Gate MVP; spike FX | Test Endpoint, sumber dan fetched-at tampil; fallback unavailable tanpa kurs palsu. |
| M2.3 Meeting & call | FR-16, FR-17, FR-18 | Gate MVP; spike Microsoft calendar dan Teams action | Sinkronisasi berizin + fallback manual; T−5 dan Join lewat browser default; call action hanya pada adapter teruji. |
| M2.4 Adapter notifikasi eksternal | FR-15 adapter v1 | M1.6; spike notifikasi | Hanya sumber yang diaktifkan user; cakupan teruji per desktop. |
| M2.5 A11y, i18n, multi-monitor | Q-6, OQ-17 | Gate MVP | Keyboard, reduced motion, HiDPI dan multi-monitor diuji; English minimal. |
| M2.6 Packaging & kesiapan rilis | RPM/DEB/Arch, license audit, soak Q-4, dokumen penggunaan/privacy/troubleshooting | M2.1 sampai M2.5; OQ-37; OQ-39 | Install/upgrade/uninstall dari release files lulus pada kombinasi yang diklaim. |

**Gate v1** adalah [VALIDATION](VALIDATION.md) definition of done, atau exception yang disetujui owner dan dicatat di CURRENT-STATE.

## 6. P2: backlog

FR-09 SSO, FR-21 server monitor dan kandidat PRD §6 tidak mempunyai milestone. Masing-masing butuh proposal, kontrak API dan persetujuan scope sebelum masuk roadmap (OQ-15, OQ-34). P2 tidak menahan v1.

## 7. Blocker keputusan

| OQ | Memblokir | Kapan harus diputus |
|---|---|---|
| OQ-38 protokol benchmark | M0.3, klaim lulus Q-1 sampai Q-4 | Sebelum M0.3 |
| OQ-36 pinned vs quota clipboard | M1.3 | Sebelum M1.3 |
| OQ-11 metode salat | M2.1 bagian salat | Sebelum M2.1 |
| OQ-17 Bahasa Indonesia | M2.5 bagian terjemahan | Sebelum M2.5 |
| OQ-37 minimum Qt dan matriks distro | M2.6, klaim compatibility | Kandidat sebelum M0.1, final sebelum M2.6 |
| OQ-39 owner, beta, rollback | Jadwal dan M2.6 | Sebelum jadwal ditetapkan |
| OQ-35 bukti masalah dan metrik G-1 | Evaluasi G-1 saat beta, bukan pekerjaan teknis | Sebelum beta |

Spike P0 tidak menunggu OQ-35 atau OQ-39.

## 8. Memelihara roadmap

- Ubah roadmap hanya bila urutan, isi atau dependency milestone berubah. Progres harian masuk CURRENT-STATE.
- Jika spike mengubah urutan (misalnya backend clipboard tidak layak di satu desktop), perbarui milestone terkait dan catat alasannya di DECISIONS.
- Milestone yang selesai tetap di roadmap; status dan buktinya ada di CURRENT-STATE.
- Bump versi dan catat di CHANGELOG pada perubahan yang sama.
