# Tech Stack Pippo

| Metadata | Nilai |
|---|---|
| Versi dokumen | 1.4 |
| Diperbarui | 2026-11-17 |
| Status | Rancangan dependency; belum ada manifest, build, lockfile atau CI |
| Target | C++20 + Qt 6 Quick/QML, Linux x86_64 KDE Plasma Wayland + Hyprland |

[Indeks](README.md) · [Arsitektur](ARCHITECTURE.md) · [Setup](SETUP.md) · [Keputusan](DECISIONS.md) · [Current state](CURRENT-STATE.md)

## 1. Kebijakan versi dan dependency

C++20 + Qt6/QML adalah constraint produk (OQ-03). CMake + Ninja adalah baseline engineering usulan. Library sistem mengikuti paket distro yang kompatibel, terutama Qt plugins, Qt Wayland dan Layer Shell Qt. Jangan mencampur Qt custom dengan plugin distro yang ABI-nya tidak sesuai, atau mengunduh dependency saat setiap build tanpa review.

| Komponen | Baseline rencana | Status verifikasi proyek |
|---|---|---|
| C++ | C++20 | Keputusan produk; compiler/build belum diuji. |
| GCC / Clang | Compiler distro yang mendukung C++20 | Versi minimum final TBD. Tidak harus memasang keduanya. |
| Qt 6 | Kandidat minimum 6.8 untuk contoh di bawah **(agent's call)** | OQ-37: contoh lama memuat 6.5 dan 6.8; belum ada versi supported. |
| CMake | Kandidat minimum 3.24 dari dokumen sebelumnya | Belum ada CMakeLists/CI untuk membuktikannya. |
| Ninja / pkg-config | Paket distro | Belum diuji; bukan runtime dependency pengguna. |
| Layer Shell Qt / Qt Wayland | Paket kompatibel dengan Qt distro | Overlay kedua compositor wajib diuji. |
| PipeWire / WirePlumber | Runtime audio distro; SDK ketika audio dimulai | Belum ada backend terpilih/teruji. |
| SQLite | Qt SQL dengan runtime driver QSQLITE | Schema/migration/driver discovery belum dibuat. |
| Portal + backend desktop | Versi paket target yang menyediakan capability terkait | Jangan menyatakan semua portal backend mendukung fitur sama. |

Dokumen lama menyebut snapshot “latest stable verified 16 November 2026” dengan versi upstream spesifik. Workspace tidak berisi rekaman verifikasinya. Klaim latest/verified tersebut dihapus; angka itu tidak menjadi pin atau supported baseline. Sebelum implementasi/rilis, pilih versi berdasarkan target distro dan catat hasil build/test, bukan menebak versi terbaru.

Patch/security update melewati review compatibility, CI dan compositor tests. Paket RPM/DEB/Arch dibangun sesuai target distro; satu binary Qt terbaru tidak otomatis kompatibel dengan semua distro. Versi development reference, minimum source compatibility dan artifact compatibility harus dicatat terpisah ketika ada bukti.

## 2. Modul dan dependency per kebutuhan

Hanya link komponen yang benar-benar dipakai target saat itu. Modul Qt mengikuti versi framework yang sama; jangan memberi pin berbeda pada Quick/QML/DBus.

| Area | Kandidat modul/backend | Mulai digunakan / batas |
|---|---|---|
| Lifecycle, data, date/locale | QtCore, QtGui | P0; Calendar lokal tidak membutuhkan provider/network. |
| UI | QtQuick, QtQml; QtQuick.Layouts | P0; QuickControls2 ketika diperlukan. |
| Overlay | Qt Wayland client/plugin + LayerShellQt | P0; bukan Qt Wayland Compositor. |
| MPRIS/system D-Bus | QtDBus | Media, battery, NetworkManager, BlueZ, notifications/portals. |
| HTTP/streaming/JSON | QtNetwork + QtCore JSON; validasi desimal untuk FX | Remote AI, configured FX GET (JSON mapping / raw number), authorized meeting source; tanpa HTTP/JSON/HTML library kedua. |
| Durable local data | QtSql + QSQLITE | Notes, clipboard, reminders; tanpa ORM. Preferences sederhana memakai QSettings/XDG. |
| Audio graph | libpipewire; WirePlumber runtime. Alternatif: libpulse lewat `pipewire-pulse` jika memenuhi seluruh kebutuhan FR-14 | FR-14; pilih satu backend pertama dan catat alasannya. Jangan otomatis tambah SDK libwireplumber jika backend pertama cukup. |
| Credentials | Secret Service/keyring; libsecret jika dipilih | AI/OAuth; tidak menyimpan plaintext fallback. |
| Shortcut / Caffeine | Portal/backend desktop yang teruji | Capability probe + fallback; API dispatch bukan bukti efek pada OS. |
| Test | Qt Test + CTest; Qt Quick Test untuk UI bila digunakan | Saat ada logic/perilaku yang perlu diuji. |
| i18n | QTranslator / Qt Linguist tools | Strings siap resource; English minimal, Indonesian conditional. |
| Lint/analysis | clang-format, clang-tidy, qmllint, qmlformat, sanitizer | Terapkan sesuai source/CI yang sudah ada; saat ini belum ada. |

Now Playing memakai MPRIS, tidak memerlukan Qt Multimedia hanya untuk remote control. Ask AI memakai network + QML, tidak memerlukan Qt WebEngine. Qt Wayland Compositor tidak diperlukan untuk aplikasi client ini. `playerctl`, `wpctl`, `notify-send`, `dbus-monitor`, `pw-dump`, profiler dan heap tools adalah alat debug/test opsional, bukan hard dependency produksi.

Metadata PipeWire tidak menjamin stream terpisah setiap tab PWA. Qt clipboard tidak membuktikan background capture Wayland tersedia. Portal atau protokol inhibitor harus diuji pada sesi target; notification publisher tidak berarti dapat mengamati notifikasi aplikasi lain. Lihat FR terkait untuk batas capability.

## 3. Contoh build tunggal

Contoh ini **ilustrasi untuk source yang belum ada**, bukan perintah menghasilkan aplikasi dari workspace saat ini. Layout mengikuti ARCHITECTURE. Qt 6.8 adalah kandidat editorial, bukan keputusan minimum supported; ubah setelah OQ-37 mempunyai bukti.

Root `CMakeLists.txt` yang direncanakan:

```cmake
cmake_minimum_required(VERSION 3.24)
project(Pippo LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

find_package(Qt6 6.8 REQUIRED COMPONENTS Core Gui Qml Quick DBus)
find_package(LayerShellQt REQUIRED)
qt_standard_project_setup(REQUIRES 6.8)

add_subdirectory(src/Pippo/App)
```

`src/Pippo/App/CMakeLists.txt` yang direncanakan:

```cmake
qt_add_executable(pippo main.cpp)

qt_add_qml_module(pippo
    URI Pippo.App
    QML_FILES Island.qml
)

target_link_libraries(pippo PRIVATE
    Qt6::Core Qt6::Gui Qt6::Qml Qt6::Quick Qt6::DBus
    LayerShellQt::Interface
)
```

Tambahkan source bootstrap/feature saat dibuat. Network/Sql/Test/QuickControls2 ditambahkan pada target pemakai, bukan semua modul sejak hari pertama. Contoh tidak menetapkan versi aplikasi; versi PRD/arsitektur berbeda dari versi produk. CMake package/target LayerShellQt dan kompatibilitas SDK distro tetap perlu probe saat P0.

Menurut dokumentasi Qt yang diperiksa melalui Context7 pada review ini, executable dapat menjadi backing target `qt_add_qml_module` tanpa plugin module terpisah. Pada kasus executable, source directory tidak wajib sama dengan URI; `src/Pippo/App` tetap konvensi proyek. `qt_standard_project_setup(REQUIRES ...)` mengaktifkan baseline policy Qt terkait dan perlu diselaraskan dengan versi yang dipilih. Sumber: [qt_add_qml_module](https://doc.qt.io/qt-6/qt-add-qml-module.html), [QML executable module](https://doc.qt.io/qt-6/qt6-port-to-qt-add-qml-module.html), [QTP0005](https://doc.qt.io/qt-6/qt-cmake-policy-qtp0005.html).

Tidak ada build yang dijalankan terhadap contoh ini. Verifikasi syntax dokumentasi tidak menggantikan configure/compile, QML lint, runtime plugin discovery atau compositor test.

### Catatan API dari dokumentasi resmi (Context7, 2026-11-17)

Dicek ulang lewat Context7 terhadap dokumentasi Qt 6.8, repository KDE layer-shell-qt, xdg-desktop-portal dan WirePlumber. Ini konfirmasi isi dokumentasi, bukan hasil compile atau uji compositor.

| Topik | Yang dikonfirmasi | Dampak ke rancangan |
|---|---|---|
| `qt_standard_project_setup(REQUIRES 6.8)` | Mengaktifkan policy Qt sampai 6.8, termasuk QTP0001 (resource prefix default QML), QTP0004 dan QTP0005. Qt 6.8 memberi warning jika QTP0004 tidak di-set. Harus dipanggil sebelum `qt_add_qml_module`. | Contoh di atas sudah sesuai. Jika minimum Qt diturunkan (OQ-37), sesuaikan `REQUIRES` dan cek ulang policy. |
| Memuat QML | `QQmlApplicationEngine::loadFromModule(uri, typeName)` tersedia sejak Qt 6.5. | `main.cpp` direncanakan memanggil `engine.loadFromModule("Pippo.App", "Island")`, bukan path `qrc:` yang ditulis manual. |
| Target CMake Layer Shell Qt | `find_package(LayerShellQt REQUIRED)` menyediakan target `LayerShellQt::Interface`. | Sama dengan contoh di atas. |
| Inisialisasi Layer Shell Qt | `LayerShellQt::Shell::useLayerShell()` deprecated sejak 6.6 dan tidak diperlukan sejak Qt 6.5. Surface diatur lewat `LayerShellQt::Window::get(QWindow*)`: `setLayer`, `setAnchors`, `setMargins`, `setExclusiveZone`, `setScope`. Tersedia juga attached property QML dari modul `org.kde.layershell`. | Jangan memanggil `useLayerShell()`. Konfigurasi layer/anchor/exclusive zone diletakkan pada bootstrap shell (C++) atau attached property, satu tempat saja. Perilaku keyboard focus tetap harus diuji per compositor. |
| Portal GlobalShortcuts | Alur `CreateSession` lalu `BindShortcuts` (shortcut ID, description, `preferred_trigger`). Hasil dikirim lewat signal `Response` pada objek Request, bukan nilai balik sinkron. Shortcut aktif lewat signal `Activated`/`Deactivated`; `ShortcutsChanged` memberi trigger yang benar-benar dipasang. | Cocok dengan OQ-26: shortcut dikirim tanpa trigger default, lalu trigger aktual dibaca dari `ShortcutsChanged`. Deteksi konflik tergantung backend portal dan tetap perlu diuji di KDE/Hyprland. |
| Portal Inhibit | Permission store portal membedakan flag `logout`, `switch`, `suspend` dan `idle`. | FR-22 hanya meminta `idle` secara default; `suspend` hanya jika user mengaktifkan (OQ-33). |
| Pindah stream audio | WirePlumber memindahkan stream jika key `target.object` pada metadata `default` diisi `node.name` atau `object.serial` (setting `linking.allow-moving-streams`, default `true`). Stream dengan `node.dont-move` tidak dapat dipindah. `target.node` deprecated. | Implementasi "move stream" FR-14 memakai metadata ini, bukan mengubah default sink. Stream yang tidak bisa dipindah ditampilkan sebagai unavailable. WirePlumber dapat mengingat target stream (lihat `stream.restore-target`); itu policy OS, bukan aturan routing permanen milik Pippo (OQ-25). Default dan perilaku restore perlu dicek pada versi terpasang. |

Sumber: [Qt 6.8 qt_add_qml_module porting](https://doc.qt.io/qt-6.8/qt6-port-to-qt-add-qml-module.html), [QTP0004](https://doc.qt.io/qt-6.8/qt-cmake-policy-qtp0004.html), [QQmlApplicationEngine](https://doc.qt.io/qt-6.8/qqmlapplicationengine.html), [layer-shell-qt README](https://github.com/kde/layer-shell-qt/blob/master/README.md), [GlobalShortcuts XML](https://github.com/flatpak/xdg-desktop-portal/blob/main/data/org.freedesktop.portal.GlobalShortcuts.xml), [WirePlumber linking policy](https://github.com/pipewire/wireplumber/blob/master/docs/rst/policies/linking.rst).

## 4. Performance, keamanan dan distribusi dependency

Gunakan async I/O, bounded image decode/cache, lazy panel, event subscriptions dan adaptive sampling. Thread affinity/ownership ada di ARCHITECTURE; budget angka canonical ada di PRD; test/performance/license gates ada di VALIDATION.

Credential di keyring, TLS verification dan payload/time limits wajib. No audio capture untuk fitur mixer. Endpoint MoneyMatch dipilih owner dan GET awal berhasil; kontrak/izin/rate limit belum tervalidasi. Mode angka polos membaca seluruh body, bukan HTML DOM/scraping meskipun MIME text/html. Source notification juga tidak otomatis berizin hanya karena dapat diakses. Calendar FR-13 offline dan tidak membutuhkan SQLite/provider holiday.

Lisensi kode Pippo yang dicatat adalah `MIT OR Apache-2.0`; dependency dan aset mempunyai lisensi sendiri. Periksa modul Qt/QML plugin/Layer Shell Qt yang benar-benar dipakai serta kewajiban distribusinya. Dynamic linking direkomendasikan untuk baseline distribusi; keputusan ini tidak menggantikan audit paket/NOTICE/asset. Belum ada LICENSE-MIT, LICENSE-APACHE, manifest atau hasil audit di workspace.

## 5. Menambah atau memperbarui dependency

Sebelum menambah dependency, catat fitur pemakai, alasan Qt/stdlib tidak cukup, minimum supported version beserta bukti, dampak runtime, lisensi, fallback dan pengujian. Jangan menambah Boost, logging/JSON/HTTP library, ORM, reflection/DI framework, event bus atau browser engine untuk helper yang sudah tersedia di stack.

Saat upgrade, periksa dokumentasi/release notes resmi untuk versi yang benar-benar dipakai, affected usages, compatibility dan advisories; kemudian build/test dan catat perubahan. Tidak ada jadwal upgrade/pin aktif sampai manifest/CI tersedia. Preset dan CI matrix hanya dibuat saat dipakai.

## 6. Referensi untuk verifikasi berikutnya

Selain referensi Qt build yang diperiksa di §3, tautan berikut adalah titik mulai verifikasi, bukan bukti kompatibilitas/versi/izin yang sudah selesai:

- [Qt Quick performance](https://doc.qt.io/qt-6/qtquick-performance.html), [Qt licensing](https://doc.qt.io/qt-6/licensing.html).
- [KDE Layer Shell Qt](https://invent.kde.org/plasma/layer-shell-qt), [layer-shell protocol](https://wayland.app/protocols/wlr-layer-shell-unstable-v1).
- [MPRIS](https://specifications.freedesktop.org/mpris/latest/), [Desktop Notifications](https://specifications.freedesktop.org/notification/latest-single/).
- [GlobalShortcuts portal](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.GlobalShortcuts.html), [Inhibit portal](https://flatpak.github.io/xdg-desktop-portal/docs/doc-org.freedesktop.portal.Inhibit.html).
- [PipeWire](https://docs.pipewire.org/), [WirePlumber](https://pipewire.pages.freedesktop.org/wireplumber/).
- [Fedora packages](https://packages.fedoraproject.org/), [Debian packages](https://packages.debian.org/), [Arch packages](https://archlinux.org/packages/).

Referensi provider/Teams/agent/data dan batas akses ada di FEATURES. Riwayat perubahan stack ada di CHANGELOG. Pemetaan dependency ke modul source yang benar-benar ada dicatat di [CODEMAP](CODEMAP.md).
