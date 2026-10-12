> Versi: v1.0.0 | Status: disetujui | Menggantikan: -

# Platform ~ Aplikasi (Pasaree)

Folder ini adalah **indeks aplikasi** divisi Pasaree: tujuh repo kode yang hidup di
organisasi Coding-Skuy ([tim Pasaree](https://github.com/orgs/Coding-Skuy/teams/pasaree/repositories)),
ditautkan ke repo ini sebagai **git submodule**.

- `platform/versions/` — dokumen formal beku (BRD/PRD/FRD/FSD/SRS).
- `platform/apps/` — kode aplikasi (submodule, repo terpisah).

## Repo aplikasi

| Submodule | Repo | Peran | Stack |
|---|---|---|---|
| `app-kmp/` | [pasaree-app-kmp](https://github.com/Coding-Skuy/pasaree-app-kmp) | Aplikasi beli mobile (Navigation3) | Kotlin MPP / CMP |
| `web/` | [pasaree-web](https://github.com/Coding-Skuy/pasaree-web) | Web lapak | Bun + SvelteKit + TS |
| `backend-service/` | [pasaree-backend-service](https://github.com/Coding-Skuy/pasaree-backend-service) | Layanan API katalog, lapak, transaksi | Rust |
| `ai-models/` | [pasaree-ai-models](https://github.com/Coding-Skuy/pasaree-ai-models) | Model kurasi lapak | Python (training) + Rust (serving) |
| `data-pipeline/` | [pasaree-data-pipeline](https://github.com/Coding-Skuy/pasaree-data-pipeline) | Event katalog ke pipeline | — |
| `design/` | [pasaree-design](https://github.com/Coding-Skuy/pasaree-design) | Kurasi lapak (token, flow) | Figma |
| `infra-devops/` | [pasaree-infra-devops](https://github.com/Coding-Skuy/pasaree-infra-devops) | CI/CD deploy | CI/CD |

## Cara pakai

```bash
# clone beserta submodule
git clone --recurse-submodules https://github.com/Coding-Skuy/Pasaree-TownHall.git
# bila repo sudah ter-clone tanpa submodule:
git submodule update --init --recursive
# perbarui ke commit main terbaru tiap repo app:
git submodule update --remote
```

## Batasan

Indeks ini hanya menautkan repo. Kode dan riwayatnya tetap milik repo app masing-masing;
keputusan versi di tulis di `platform/versions/vX.Y.Z/`, bukan di sini.