# Architecture — {nama app}

> Diisi di Siklus Design. Gate: tidak ada "TBD", keputusan auth terjawab,
> rencana hardening terjadwal.

## 1. Gambaran Arsitektur
- Pola (monolith modular / event-driven / lainnya) dan alasannya:
- Diagram (teks/mermaid):
- Komponen & batas modul:

## 2. Data Model
- Entitas utama & relasi:
- Skema kunci (tabel/koleksi):

## 3. Keputusan Teknologi (Decision Record)
| # | Keputusan | Alasan | Alternatif yang ditolak | Dampak jangka panjang |
|---|---|---|---|---|
| 1 | | | | |

## 4. Keputusan Auth (WAJIB terjawab)
- Framework punya auth bawaan? (ya/tidak, sebutkan):
- User ingin sistem login/register sendiri? (ya/tidak/belum ditanya):
- Keputusan: bawaan DIMATIKAN / dipakai bawaan / sistem sendiri dibangun:
- Alasan:

## 5. Security Design
- XSS:
- SQL Injection:
- CSRF:
- IDOR:
- Eksposur directory/endpoint:
- Aturan kalkulasi harga: semua di BACKEND (ya/):

## 6. Rencana Hardening (task eksplisit, bukan "kalau sempat")
| Pass | Fokus | Kriteria selesai |
|---|---|---|
| 1 | Keamanan | audit input, otorisasi, secrets, headers selesai |
| 2 | Performa/resource | profil query, memori, bundle; temuan diperbaiki |
| 3 | Simplify | duplikasi & kode mati dihapus |

## 7. Antarmuka Antar Modul
Ringkasan kontrak (detail penuh ditulis di `docs/contracts.md` sebelum spawn
sub-agent):
