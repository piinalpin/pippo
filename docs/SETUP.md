# Setup Development Pippo

| Metadata | Nilai |
|---|---|
| Versi dokumen | 1.2 |
| Diperbarui | 2026-11-17 |
| Status | Panduan persiapan; source dan build belum tersedia |

[Indeks](README.md) · [Current state](CURRENT-STATE.md) · [Tech stack](TECH-STACK.md) · [Validasi](VALIDATION.md)

Perintah instalasi di bawah dipindahkan dari indeks lama dan **belum dijalankan atau diverifikasi per distro dalam review ini**. Perlakukan nama paket sebagai kandidat; periksa repository distro dan kompatibilitas versi sebelum memasang. Review ini tidak memasang dependency atau mengubah konfigurasi host. Satu-satunya bagian API yang diperiksa ulang adalah konvensi QML/CMake pada TECH-STACK.

## Prerequisites

Panduan ini untuk **mengembangkan Pippo dari source**, bukan daftar dependency minimal pengguna akhir. Pippo memakai **C++20, Qt 6 / Qt Quick / QML, CMake + Ninja** dan menargetkan **Linux x86_64 dengan sesi KDE Plasma Wayland atau Hyprland**. Qt **6.8 diterima owner sebagai kandidat untuk M0.1**; ini belum minimum supported final. OQ-37 menunggu hasil uji compatibility dan matriks distro/compositor/portal.

### 1. Yang wajib tersedia untuk P0

| Kebutuhan | Kegunaan |
|---|---|
| GCC dengan dukungan C++20 atau Clang | Compile kode native C++20 |
| CMake >= 3.24, Ninja, `pkg-config`, Git | Configure, build, dependency discovery |
| Qt6 Base + development headers | `QtCore`, `QtGui`, `QtDBus`; paket yang sama juga memuat `QtNetwork`, `QtSql`, `QtTest`, yang baru di-link saat fitur pemakainya dibuat (TECH-STACK §2) |
| Qt6 Declarative / Quick / QML + development tools | QML UI, Qt Quick, animation, `qt_add_qml_module`, lint |
| Qt6 Wayland client/platform plugin | Menjalankan Qt Quick di sesi Wayland |
| KDE Layer Shell Qt (library **dan development headers**) | Membuat floating top-center surface pada compositor dengan `wlr-layer-shell` |
| KDE Plasma Wayland **atau** Hyprland | Menguji perilaku island sungguhan; **tidak perlu memasang keduanya** |

> **Penting:** Qt6 terpasang saja belum cukup untuk overlay. Layer Shell Qt perlu tersedia pada **build** dan **runtime**; support fullscreen, keyboard focus, dan multi-monitor tetap harus diuji per compositor. Jangan menginstal seluruh KDE Plasma hanya agar aplikasi dapat berjalan di Hyprland.

### 2. Instalasi per distribusi

Jalankan **salah satu** blok berikut sesuai distro yang digunakan. Nama paket dapat berbeda pada versi distro atau repository tertentu. Perintah di bawah ditujukan untuk paket distro, bukan Qt Online Installer.

**Fedora (RPM):**

```bash
sudo dnf install -y \
  gcc-c++ cmake ninja-build pkgconf-pkg-config git \
  qt6-qtbase-devel qt6-qtdeclarative-devel qt6-qtwayland-devel \
  layer-shell-qt-devel
```

**Debian 13 / Ubuntu yang menyediakan paket Qt6 dan Layer Shell Qt:**

```bash
sudo apt update
sudo apt install -y \
  build-essential cmake ninja-build pkg-config git \
  qt6-base-dev qt6-declarative-dev qt6-wayland-dev \
  liblayershellqtinterface-dev libqt6sql6-sqlite
```

Nama `liblayershellqtinterface-dev` diwarisi dari panduan Debian 13 (Trixie), tetapi ketersediaannya belum diuji dalam review ini dan **tidak dijamin pada semua versi Ubuntu/derivatif**. Sebelum install, cek `apt-cache policy liblayershellqtinterface-dev`; jika tidak tersedia, gunakan paket yang sesuai dengan versi distro atau build Layer Shell Qt dari source sesuai dokumentasi upstream. Hindari mencampur versi Qt dan library KDE dari repository yang tidak kompatibel.

**Arch Linux / distro berbasis Arch (pacman):**

```bash
sudo pacman -S --needed \
  base-devel cmake ninja pkgconf git \
  qt6-base qt6-declarative qt6-wayland layer-shell-qt
```

Gunakan repository resmi distro terlebih dahulu. Paket Qt/Shaders/tools tambahan baru diinstal saat kode fitur benar-benar membutuhkannya.

### 3. Dependency tambahan berdasarkan fitur (tidak wajib untuk shell P0)

| Fitur | Dependency / layanan | Kapan diperlukan |
|---|---|---|
| Advanced Audio Mixer | PipeWire + WirePlumber runtime; development library `libpipewire` dan mungkin `libwireplumber` | Ketika implementasi volume/mute/output per playback stream aktif dimulai (FR-14, OQ-25 A) |
| Secrets / API keys | Secret Service/keyring (opsi: `libsecret` development library) | Ketika menyimpan kredensial Ask AI secara aman |
| Now Playing | D-Bus session + MPRIS player | Runtime untuk menguji player; `playerctl` opsional untuk debugging |
| Wi-Fi / Bluetooth / brightness | NetworkManager, BlueZ, layanan backlight yang tersedia | Runtime; cek capability dan permissions desktop, bukan assume seluruh mesin mendukung |
| Clipboard / shortcut global / notification hub | Wayland compositor capabilities + XDG Desktop Portals/backend yang cocok | Integration testing; tidak semua compositor/portal mengizinkan observasi atau kontrol yang sama |
| Database SQLite | Qt SQL driver `QSQLITE` | Saat notes, metadata clipboard dan reminders diuji (Debian: `libqt6sql6-sqlite`). Preferences memakai QSettings/XDG, bukan SQLite (ARCHITECTURE §7). |

Contoh library **opsional** saat mengerjakan Audio Mixer atau keyring (bukan install wajib saat mulai P0):

```bash
# Fedora
sudo dnf install pipewire-devel wireplumber-devel libsecret-devel

# Debian/Ubuntu (jika tersedia pada distro)
sudo apt install libpipewire-0.3-dev libwireplumber-0.5-dev libsecret-1-dev

# Arch
sudo pacman -S --needed libpipewire libwireplumber libsecret
```

Jangan mengganti konfigurasi audio system-wide atau notification daemon yang sudah ada hanya untuk mencoba Pippo. Uji pada sesi desktop milik user.

### 4. Verifikasi environment

```bash
# Toolchain
c++ --version
cmake --version
ninja --version
pkg-config --modversion Qt6Core Qt6Quick

# Harus 'wayland' ketika menguji floating island
printf 'Session: %s\n' "${XDG_SESSION_TYPE:-unknown}"
printf 'Desktop: %s\n' "${XDG_CURRENT_DESKTOP:-unknown}"

# Audio backend (opsional, untuk fitur mixer)
command -v wpctl && wpctl status
```

**Checklist sebelum coding (belum dijalankan):** toolchain berhasil terdeteksi, Qt yang terpasang memenuhi versi minimum, library `LayerShellQt` ditemukan CMake, dan ada sesi **KDE Plasma Wayland atau Hyprland** yang berjalan. `XDG_SESSION_TYPE=wayland` saja tidak menjamin compositor mendukung Layer Shell.

### 5. Build, test, dan debugging

Setelah source aplikasi dan root `CMakeLists.txt` tersedia di repository:

```bash
cmake -S . -B build -G Ninja -DCMAKE_BUILD_TYPE=Debug
cmake --build build --parallel
ctest --test-dir build --output-on-failure
```

- Jalankan binary dari dalam sesi Wayland yang sesuai; jangan memakai `sudo` untuk menjalankan UI Pippo.
- Gunakan `clang-format`, `clang-tidy`, `qmllint` dan `qmlformat` **jika telah tersedia**. Instal saat lint/CI mulai diterapkan, tidak perlu menjadi blocker eksperimen shell pertama.
- Lakukan benchmark terpisah di KDE dan Hyprland (baseline **8 GiB RAM, iGPU, 1080p/60 Hz**) sesuai PRD. Build `Debug` untuk development, lalu gunakan `Release` untuk pengukuran performa yang representatif.
- Jangan menjalankan perintah build di atas sebelum repository memiliki source dan `CMakeLists.txt`: folder `docs/` berisi spesifikasi, bukan aplikasi executable.

**Referensi paket dan tooling:** [Qt/CMake QML](https://doc.qt.io/qt-6/cmake-build-qml-application.html) · [Layer Shell Qt (KDE)](https://invent.kde.org/plasma/layer-shell-qt) · [Fedora packages](https://packages.fedoraproject.org/) · [Debian packages](https://packages.debian.org/) · [Arch packages](https://archlinux.org/packages/).

## Troubleshooting awal

- Configure/build belum dapat dijalankan di workspace ini karena root CMakeLists dan source belum ada. Ini status proyek, bukan bukti toolchain rusak.
- Qt ditemukan tetapi overlay gagal: periksa sesi Wayland, compositor, Layer Shell Qt dan kecocokan plugin/ABI; Qt terpasang saja tidak cukup.
- Fitur clipboard/shortcut/notification/Caffeine unavailable: periksa capability backend sesi yang diuji, bukan menambah permission root atau mengganti notification daemon.
- Laporkan masalah dengan distro, versi Qt/compositor/portal, langkah reproduksi dan error yang sudah diredaksi. Jangan menyertakan API key, isi clipboard, prompt atau token.

Panduan menjalankan binary, lokasi config/database yang konkret, migration dan troubleshooting runtime ditambahkan setelah perilaku tersebut diimplementasikan dan diuji; perintah build/test dan kontrak yang benar-benar ada dicatat di [CODEMAP](CODEMAP.md); belum ada command `pippo`, opsi CLI, D-Bus API Pippo atau skema config stabil yang dapat dipakai sekarang.
