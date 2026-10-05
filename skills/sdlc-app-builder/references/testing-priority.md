# Testing Priority — urutan testing dan daftar prioritas

Urutan KAKU di siklus Testing (tidak boleh ditukar):
**Debugging menyeluruh → Pentesting → Release readiness.**

---

## 1. Debugging menyeluruh (langkah pertama)

- Seluruh bagian kode dites SATU PER SATU (muat skill `systematic-debugging` bila ada).
- Prioritas kode yang berdampak langsung pada keuntungan perusahaan (revenue path):

  | Prioritas | Area | Yang dites |
  |---|---|---|
  | 1 | Pembayaran | alur bayar, refund, status transaksi, idempotency, webhook |
  | 2 | Harga | SEMUA kalkulasi (diskon, pajak, ongkir, pembulatan) di BACKEND; frontend hanya menampilkan. Frontend-only pricing = gagal otomatis |
  | 3 | Stok | reservasi, pengurangan atomic, oversell prevention, keamanan barang |
  | 4 | Produk | CRUD katalog, varian, validasi |
  | 5 | Modul non-revenue | auth flow, profil, notifikasi, dst. |

- Setiap bug ditemukan → catat di test-plan → tulis regression test-nya → masuk
  pipeline CI (lihat `cicd-rules.md` §2).

## 2. Pentesting (sebelum deploy)

Checklist minimum:

- [ ] Directory sensitif tidak bisa diakses dari luar: `.env`, `.git`, `backup/`,
      `uploads/` tak terbatas, panel admin, file dump, debug endpoint.
- [ ] Directory listing dimatikan di web server.
- [ ] SQL Injection: semua query parameterized / lewat ORM.
- [ ] XSS: semua output user-content di-escape; CSP dipasang.
- [ ] CSRF: token / same-site cookie di semua form & state-changing endpoint.
- [ ] IDOR: cek otorisasi per objek (user A tidak bisa akses data user B lewat ID).
- [ ] Auth bypass: endpoint terlindungi dites tanpa token/session.
- [ ] Secrets: tidak ada di kode, log, atau response API; semua lewat env.
- [ ] Error message tidak membocorkan stack trace / struktur internal.
- [ ] Rate limiting di endpoint auth & payment.

Plus deteksi kerusakan desain kode yang boros resource:

- [ ] Memory leak (objek besar bertahan, listener tak dilepas, cache tanpa batas).
- [ ] Koneksi DB/HTTP/file tidak ditutup.
- [ ] Query tanpa index pada tabel besar.
- [ ] Algoritma salah tulis (O(n²) pada data tumbuh, loop bersarang tak perlu).

Tools bantuan bila ada: skill `web-security-review` (OWASP), `code-review-security`,
`secrets-scan`.

**Gate:** temuan kritis/high ditutup sebelum lanjut; laporan tersimpan di
`docs/pentest-report.md`.

## 3. Release readiness

- Build dari clean checkout sukses (bukan dari folder kerja yang "kebetulan jalan").
- Semua test pipeline hijau di commit yang dirilis.
- Rollback DIUJI di staging minimal sekali (puliha versi sebelumnya, verifikasi jalan).
- CD bertahap: staging → smoke test → production.
- Isi `templates/release-checklist.md` dan simpan sebagai `docs/deployment.md`.
