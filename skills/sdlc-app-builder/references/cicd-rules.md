# CI/CD Rules — aturan lintas-siklus untuk app yang bertahan lama

Prinsip: gerbang yang sama yang dilalui rilis pertama menjadi gerbang permanen untuk
setiap perubahan berikutnya. CI/CD dibangun sejak dini, bukan menjelang rilis.

---

## 1. CI — sejak commit pertama (siklus Implementation)

Pipeline minimum, urut:

1. Build sukses (compile/bundle/build image).
2. Lint + format check.
3. Unit test.
4. Integration test.

Kebijakan:

- Setiap push/commit memicu pipeline.
- **Commit merah tidak boleh ditumpuk** — berhenti, perbaiki, baru lanjut.
- Workflow file disimpan di repo proyek (`.github/workflows/` atau setara).
- Gate Implementation = review kode + pipeline hijau.

Contoh isi pipeline per jenis app:

| Jenis app | Job CI minimum |
|---|---|
| Web | build, lint, unit, integration (DB test container), E2E smoke |
| Desktop | build per OS target, lint, unit, smoke launch |
| Mobile/iOS | build debug, lint, unit, emulator/simulator smoke |

Platform CI: GitHub Actions bila repo di GitHub; GitLab CI / build server setara
bila bukan — prinsipnya sama, jangan hard-code ke satu platform.

## 2. Regression otomatis (siklus Testing)

- Semua temuan debugging + pentest yang bisa diotomatisasi → jadi test di pipeline:
  - pricing-backend test (kalkulasi harga tidak boleh bisa dipindah ke frontend),
  - eksposur directory (scan path .env, .git, backup, admin dari luar),
  - regression bug yang pernah ditemukan (bug lama tak boleh masuk lagi tanpa
    terdeteksi).
- Contract test antar modul ikut dijalankan permanen — menjaga hasil kerja parallel
  sub-agent tetap nyambung selama app hidup.

## 3. CD — bertahap (release readiness)

- Staging environment dulu → smoke test lulus → baru production.
- Deployment dipicu pipeline (tag/merge ke main), bukan perintah manual.
- **Rollback otomatis dan DIUJI**: dipulihkan versi sebelumnya di staging minimal
  sekali sebelum rilis pertama; rollback bukan dokumen, tapi prosedur yang bekerja.
- Versioning semver konsisten.
- Mobile/desktop: staged rollout (internal/beta track → production) + auto-update
  channel.

## 4. Observability & automation (sejak rilis, dipakai Maintenance)

- Monitoring + alerting sejak rilis pertama: error rate, latensi, resource.
- Dependency update bot (dependabot/renovate/setara): update diterima hanya bila
  pipeline hijau.
- Maintenance fix lewat branch kecil + PR → CI yang sama menilai setiap perubahan;
  kualitas tidak menurun seiring usia app.

## 5. Skeleton workflow CI (contoh, sesuaikan)

```yaml
# .github/workflows/ci.yml — sesuaikan bahasa/framework & platform CI
name: ci
on: [push, pull_request]
jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Install dependencies
        run: <package-manager> ci
      - name: Lint
        run: <lint-command>
      - name: Build
        run: <build-command>
      - name: Unit + integration tests
        run: <test-command>
      - name: Pricing-backend regression
        run: <test-command> tests/pricing tests/security
```

Ganti placeholder `<...>` sesuai proyek. Untuk non-GitHub, terjemahkan job yang sama
ke platform CI yang dipakai.
