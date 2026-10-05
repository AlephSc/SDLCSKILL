# UI Design — {nama app}

> Diisi di Siklus Design. Agent memilih komponen dengan ALASAN, bukan default.
> Kriteria: user tidak nyeri mata / tidak nyaman = gate gagal.

## 1. Design Tokens
- Palet warna (kontras AA minimum):
- Tipografi (jenis huruf, skala ukuran):
- Spacing scale:
- Radius / elevation / border:

## 2. Keputusan Komponen (dengan alasan)
| Kebutuhan | Komponen dipilih | Mengapa bukan alternatifnya |
|---|---|---|
| Aksi utama per layar | Button primary (satu per layar) | |
| Aksi sekunder | Button secondary/ghost | |
| Input nilai dengan rentang | slider / stepper / dropdown / text | |
| Navigasi | sidebar / tab / bottom-nav | |
| Form panjang | multi-step vs satu halaman | |
| Umpan balik | loading, empty, error, success state | |

## 3. Checklist Kenyamanan (gate)
- [ ] Hierarki visual jelas (mata tahu mana yang penting dulu)
- [ ] Kontras teks memenuhi AA
- [ ] Semua state interaktif punya feedback (hover/active/disabled/loading)
- [ ] Responsive / mobile-friendly dites
- [ ] Tidak ada kombinasi warna yang menyakitkan mata
- [ ] Konsisten memakai design tokens di atas (tidak ada hard-code warna/ukuran)
