# Efficiency Rules — aturan kerja efisien untuk agent

Masalah yang dicegah: agent bekerja tidak efisien — menulis kode sebelum dependency
terpasang, sub-agent saling menimpa pekerjaan, kode boros resource.

---

## 1. Dependency-first

- Sebelum menulis baris kode pertama, kumpulkan daftar dependency utama + SDK +
  toolchain dari architecture.md.
- Mulai unduh di background (terminal background + notify selesai). Selama menunggu,
  kerjakan kode secara parallel.
- Setelah unduhan selesai: verifikasi versi, kunci lockfile, lanjutkan.
- Dilarang: menulis semua kode dulu, baru `npm install` / `pip install` di akhir —
  memicu rework versi & konfigurasi.

## 2. Parallel via sub-agent yang terkoneksi

Sebelum spawn sub-agent:

1. Tulis `docs/contracts.md`: skema data bersama, signature fungsi/interface publik,
   event/batas modul, konvensi penamaan.
2. Pecah pekerjaan per batas kontrak (bukan per file acak).
3. Setiap task sub-agent WAJIB memuat:
   - potongan kontrak yang relevan untuk tugasnya,
   - bagian miliknya (path/fungsi eksplisit),
   - bagian yang DILARANG disentuh (milik sub-agent lain),
   - ringkasan tugas sub-agent tetangga (agar hasilnya menyambung).
4. Setelah semua selesai: verifikasi integrasi lewat kontrak (import terpenuhi,
   skema cocok, test integrasi hijau).

Anti-pattern: sub-agent diberi instruksi satu kalimat ("buat modul auth") tanpa
kontrak → duplikasi, nama fungsi beda, skema tak cocok.

## 3. Kode hemat resource

Hindari pola boros ini saat menulis dan saat review:

- N+1 query → batch/join/eager loading.
- Load seluruh dataset ke memori → pagination/streaming.
- Polling ketika event/webhook tersedia → pakai event.
- Render ulang tak perlu (frontend) → memo/keys yang benar, virtual list untuk data besar.
- Koneksi (DB/HTTP/file) tidak ditutup → context manager/finally.
- Log per-request yang tidak perlu di hot path → sampling/level yang tepat.
- Algoritma O(n²) pada data yang tumbuh → struktur data yang tepat.

## 4. Hardening pass (2–3 kali, terjadwal di architecture.md)

- Pass 1 — keamanan: audit input, otorisasi per objek, secrets di env (bukan kode),
  security headers, dependency audit.
- Pass 2 — performa/resource: profil, ukur sebelum optimasi, fokus hot path.
- Pass 3 — simplify: hapus duplikasi, sederhanakan alur, hapus kode mati.

Setiap pass menghasilkan catatan perubahan; gate review menuntut ketiganya selesai.
