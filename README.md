# SDLCSKILL — SDLC App Builder Skill

Skill yang memandu AI agent membangun aplikasi yang **matang (production-ready) dan
bertahan lama** melalui disiplin **SDLC + CI/CD**: enam siklus dengan artefak dan gate
yang wajib lolos, mandat efisiensi kerja agent di tiap siklus, dan praktik CI/CD yang
dibangun sejak commit pertama.

## Struktur

```
skills/sdlc-app-builder/
├── SKILL.md                      # inti skill: 6 siklus, gate, aturan CI/CD
├── references/
│   ├── phase-playbooks.md        # panduan detail per siklus
│   ├── efficiency-rules.md       # dependency-first, parallel terkoordinasi, kode hemat resource
│   ├── cicd-rules.md             # pipeline sejak commit pertama, CD bertahap, rollback
│   └── testing-priority.md       # debugging revenue-first, checklist pentest
└── templates/
    ├── vision.md                 # kebutuhan user + MVP + resource + jenis app
    ├── impact-analysis.md        # dampak jangka panjang & scaling
    ├── architecture.md           # arsitektur + decision record + rencana hardening
    ├── ui-design.md              # design tokens + keputusan komponen
    ├── contracts.md              # kontrak antarmuka untuk parallel sub-agent
    ├── srs.md                    # requirement formal ringkas
    ├── test-plan.md              # debugging + pentest
    ├── release-checklist.md      # rilis bertahap + rollback teruji
    └── maintenance.md            # analisa → plan → desain → eksekusi
```

## 6 Siklus + Gate

| Siklus | Artefak | Gate |
|---|---|---|
| 1. Planning | `docs/vision.md` | Kebutuhan, MVP, resource, jenis app terkonfirmasi user |
| 2. Analysis | `docs/impact-analysis.md` | Tiap pilihan utama punya catatan dampak jangka panjang |
| 3. Design | `docs/architecture.md` + `docs/ui-design.md` | Tak ada "TBD"; auth terjawab; hardening terjadwal |
| 4. Implementation | Kode + workflow CI + `docs/contracts.md` | Pipeline hijau + review lolos |
| 5. Testing | `docs/test-plan.md` + `docs/pentest-report.md` | Debugging 1-per-1, pentest kritis tertutup, rollback teruji |
| 6. Maintenance | `docs/maintenance.md` | Tak ada fix tanpa analisa-plan-desain |

**Aturan CI/CD lintas-siklus:** pipeline sejak commit pertama; commit merah tidak
ditumpuk; temuan testing diotomatisasi jadi regression; CD bertahap staging →
production dengan rollback teruji; monitoring + dependency bot sejak rilis.

## Aturan Kunci (yang paling sering dilanggar agent lain)

1. **Planning:** agent WAJIB bertanya spesifik — kebutuhan, MVP, resource, jenis app —
   sebelum menulis kode apa pun. Dilarang menebak.
2. **Analysis:** prediksi dampak jangka panjang setiap pilihan user (lock-in, utang
   teknis, titik scale) sebelum desain.
3. **Design:** UI/UX dipelajari dulu (komponen dipilih dengan alasan, bukan default),
   security didesain sejak awal (XSS, SQLi, CSRF, IDOR, eksposur directory), auth
   bawaan framework DIMATIKAN bila user punya sistem sendiri, dan 2–3 hardening pass
   dijadwalkan eksplisit.
4. **Implementation:** dependency diunduh di background DULU, kode dikerjakan parallel;
   sub-agent wajib terkoneksi lewat kontrak tertulis sebelum spawn.
5. **Testing:** debugging seluruh kode satu per satu, prioritas kode revenue; SEMUA
   kalkulasi harga di BACKEND (frontend-only pricing = gagal otomatis); pentesting
   sebelum deploy.
6. **Maintenance:** analisa → plan → desain dulu, baru eksekusi.

## Cara Pakai

Tempatkan folder `skills/sdlc-app-builder/` di library skill agent Anda (Hermes:
`~/.hermes/skills/` atau in-repo `optional-skills/software-development/sdlc-app-builder/`),
lalu minta agent membangun aplikasi — skill akan memuat sendiri saat konteksnya cocok.
Untuk skill Hermes, file SKILL.md + references/ + templates/ dipakai apa adanya.

## Sumber

- Planning lengkap: [`planning/planning-skill-sdlc-app-builder.md`](planning/planning-skill-sdlc-app-builder.md)
- License: MIT

---

Maintained by [AlephSc](https://github.com/AlephSc).
