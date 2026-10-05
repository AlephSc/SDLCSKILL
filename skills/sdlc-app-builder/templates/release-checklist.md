# Release Checklist — {nama app} v{versi}

> Diisi di release readiness (gate akhir Siklus Testing).

## Build
- [ ] Build sukses dari CLEAN checkout (bukan folder kerja lama)
- [ ] Semua test pipeline hijau di commit yang dirilis
- [ ] Versi semver naik & tercatat

## Packaging / Distribusi
- Web: hosting/CDN siap, domain, environment variables terpasang
- Desktop: installer (MSI/DMG/AppImage) dibangun, code signing
- Mobile/iOS: store packaging, signing, metadata, screenshot

## CD Bertahap
- [ ] Deploy ke STAGING sukses
- [ ] Smoke test di staging lulus (alur revenue: bayar, harga, stok)
- [ ] Baru deploy ke PRODUCTION

## Rollback (WAJIB DIUJI, bukan hanya ditulis)
- [ ] Prosedur rollback terdokumentasi (langkah persis)
- [ ] Rollback DIUJI di staging minimal sekali: versi sebelumnya dipulihkan & jalan
- [ ] Data/user tidak hilang saat rollback (migrasi reversible bila ada)

## Pasca-Rilis
- [ ] Monitoring + alerting aktif (error rate, latensi, resource)
- [ ] Dependency update bot aktif (update diterima hanya bila CI hijau)
- [ ] Kanal bug/laporan user siap
