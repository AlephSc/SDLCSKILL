# Test Plan — {nama app}

> Diisi di Siklus Testing. Urutan kaku: Debugging → Pentesting → Release readiness.

## 1. Debugging Menyeluruh (satu per satu)
| # | Modul/Fungsi | Prioritas (revenue=1) | Hasil | Bug ditemukan | Regression test dibuat? |
|---|---|---|---|---|---|
| 1 | Pembayaran | 1 | | | |
| 2 | Harga (kalkulasi BACKEND) | 1 | | | |
| 3 | Stok | 1 | | | |
| 4 | Produk | 2 | | | |
| 5 | {modul non-revenue} | 3 | | | |

Aturan harga: semua kalkulasi (diskon, pajak, ongkir, pembulatan) di backend.
Frontend-only pricing ditemukan = gagal otomatis, perbaiki sebelum lanjut.

## 2. Pentesting
Gunakan checklist lengkap di `../references/testing-priority.md` §2.
| Cek | Hasil | Temuan | Status (open/closed) |
|---|---|---|---|
| Eksposur directory (.env, .git, backup, admin) | | | |
| SQL Injection | | | |
| XSS / CSP | | | |
| CSRF | | | |
| IDOR | | | |
| Auth bypass | | | |
| Rate limiting | | | |
| Resource (memory leak, koneksi, index, algoritma) | | | |

## 3. Hasil
- Temuan kritis/high yang ditutup:
- Temuan medium/low yang ditunda (dengan alasannya):
- Laporan pentest penuh: `docs/pentest-report.md`
