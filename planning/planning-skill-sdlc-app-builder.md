# Planning: Skill "sdlc-app-builder"

> Status: **PLANNING** — belum masuk tahap pembuatan.
> Tujuan: membuat skill in-repo Hermes yang memandu agent membangun App yang matang
> (production-ready) mengikuti siklus lengkap SDLC **plus praktik CI/CD sejak commit
> pertama**, sehingga app benar-benar bertahan lama — fleksibel untuk web / desktop /
> mobile / iOS.
>
> Rev 3 — mengintegrasikan CI/CD sebagai praktik lintas-siklus, bukan siklus terpisah:
> pipeline dibangun sejak siklus Implementation, regression otomatis saat Testing,
> CD bertahap (staging → production) saat release, dan observability + automation
> berkelanjutan saat Maintenance.
>
> Rev 2 — menambahkan mandat efisiensi agent per siklus (anti-kerja-asal): pertanyaan
> spesifik saat planning, analisis dampak jangka panjang, desain UI/UX + security
> yang serius, dependency-first + parallel coding, testing berprioritas bisnis +
> pentesting, dan maintenance yang beranalisa dulu.

---

## 1. Ringkasan

| Aspek | Keputusan awal |
|---|---|
| Nama skill (usulan) | `sdlc-app-builder` |
| Deskripsi (≤60 char, wajib) | `Guide mature app builds through full SDLC phases.` |
| Tier | `optional-skills/` — vertical/recurring-job, bukan daily-driver |
| Kategori | `software-development` |
| Versi awal | `0.1.0` |
| Platform | `[linux, macos, windows]` (prosedur, bukan skrip platform-bound) |
| Cakupan | Planning → Analysis → Design → Implementation → Testing → Maintenance (6 siklus, sesuai terminologi SDLC user) **+ praktik CI/CD lintas-siklus** |
| Jenis app | Fleksibel (web, desktop, mobile, iOS) — siklus sama, deliverable per-siklus berbeda |
| Tema pembeda | Efisiensi kerja agent & kualitas wajib: UI/UX, security, resource, prioritas kode bisnis |
| Ketahanan (Rev 3) | CI/CD sejak commit pertama: pipeline otomatis, regression suite, CD bertahap dengan rollback, observability, automation maintenance |

> Catatan: Deployment tidak lagi siklus terpisah — dimasukkan sebagai gate akhir
> siklus Testing (release readiness) agar mengikuti 6 siklus SDLC yang diminta user.
> Praktik CI/CD menyebar ke semua siklus (lihat Bagian 4A) — inilah yang membuat
> app bertahan lama: setiap perubahan setelah rilis tetap lolos gerbang otomatis
> yang sama seperti saat pertama dibangun.

---

## 2. Masalah yang diselesaikan

Tanpa skill ini, agent yang diminta "buatkan aplikasi" cenderung:
- langsung menulis kode tanpa bertanya kebutuhan spesifik, MVP, dan resource user,
- membuat desain "asal-asalan" (UI nyeri mata, arsitektur tidak scalable, lupa security),
- bekerja tidak efisien (mengerjakan kode sebelum dependency terunduh, sub-agent saling
  tidak tahu batas tugasnya),
- melewatkan debugging sistematis dan pentesting sebelum deploy,
- maintenance tanpa analisa, plan, dan desain terlebih dahulu.

Skill ini memaksa disiplin: **setiap siklus SDLC menghasilkan artefak + gate yang harus
lolos sebelum siklus berikutnya dimulai**, ditambah mandat kerja-efisien per siklus
(lihat Bagian 4).

---

## 3. Prinsip desain skill (penting — aturan hardline repo)

1. **BUKAN router/hub skill.** Aturan repo melarang skill yang isinya hanya pointer ke
   skill lain. Skill ini bernilai dari: artefak per siklus, gate checklist, mandat
   efisiensi, dan prosedur eksekusi — bukan tabel rujukan.
2. **Extend, jangan duplikat.** Skill pendukung yang sudah ada tetap dipakai pada siklus
   yang relevan, dicantumkan sebagai `related_skills`:
   - `test-driven-development` → siklus Implementation/Testing
   - `requesting-code-review` → gate akhir Implementation
   - `api-designer` → siklus Design (kalau app punya API)
   - `fullstack-guardian` → konteks eksekusi web fullstack
   - `systematic-debugging` → siklus Testing (tahap debugging wajib)
   - `web-security-review` / `code-review-security` → pentesting & hardening
   - `simplify-code` → hardening iteratif kode (efisiensi resource)
   Aturan menulisnya: "saat memasuki siklus X, muat skill Y" — satu kalimat, bukan duplikasi isi.
   ⚠️ Verifikasi setiap entri benar-benar ada in-repo saat tahap pembuatan.
3. **Setiap langkah punya completion criterion** yang bisa dicek (contoh:
   "SRS terwujud sebagai `docs/srs.md` di repo proyek, disetujui user" — bukan "pahami requirements").
4. **Fleksibilitas via matriks deliverable**, bukan via branching teks panjang:
   satu tabel siklus × jenis app (web/desktop/mobile/iOS) menentukan artefak minimum.

---

## 4. MANDAT EFISIENSI PER SIKLUS (inti rev-2, wajib masuk SKILL.md)

Ini jawaban atas masalah "agent bekerja tidak efisien". Setiap mandat ditulis sebagai
aturan imperatif dengan completion criterion yang bisa dicek.

### 4.1 Siklus PLANNING — tanya dulu, spesifik, jangan menebak

Agent **HARUS** bertanya (via `clarify`) dengan sangat spesifik sebelum menyusun visi:
1. **Kebutuhan user** — masalah apa yang diselesaikan, siapa pemakainya, fitur intinya apa.
2. **MVP produk** — fitur minimum yang harus ada di rilis pertama; apa yang TIDAK masuk MVP.
3. **Resource yang dimiliki** — budget/hosting, bahasa/framework yang dikuasai tim,
   waktu, infrastruktur, lisensi.
4. **Jenis app yang diinginkan** — web, desktop, mobile, atau iOS.

Aturan: tidak boleh pindah ke siklus Analysis sebelum keempat kelompok jawaban ini
terdokumentasi di `docs/vision.md` dan dikonfirmasi user.
Anti-pattern yang dilarang: mengasumsikan jawaban, atau bertanya dengan pertanyaan
terbuka yang menggantung ("kamu mau aplikasi seperti apa?") — tanyakan pilihan konkret.

### 4.2 Siklus ANALYSIS — analisis dampak jangka panjang

Karena arah app sudah jelas dari Planning, agent **HARUS** menganalisis/memprediksi:
- **Tujuan app ke depan** — ke mana produk ini berkembang (roadmap 6–24 bulan).
- **Dampak pilihan user dalam jangka panjang** — setiap pilihan di Planning (stack,
  hosting, fitur MVP) dievaluasi konsekuensinya: biaya migrasi, vendor lock-in,
  utang teknis, biaya operasional.
- **Arah perkembangan & scaling** — kira-kira di titik mana app harus scale (user naik,
  data membesar, fitur bertambah), dan apakah pilihan sekarang tidak menghalangi itu.

Artefak: `docs/impact-analysis.md` (berisi asumsi, proyeksi, dan risiko pilihan user).
Gate: setiap pilihan utama user punya catatan dampak jangka panjang + rekomendasi.

### 4.3 Siklus DESIGN — tidak boleh asal-asalan

Ini siklus dengan mandat terbanyak, karena kesalahan desain paling mahal:

**a. UI/UX design (WAJIB dipelajari dulu, bukan dikerjakan insting):**
- Agent harus menetapkan design system: palet warna, tipografi, spacing, dan **memilih
  jenis komponen yang cocok** — button (primary/secondary/toggle), slider vs stepper
  vs dropdown, form controls, navigasi — bukan memakai komponen default apa adanya.
- Kriteria nyata: user tidak boleh merasa sakit mata / tidak nyaman — kontras, hierarki
  visual, feedback state (hover/active/disabled), dan mobile-friendly harus dicek.
- Kalau tersedia, muat skill UI/UX yang ada (mis. `ui-ux-pro-max`) untuk rujukan
  pattern & palet — satu kalimat rujukan, bukan duplikasi isinya.

**b. System design:**
- Arsitektur yang **efisien sesuai bidang pekerjaannya masing-masing** (kata kerja
  dipetakan ke arsitektur yang tepat: CRUD → monolith modular, real-time → event-driven,
  dsb.) tapi **harus tetap scalable** — dipilih berdasarkan hasil siklus Analysis.
- Definisikan data model, batas antar-modul, dan bottleneck yang diperkirakan.

**c. Security design (sejak awal, bukan setelah jadi):**
- Ancaman eksplisit yang harus didesain jauh: XSS, SQL Injection, CSRF, IDOR, dan
  eksposur endpoint/ directory dari luar.
- Aturan **login/register**: jika framework yang dipilih menyediakan auth bawaan
  (mis. NextAuth, Supabase Auth, Laravel Breeze) dan user menginginkan sistem
  login/register sendiri → **auth bawaan WAJIB dimatikan/diskip** supaya tidak
  bertabrakan. Jika user belum menyatakan preferensinya → **tanyakan** di siklus ini,
  jangan asumsikan.
- Kalkulasi harga/nilai uang: didesain agar selalu dioperasikan di **BACKEND**
  (lihat 4.5).

**d. Komitmen kualitas kode:**
- Kode harus ditulis **efisien** — hindari pola boros resource (N+1 query, load seluruh
  dataset ke memori, polling saat event tersedia, render ulang tidak perlu).
- Rencanakan **minimal 2–3 kali hardening pass** terhadap kode yang diimplementasikan
  nanti (pass 1: keamanan; pass 2: performa/resource; pass 3: simplify) — dijadwalkan
  di `docs/architecture.md` sebagai task eksplisit, bukan "kalau sempat".

Artefak: `docs/architecture.md` + `docs/ui-design.md` (design tokens + keputusan
komponen + alasannya) + security checklist. Gate: tidak ada komponen "TBD", keputusan
auth (bawaan vs sendiri) sudah terjawab, rencana hardening terjadwal.

### 4.4 Siklus IMPLEMENTATION — dependency-first + parallel yang terkoordinasi

- **Download didahulukan:** semua yang berhubungan dengan download (dependency utama,
  SDK, toolchain, image base) dimulai **lebih dulu di background** (`terminal` dengan
  `background=true` + `notify=true`), lalu sambil menunggu, agent mengerjakan kode
  secara **parallel**. Dilarang menulis kode lalu baru memasang dependency di akhir.
- **Parallel via sub-agent harus terkoneksi:** ketika memecah pekerjaan ke sub-agent
  (`delegate_task`), setiap sub-agent WAJIB:
  1. diberi konteks kontrak bersama — skema data, nama fungsi/interface, batas modul;
  2. tahu **bagian mana yang miliknya** dan bagian siapa yang tidak boleh disentuh;
  3. tahu tugas sub-agent lain yang bersebelahan, agar hasilnya menyambung.
  Implementasinya: satu `docs/contracts.md` (atau bagian di architecture.md) berisi
  kontrak antarmuka yang ditulis SEBELUM spawn, dan setiap task sub-agent menyertakan
  potongan kontrak yang relevan.
- Gate: semua dependency terpasang & terkunci (lockfile), semua modul dari kontrak
  selesai sesuai pemiliknya, review lewat `requesting-code-review`.

### 4.5 Siklus TESTING — debugging dulu, prioritas kode bisnis, lalu pentesting

Urutan WAJIB (tidak boleh ditukar):

**1. Debugging menyeluruh (langkah pertama):**
- Seluruh bagian kode dites **satu per satu** — muat skill `systematic-debugging`.
- **Prioritas pada kode yang berdampak langsung pada keuntungan perusahaan** (revenue path):
  - kode Toko: produk, pembayaran, stok barang, keamanan barang;
  - **kepastian harga: SEMUA kalkulasi harga (diskon, pajak, ongkir, pembulatan)
    dilakukan di operasi BACKEND** — frontend hanya menampilkan hasil. Ini aturan
    kunci; frontend-only pricing = gate gagal.

**2. Pentesting (sebelum deploy):**
- Lakukan pentest pada app (web) agar tidak ada kebocoran/vuln setelah deploy:
  - pastikan **directory listing / file sensitif TIDAK BISA diakses dari luar app**
    (`.env`, `.git`, upload dir, admin panel, backup, dsb.);
  - cek OWASP Top 10 praktis: injeksi, XSS, auth bypass, IDOR, misconfiguration
    (muat `web-security-review`);
  - cari titik lemah dari sisi manapun: vuln website maupun **kerusakan desain kode
    yang membuat resource boros** — memory leak, koneksi tidak ditutup, query tanpa
    index, algoritma yang salah tulis.
- Gate: temuan kritis ditutup sebelum lanjut; laporan pentest tersimpan di
  `docs/test-plan.md` / `docs/pentest-report.md`.

**3. Release readiness (pengganti siklus Deployment):** build dari clean checkout,
checklist rilis, rollback plan (`templates/release-checklist.md`).

### 4.6 Siklus MAINTENANCE — analisa → plan → desain, BARU eksekusi

- Agent **harus tahu bagian mana yang membutuhkan maintenance**: identifikasi dari
  monitoring/log, laporan bug, sinyal degradasi performa, atau usia dependency.
- Sebelum menyentuh kode, wajib urutan: **analisa (root cause & dampak) → plan
  (langkah, urutan, risiko) → desain (perubahan arsitektur/antarmuka bila perlu)** —
  ini siklus mini SDLC untuk tiap maintenance; barulah eksekusi.
- Artefak: `docs/maintenance.md` (log keputusan per item maintenance).
- Gate: tidak ada perbaikan yang dieksekusi tanpa dokumen analisa-plan-desainnya.

### 4A. CI/CD — praktik lintas-siklus (Rev 3: kunci ketahanan jangka panjang)

CI/CD bukan siklus terpisah, melainkan **jaring pengaman otomatis yang dibangun
sejak dini dan dipakai selamanya**. Prinsip: apa yang lolos untuk rilis pertama
harus menjadi gerbang yang sama untuk semua perubahan berikutnya — di situlah app
"bertahan lama".

**a. CI (Continuous Integration) — dibangun SEJAK siklus Implementation:**
- Pipeline otomatis dibuat sejak commit pertama, bukan menjelang rilis. Isi minimum:
  build sukses → lint/format → unit test → integration test (per jenis app,
  lihat matriks Bagian 7).
- Setiap commit/push memicu pipeline; **commit yang merah TIDAK boleh ditumpuk** —
  perbaiki dulu sebelum lanjut fitur berikutnya.
- Workflow file (`.github/workflows/` atau setara) = artefak wajib siklus
  Implementation, disimpan di repo proyek.
- Gate Implementation berpindah: review kode **plus** pipeline hijau.

**b. Regression otomatis — saat siklus Testing:**
- Semua test dari debugging menyeluruh (4.5 langkah 1) + pentest regresi (4.5
  langkah 2: cek eksposur directory, pricing-backend, OWASP praktis) diotomatisasi
  menjadi bagian CI — jadi bug yang pernah ditemukan **tidak bisa masuk lagi tanpa
  terdeteksi**.
- Contract test antar modul (dari `templates/contracts.md`) ikut dijalankan di
  pipeline, menjaga hasil kerja parallel sub-agent tetap nyambung selamanya.

**c. CD (Continuous Deployment) — saat release readiness (gate akhir Testing):**
- Rilis **bertahap, bukan langsung**: staging environment dulu → smoke test lulus →
  production.
- **Rollback plan otomatis** (versi sebelumnya bisa dipulihkan cepat) — wajib ada
  di `templates/release-checklist.md` dan diuji, bukan hanya ditulis.
- Deployment dipicu pipeline (tag/merge ke main), bukan deploy manual — menghilangkan
  faktor lupa/langkah terlewat.
- Desktop/mobile: channel update otomatis (auto-update / store track) + versioning
  semver yang konsisten.

**d. Observability & automation — saat siklus Maintenance:**
- Monitoring + alerting (error rate, performa, resource) sudah dipasang sejak
  rilis pertama; siklus Maintenance memakainya untuk **mendeteksi otomatis bagian
  yang butuh maintenance** (memperkuat mandat 4.6).
- Dependency update otomatis (dependabot/renovate atau setara) dengan pipeline
  sebagai penentu aman/tidaknya update.
- Praktik trunk-based / branch kecil + PR: setiap perbaikan maintenance tetap
  melalui CI yang sama → kualitas tidak menurun seiring usia app.

**Prinsip penulisan di SKILL.md:** CI/CD ditulis sebagai aturan lintas-siklus
(ditandai jelas di Quick Reference), bukan siklus ke-7 — agar tetap sesuai struktur
6 siklus SDLC yang diminta user.

---

## 5. Struktur SKILL.md (mengikuti section order modern)

```
# SDLC App Builder Skill
Intro 2-3 kalimat: memandu pembangunan app matang lewat 6 siklus SDLC dengan mandat
efisiensi per siklus; bukan generator UI, bukan pengganti skill TDD/review.

## When to Use
- User minta "buat aplikasi / app lengkap / aplikasi matang / production-ready"
- User minta merestrukturisasi proyek setengah jadi ke proses SDLC
Don't use for: perbaikan bug tunggal, eksperimen throwaway (pakai skill `spike`)

## Prerequisites
- Repo proyek target (atau direktori kosong baru)
- Tidak butuh env var / API key khusus

## How to Run
- Mulai dari siklus Planning, urutan ketat, gate tiap siklus

## Quick Reference
- Tabel: Siklus → Artefak wajib → Gate → Mandat utama (termasuk mandat CI/CD)
- Blok "Aturan CI/CD lintas-siklus": pipeline sejak commit pertama, commit merah
  tidak ditumpuk, regression otomatis, CD bertahap + rollback, dependency bot
- Matriks deliverable per jenis app (web / desktop / mobile / iOS)

## Procedure (inti — tiap siklus: input, aktivitas, artefak, gate)
Per-utama ringkas di SKILL.md (mengacu Bagian 4 planning ini); detail eksekusi
ada di references/phase-playbooks.md.

## Pitfalls
- Melompat ke Implementation sebelum gate Planning/Analysis/Design lolos
- Mengerjakan kode sebelum dependency background selesai → rework
- Sub-agent tanpa kontrak bersama → kode saling tabrakan
- Kalkulasi harga di frontend → gate gagal otomatis
- Membuat pipeline CI menjelang rilis → terlambat; pipeline WAJIB sejak commit pertama
- Commit merah ditumpuk → debt pipeline; berhenti dulu sampai hijau
- App kecil: gabungkan Planning+Analysis+Design jadi satu sesi (artefak & gate tetap ada)

## Verification
- Semua artefak docs/ ada; tiap gate tercatat lolos; build dari clean checkout sukses;
  pipeline CI hijau di commit terakhir; laporan debugging+pentest tersimpan; rollback
  pernah diuji; tidak ada auth bawaan yang bentrok
```

Target ukuran: **~200 baris** inti SKILL.md. Mandat detail per siklus dipindah ke
`references/` agar SKILL.md tetap padat.

---

## 6. Supporting files (rencana)

| File | Isi |
|---|---|
| `references/phase-playbooks.md` | Playbook detail per siklus: daftar pertanyaan wajib Planning, template impact-analysis, keputusan komponen UI, security checklist, pola hardening |
| `references/efficiency-rules.md` | Aturan efisiensi agent: dependency-first parallel pattern, kontrak antar sub-agent, anti-pattern resource boros |
| `references/cicd-rules.md` | Aturan CI/CD lintas-siklus: isi pipeline minimum per jenis app, kebijakan commit merah, regression otomatis, CD bertahap, rollback otomatis, dependency bot |
| `references/testing-priority.md` | Urutan testing, daftar prioritas revenue-path, checklist pentest (dir listing, OWASP praktis, memory leak) |
| `templates/vision.md` | Template kebutuhan user + MVP + resource + jenis app |
| `templates/impact-analysis.md` | Template dampak jangka panjang & scaling |
| `templates/architecture.md` | Template arsitektur + decision record + rencana hardening |
| `templates/ui-design.md` | Template design tokens + keputusan komponen (button/slider/form) + alasan |
| `templates/contracts.md` | Template kontrak antarmuka untuk parallel sub-agent |
| `templates/srs.md` | Template SRS ringkas |
| `templates/test-plan.md` | Template test plan (debugging + pentest) per jenis app |
| `templates/release-checklist.md` | Checklist rilis (build, packaging, rollback TERUJI, staging → production) |
| `templates/maintenance.md` | Template analisa-plan-desain maintenance |
| `scripts/check_gate.py` | Opsional: verifikasi artefak siklus ada di repo proyek (stdlib only, cross-platform) |

---

## 7. Matriks deliverable per jenis app (draf isi Quick Reference)

| Siklus | Web | Desktop | Mobile/iOS |
|---|---|---|---|
| Design | API contract, data model, wireframe UI, auth strategy | arsitektur proses, persistensi, layout | navigasi, state, platform guidelines |
| Implementation | background install deps, parallel modul per kontrak, workflow CI (.github/workflows) | build toolchain dulu di background + pipeline build | SDK/emulator dulu di background + pipeline build |
| Testing | unit + E2E browser + pentest (OWASP, dir exposure) + regression otomatis | unit + UI automation + resource profiling | unit + device/emulator + store policy check |
| Release | CD bertahap: staging → production, rollback otomatis | installer + auto-update channel, rollback versi | store track (beta → prod), staged rollout |
| Maintenance | monitoring/alerting, dependency bot, error tracking | crash reporting, update channel | store update, analytics |

---

## 8. Rencana pengujian & dokumentasi (wajib untuk skill in-repo)

1. **Tests:** `tests/skills/test_sdlc_app_builder_skill.py`
   - validasi frontmatter (name/description ≤60/platforms)
   - validasi struktur body (section order minimum)
   - validasi semua `related_skills` ada in-repo
   - (bila scripts/check_gate.py ada) unit test script dengan repo fixture sementara
2. **Docs regen:** `python website/scripts/generate-skill-docs.py`, lalu `git checkout --`
   semua file yang bukan milik skill ini; pastikan `website/sidebars.ts` berisi
   tepat satu entri slug.
3. **Verifikasi manual:** jalankan skill pada proyek contoh kecil (mis. mini toko CLI/web
   dengan pricing) — semua siklus menghasilkan artefak, gate tercatat, dan aturan
   kunci (kalkulasi harga backend, dependency-first) terlihat dipatuhi.

---

## 9. Roadmap pembuatan (urutan eksekusi nanti)

1. Survey 2–3 peer SKILL.md di `skills/software-development/` dan `optional-skills/`
   untuk tone & struktur → catat pola. Verifikasi `related_skills` in-repo.
2. Finalisasi nama, description, tier (konfirmasi ke user sebelum commit).
3. Draft SKILL.md (`write_file` ke `optional-skills/software-development/sdlc-app-builder/`)
   — pastikan Bagian 4 (mandat efisiensi) terangkum di Procedure + Quick Reference.
4. Buat templates/ dan references/ sesuai Bagian 6.
5. Validasi lokal (skrip validasi frontmatter dari skill authoring).
6. Tulis tests → jalankan `scripts/run_tests.sh`.
7. Regen docs dengan scope discipline → cek sidebar.
8. Commit + PR.

Estimasi: 1 sesi kerja setelah planning disetujui.

---

## 10. Risiko & mitigasi

| Risiko | Mitigasi |
|---|---|
| Dianggap router skill oleh reviewer | Isi inti = artefak + gate + mandat + prosedur; rujukan skill lain hanya 1 kalimat per siklus |
| Terlalu besar (>200 baris) karena mandat banyak | Detail mandat → `references/efficiency-rules.md` dan `references/phase-playbooks.md` |
| Tumpang tindih dengan `fullstack-guardian` / security skills | Skill ini fokus lifecycle & disiplin siklus; skill lain fokus eksekusi. Jelaskan pembagian di intro |
| Deskripsi >60 char / bahasa marketing | Validasi otomatis di langkah 5 roadmap |
| App kecil terbebani proses penuh | Tailoring di Pitfalls: fusi Planning+Analysis+Design untuk proyek kecil |
| Mandat "hardening beberapa kali" jadi hiasan | Hardening dijadwalkan sebagai task eksplisit di architecture.md + gate review |
| Sub-agent parallel salah kaprah | Kontrak `templates/contracts.md` wajib dibuat SEBELUM spawn sub-agent |
| CI/CD membuat skill terlalu gemuk | CI/CD diringkas jadi 1 blok aturan lintas-siklus di SKILL.md; detail ke `references/cicd-rules.md` |
| Pipeline CI dibuat terlambat (menjelang rilis) | Workflow CI = artefak WAJIB siklus Implementation; gate Implementation menuntut pipeline hijau |
| Rollback hanya tertulis di dokumen | Checklist rilis mewajibkan rollback DIUJI di staging, bukan hanya didokumentasikan |
| CI/CD hard-coded ke GitHub Actions | `references/cicd-rules.md` menyebut alternatif (GitLab CI, desktop/mobile pipeline) sesuai platform |

---

## 11. Checklist kesiapan sebelum pembuatan

- [ ] User menyetujui nama skill & deskripsi
- [ ] User menyetujui tier `optional-skills/`
- [ ] User menyetujui struktur 6 siklus (Planning → Analysis → Design → Implementation → Testing → Maintenance) + release readiness di gate Testing
- [ ] User menyetujui mandat efisiensi per siklus (Bagian 4)
- [ ] User menyetujui integrasi CI/CD lintas-siklus (Bagian 4A): pipeline sejak commit pertama, regression otomatis, CD bertahap, observability
- [ ] Keputusan: perlu `scripts/check_gate.py` atau tidak
- [ ] (Opsional) nama author kredit manusia untuk frontmatter
