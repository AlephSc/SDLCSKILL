---
name: sdlc-app-builder
description: Guide mature app builds through full SDLC phases.
version: 0.1.0
author: AlephSc, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [sdlc, cicd, app-builder, production-ready, quality-gates]
---

# SDLC App Builder Skill

Memandu pembangunan aplikasi yang matang (production-ready) melalui 6 siklus SDLC —
Planning → Analysis → Design → Implementation → Testing → Maintenance — dengan mandat
efisiensi kerja di tiap siklus dan praktik CI/CD lintas-siklus sejak commit pertama.
Skill ini BUKAN generator UI dan BUKAN pengganti skill TDD/review: ia adalah disiplin
fase, artefak, dan gate yang memastikan tidak ada fase yang dilompati.

## When to Use

- User minta "buat aplikasi / app lengkap / aplikasi matang / production-ready".
- User minta merestrukturisasi proyek setengah jadi menjadi proses SDLC yang tertib.
- Don't use for: perbaikan bug tunggal, eksperimen throwaway (pakai skill `spike`),
  atau sekadar refactor kecil tanpa tujuan rilis.

## Prerequisites

- Repo proyek target (atau direktori kosong baru yang akan di-init).
- Tidak butuh env var / API key khusus.
- Skill pendukung bila tersedia (muat saat siklusnya tiba, jangan sekarang):
  `test-driven-development`, `requesting-code-review`, `systematic-debugging`,
  `web-security-review`, `ui-ux-pro-max`.

## How to Run

Mulai dari siklus Planning. Jalankan siklus BERURUTAN — gate tiap siklus harus lolos
dan tercatat sebelum siklus berikutnya dimulai. Untuk setiap siklus: baca mandatnya di
`references/phase-playbooks.md`, kerjakan aktivitasnya, hasilkan artefaknya, loloskan
gate-nya.

## Quick Reference

| Siklus | Artefak wajib | Gate |
|---|---|---|
| Planning | `docs/vision.md` | Kebutuhan, MVP, resource, jenis app terkonfirmasi user |
| Analysis | `docs/impact-analysis.md` | Tiap pilihan utama punya catatan dampak jangka panjang |
| Design | `docs/architecture.md` + `docs/ui-design.md` | Tak ada "TBD"; keputusan auth terjawab; hardening terjadwal |
| Implementation | Kode + workflow CI + `docs/contracts.md` | Pipeline hijau + review lolos |
| Testing | `docs/test-plan.md` + `docs/pentest-report.md` | Debugging 1-per-1 selesai; pentest kritis tertutup; release readiness |
| Maintenance | `docs/maintenance.md` | Tak ada fix tanpa analisa-plan-desain |

**Aturan CI/CD lintas-siklus (berlaku selamanya):**
1. Pipeline CI dibuat SEJAK commit pertama — build → lint → unit → integration.
2. Commit merah TIDAK boleh ditumpuk; berhenti sampai hijau.
3. Semua temuan testing diotomatisasi jadi regression test di pipeline.
4. CD bertahap: staging → smoke test → production; rollback otomatis dan DIUJI.
5. Monitoring + dependency bot aktif sejak rilis; perbaikan tetap lewat CI yang sama.

Detail per jenis app (web/desktop/mobile/iOS): `references/cicd-rules.md`.

## Procedure

### Siklus 1 — Planning: tanya dulu, spesifik, jangan menebak
Agent WAJIB bertanya (tool tanya-jawab, pilihan konkret — bukan pertanyaan terbuka
menggantung) tentang 4 hal sebelum menyusun visi:
1. **Kebutuhan user** — masalah apa, siapa pemakainya, fitur inti apa.
2. **MVP produk** — fitur minimum rilis pertama; apa yang TIDAK masuk MVP.
3. **Resource** — budget/hosting, bahasa/framework yang dikuasai, waktu, infrastruktur.
4. **Jenis app** — web, desktop, mobile, atau iOS.
Gate: keempat jawaban terdokumentasi di `docs/vision.md` (template:
`templates/vision.md`) dan dikonfirmasi user.

### Siklus 2 — Analysis: dampak jangka panjang
Analisis/memprediksi: tujuan app ke depan (roadmap 6–24 bulan), dampak setiap pilihan
user (stack, hosting, MVP) terhadap biaya migrasi / lock-in / utang teknis, dan titik
kira-kira app harus scale beserta apa yang bisa menghalanginya sekarang.
Gate: `docs/impact-analysis.md` terisi (template: `templates/impact-analysis.md`),
tiap pilihan utama punya catatan dampak + rekomendasi.

### Siklus 3 — Design: tidak boleh asal-asalan
Tiga desain wajib, urut:
1. **UI/UX** — tetapkan design system (palet, tipografi, spacing) dan PILIH jenis
   komponen yang cocok (button primary/secondary, slider vs stepper vs dropdown,
   form controls, navigasi) dengan alasan. Kriteria: kontras, hierarki visual,
   feedback state, mobile-friendly — user tidak boleh nyeri mata. Muat skill UI/UX
   bila tersedia. → `docs/ui-design.md` (`templates/ui-design.md`).
2. **System design** — arsitektur efisien sesuai bidang kerja (CRUD → monolith
   modular, real-time → event-driven, dst.) tapi TETAP scalable berdasarkan hasil
   Analysis. Definisikan data model, batas modul, bottleneck. → `docs/architecture.md`
   (`templates/architecture.md`).
3. **Security design** — desain jauh sejak awal: XSS, SQL Injection, CSRF, IDOR,
   eksposur directory/endpoint. **Aturan auth:** jika framework menyediakan auth
   bawaan dan user ingin sistem login/register sendiri → auth bawaan WAJIB
   dimatikan/diskip; jika preferensi user belum jelas → TANYAKAN di siklus ini,
   jangan asumsikan. Kalkulasi harga didesain agar selalu di BACKEND.
4. Jadwalkan **2–3 hardening pass eksplisit** di architecture.md sebagai task
   (pass 1 keamanan, pass 2 performa/resource, pass 3 simplify) — bukan "kalau sempat".
Gate: tak ada komponen "TBD", keputusan auth terjawab, rencana hardening terjadwal.

### Siklus 4 — Implementation: dependency-first + parallel terkoordinasi
1. **Download didahulukan:** dependency utama, SDK, toolchain diunduh dulu di
   background (`terminal` dengan `background=true` + `notify=true`), lalu kode
   dikerjakan PARALLEL sambil menunggu. Dilarang menulis kode dulu, pasang
   dependency belakangan.
2. **Parallel via sub-agent harus terkoneksi:** sebelum spawn, tulis
   `docs/contracts.md` (`templates/contracts.md`) berisi kontrak antarmuka — skema
   data, signature fungsi, batas modul. Setiap sub-agent WAJIB diberi: (a) potongan
   kontrak yang relevan, (b) bagian miliknya, (c) bagian siapa yang TIDAK boleh
   disentuh, (d) tugas sub-agent tetangga.
3. Buat workflow CI sejak commit pertama (lihat Aturan CI/CD di Quick Reference).
4. Kode ditulis efisien: hindari N+1 query, load seluruh dataset ke memori, polling
   saat event tersedia, render ulang tak perlu (`references/efficiency-rules.md`).
Gate: dependency terkunci (lockfile), semua modul kontrak selesai sesuai pemiliknya,
workflow CI ada dan hijau, review lolos (skill `requesting-code-review`).

### Siklus 5 — Testing: debugging dulu, prioritas bisnis, lalu pentest
Urutan KAKU, tidak boleh ditukar (`references/testing-priority.md`):
1. **Debugging menyeluruh** (skill `systematic-debugging`): seluruh bagian kode dites
   SATU PER SATU. Prioritas kode revenue: toko (produk, pembayaran, stok, keamanan
   barang) dan **kepastian harga — SEMUA kalkulasi harga (diskon, pajak, ongkir,
   pembulatan) di BACKEND; frontend hanya menampilkan**. Frontend-only pricing =
   gate gagal otomatis.
2. **Pentesting** sebelum deploy: pastikan directory sensitif (.env, .git, upload,
   admin, backup) TIDAK bisa diakses dari luar; cek OWASP praktis (skill
   `web-security-review`); deteksi kode boros resource — memory leak, koneksi tak
   ditutup, query tanpa index.
3. **Release readiness:** build dari clean checkout, isi `templates/release-checklist.md`
   (rollback DIUJI di staging, bukan hanya ditulis), CD bertahap staging → production.
Gate: temuan kritis tertutup, laporan tersimpan (`docs/test-plan.md`,
`docs/pentest-report.md`), rollback pernah diuji.

### Siklus 6 — Maintenance: analisa → plan → desain, BARU eksekusi
1. Identifikasi bagian yang butuh maintenance: monitoring/alerting, laporan bug,
   sinyal degradasi performa, usia dependency.
2. Untuk TIAP item, wajib urutan mini-SDLC: **analisa** (root cause & dampak) →
   **plan** (langkah, urutan, risiko) → **desain** (perubahan arsitektur/antarmuka
   bila perlu) — barulah eksekusi.
3. Eksekusi tetap lewat CI yang sama (branch kecil + PR).
Gate: tidak ada perbaikan tanpa dokumen analisa-plan-desainnya di
`docs/maintenance.md` (`templates/maintenance.md`).

## Pitfalls

- Melompat ke Implementation sebelum gate Planning/Analysis/Design lolos — penyebab
  utama app tidak matang.
- Mengerjakan kode sebelum dependency background selesai → rework.
- Sub-agent tanpa kontrak bersama → kode saling tabrakan.
- Kalkulasi harga di frontend → gate gagal otomatis.
- Membuat pipeline CI menjelang rilis → terlambat; WAJIB sejak commit pertama.
- Commit merah ditumpuk → debt pipeline; berhenti sampai hijau.
- Auth bawaan framework dibiarkan aktif padahal user punya sistem sendiri → bentrok.
- App kecil: boleh menggabungkan Planning+Analysis+Design jadi satu sesi, TAPI
  artefak & gate tetap ada (jangan dilompati, hanya digabung).

## Verification

- Semua artefak `docs/` ada di repo proyek (vision, impact-analysis, architecture,
  ui-design, contracts, test-plan, pentest-report, maintenance).
- Tiap gate tercatat lolos.
- Build sukses dari clean checkout; pipeline CI hijau di commit terakhir.
- Rollback pernah diuji di staging.
- Tidak ada auth bawaan framework yang bentrok dengan sistem auth user.
- Laporan debugging + pentest tersimpan dan temuan kritis statusnya closed.
