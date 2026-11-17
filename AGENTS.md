# Pippo agent entry point

1. Baca [indeks dokumentasi](docs/README.md) dan [current state](docs/CURRENT-STATE.md) sebelum menjawab status atau memulai pekerjaan.
2. Ikuti [panduan agent](docs/AGENT-GUIDE.md) untuk rute baca sesuai tugas dan pembaruan docs dalam perubahan yang sama.
3. PRD/FEATURES menjelaskan target; CURRENT-STATE mencatat implementasi dan bukti. Jangan menyatakan fitur sudah ada hanya karena tercantum pada desain.
4. Untuk onboarding/status, gunakan docs terlebih dahulu. Untuk memahami atau mengubah kode, baca [CODEMAP](docs/CODEMAP.md) dulu lalu buka hanya file yang disebut kartu modul; jangan memindai seluruh source. Untuk implementasi/diagnosis, periksa source dan test yang relevan serta catat hasilnya kembali di docs.
5. Perubahan yang menambah/memindah modul, public type, wiring, kontrak data/IPC/config atau perintah build/test wajib memperbarui CODEMAP pada perubahan yang sama.

Dokumentasi proyek berada di `docs/`. File ini hanya titik masuk instruksi agent; keputusan dan spesifikasi tidak diduplikasi di sini.

<!-- context7 -->
## Documentation lookup

Use the `ctx7` CLI to fetch current documentation whenever the user asks about a library, framework, SDK, API, CLI tool, or cloud service, including syntax, configuration, migration, porting, library-specific debugging, setup, and CLI usage. Prefer this over web search for library docs. Do not use it for refactoring/review/debugging independent of library/platform APIs or general programming concepts.

1. Resolve first: `npx ctx7@latest library <name> "<specific concept>"`. Use the official name and punctuation. Skip resolution only when the user supplied a valid `/org/project` ID.
2. Choose the best match by exact name, relevance, snippets, source reputation and benchmark score. Try alternate names/queries if needed.
3. Fetch: `npx ctx7@latest docs <libraryId> "<specific concept>"`. Separate distinct concepts unless their interaction is the question; use a version-specific ID from the library output when needed.
4. Answer from the fetched documentation. Run no more than three commands per question; do not put secrets in queries.

Run Context7 CLI requests outside the default sandbox. If a request fails with DNS/network errors, rerun it outside the sandbox. On a quota error, tell the user and suggest `npx ctx7@latest login` or `CONTEXT7_API_KEY`; do not silently fall back to training data.
<!-- context7 -->
