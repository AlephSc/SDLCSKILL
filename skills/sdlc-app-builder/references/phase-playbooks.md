# Phase Playbooks — panduan detail per siklus

Ketika memasuki sebuah siklus, kerjakan sesuai playbook di bawah. Setiap siklus
berakhir dengan artefak + gate. Jangan mulai siklus berikut sebelum gate lolos.

---

## Siklus 1 — Planning

**Pertanyaan wajib (via tool tanya-jawab, pilihan konkret, bukan pertanyaan terbuka):**

1. Kebutuhan user:
   - Masalah apa yang diselesaikan app ini?
   - Siapa pemakainya (peran, jumlah, tingkat keahlian)?
   - Fitur inti apa yang tidak boleh hilang?
2. MVP produk:
   - Fitur minimum rilis pertama apa saja?
   - Apa yang TIDAK masuk MVP (disimpan ke rilis berikutnya)?
   - Apa definisi "MVP selesai" (kriteria diterima user)?
3. Resource:
   - Budget/hosting apa yang tersedia (gratis, VPS, managed, store account)?
   - Bahasa/framework yang dikuasai tim/user?
   - Berapa waktu yang tersedia? Ada infrastruktur/devops yang sudah berjalan?
   - Lisensi berbayar yang boleh dipakai?
4. Jenis app: web / desktop / mobile / iOS (kalau mobile: Android saja, iOS saja,
   atau cross-platform?).

**Anti-pattern yang dilarang:** mengasumsikan jawaban; pertanyaan menggantung
("kamu mau app seperti apa?"); bertanya dua kali hal yang sama di siklus lain.

**Artefak:** `docs/vision.md` — pakai `templates/vision.md`.
**Gate:** user mengonfirmasi isi vision secara eksplisit.

---

## Siklus 2 — Analysis

Untuk setiap pilihan utama user di Planning (stack, hosting, jenis app, cakupan MVP):

1. Prediksi tujuan app 6–24 bulan ke depan: fitur apa yang paling mungkin ditambah,
   beban user bagaimana berkembang.
2. Dampak jangka panjang: biaya migrasi kalau pilihan ternyata salah, vendor lock-in,
   utang teknis, biaya operasional bulanan.
3. Titik scaling: di angka mana (user, data, request) arsitektur awal akan tertekan,
   dan apakah pilihan sekarang TIDAK menghalangi scale itu.

**Artefak:** `docs/impact-analysis.md` — pakai `templates/impact-analysis.md`.
**Gate:** tiap pilihan utama punya catatan dampak + rekomendasi, dibaca ulang user.

---

## Siklus 3 — Design

### 3a. UI/UX (pelajari dulu, jangan insting)
- Tetapkan design tokens: palet warna (kontras AA minimum), tipografi (skala jenis
  huruf & ukuran), spacing scale, radius, elevation.
- Pilih komponen dengan alasan, bukan default:
  - Button: kapan primary/secondary/ghost/toggle; satu primary per layar.
  - Input: slider vs stepper vs dropdown vs text — berdasarkan rentang nilai & presisi.
  - Navigasi: sidebar vs tab vs bottom-nav — berdasarkan jumlah & kedalaman menu.
- Kriteria kenyamanan: hierarki visual jelas, feedback state (hover/active/disabled/
  loading), responsive/mobile-friendly, tidak ada kombinasi warna yang menyakitkan mata.
- Bila tersedia, muat skill UI/UX (mis. `ui-ux-pro-max`) untuk pattern & palet.

### 3b. System design
- Petakan kata kerja domain ke arsitektur: CRUD → monolith modular; real-time →
  event-driven/websocket; analitik berat → antrian/worker; dsb.
- Wajib scalable: identifikasi bottleneck diperkirakan, batas antar modul jelas,
  data model normalkan secukupnya + strategi caching awal.
- Definisikan kontrak antarmuka awal antar modul (dijelaskan penuh di
  `templates/contracts.md`, dipakai Siklus 4).

### 3c. Security design
- Daftar ancaman minimum yang didesain sejak awal: XSS (escaping/ sanitization),
  SQL Injection (parameterized query/ORM), CSRF (token/same-site), IDOR (cek
  otorisasi per objek), eksposur directory & endpoint admin.
- **Aturan auth:** framework menyediakan auth bawaan (NextAuth, Supabase Auth,
  Laravel Breeze, dsb.)?
  - User ingin sistem login/register sendiri → auth bawaan WAJIB dimatikan/diskip
    supaya tidak bentrok (session ganda, dua tabel user, dua alur reset password).
  - Preferensi belum jelas → TANYAKAN di siklus ini. Jangan asumsikan.
- Kalkulasi harga/nilai uang: didesain agar SELALU dieksekusi di backend.

### 3d. Rencana hardening (wajib terjadwal)
Tulis 3 task eksplisit di architecture.md, dieksekusi setelah implementasi inti:
- Pass 1 — keamanan: audit input, otorisasi, secrets, header keamanan.
- Pass 2 — performa/resource: profil query, memori, bundle size.
- Pass 3 — simplify: hapus duplikasi, sederhanakan alur (skill `simplify-code`).

**Artefak:** `docs/ui-design.md` + `docs/architecture.md`.
**Gate:** tidak ada "TBD", keputusan auth terjawab, hardening terjadwal.

---

## Siklus 4 — Implementation

1. **Dependency-first:** kumpulkan semua dependency utama + SDK + toolchain, mulai
   unduh di background (terminal background + notifikasi selesai). Selama menunggu,
   tulis kode. Dilarang urutan sebaliknya.
2. **Parallel terkoordinasi:** tulis `docs/contracts.md` SEBELUM spawn sub-agent.
   Setiap task sub-agent menyertakan: kontrak yang relevan (skema data, signature,
   event), bagian miliknya (path modul), bagian yang dilarang disentuh, dan ringkasan
   tugas sub-agent tetangga. Verifikasi hasil: tiap modul menyambung lewat kontrak.
3. **Workflow CI sejak commit pertama** — lihat `cicd-rules.md`.
4. **Kode efisien** — lihat `efficiency-rules.md`.

**Gate:** lockfile terkunci, modul kontrak selesai sesuai pemiliknya, CI hijau,
review lolos.

---

## Siklus 5 — Testing

Urutan kaku:

1. **Debugging menyeluruh** — seluruh bagian kode dites satu per satu, mulai dari
   kode revenue (toko: produk, pembayaran, stok, keamanan barang; kalkulasi harga
   WAJIB backend — frontend-only pricing = gagal otomatis). Baru modul non-revenue.
2. **Pentesting** — eksposur directory (.env, .git, upload, admin, backup TIDAK
   boleh diakses dari luar), OWASP praktis, kode boros resource (memory leak,
   koneksi bocor, query tanpa index, algoritma salah tulis).
3. **Release readiness** — build dari clean checkout, rollback DIUJI di staging,
   CD bertahap staging → production.

**Gate:** temuan kritis closed, laporan tersimpan, rollback pernah diuji.

---

## Siklus 6 — Maintenance

Per item maintenance (sumber: monitoring, laporan bug, degradasi performa, usia
dependency), jalankan mini-SDLC:

1. **Analisa:** root cause, dampak kalau dibiarkan, dampak kalau diperbaiki.
2. **Plan:** langkah, urutan, risiko, kriteria selesai.
3. **Desain:** perubahan arsitektur/antarmuka bila perlu (update kontrak bila menyentuh
   batas modul).
4. **Eksekusi** lewat branch kecil + PR + CI yang sama.

**Gate:** tidak ada eksekusi tanpa dokumen analisa-plan-desain di `docs/maintenance.md`.
