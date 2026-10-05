# Contracts — kontrak antarmuka antar modul/sub-agent

> WAJIB ditulis SEBELUM spawn sub-agent (Siklus Implementation).
> Setiap task sub-agent menyertakan potongan kontrak yang relevan.

## 1. Skema Data Bersama
```
{entitas, field, tipe, validasi — bahasa apa pun yang jelas}
```

## 2. Signature Fungsi / Interface Publik
```
{nama fungsi, parameter, return, error yang dilempar}
```

## 3. Event / Batas Modul
| Event/Aksi | Pemancar | Penerima | Payload |
|---|---|---|---|
| | | | |

## 4. Pembagian Wilayah Kerja (wajib eksplisit)
| Sub-agent / Modul | MILIKNYA (path/fungsi) | DILARANG menyentuh | Tugas tetangga yang relevan |
|---|---|---|---|
| | | | |

## 5. Konvensi Bersama
- Penamaan:
- Struktur direktori:
- Error handling:
- Style commit:

## Verifikasi Integrasi (setelah semua selesai)
- [ ] Semua import/depensi antar modul terpenuhi sesuai kontrak
- [ ] Skema data cocok di semua modul
- [ ] Test integrasi hijau (masuk juga ke pipeline CI permanen)
