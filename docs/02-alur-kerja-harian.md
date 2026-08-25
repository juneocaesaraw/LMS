# 2. Alur Kerja Harian

## Kenapa tidak commit langsung ke `main`

`main` adalah branch yang di-deploy Railway. Commit langsung ke `main` berarti
setiap kesalahan (typo, bug, migration yang belum siap) langsung mengarah ke
production, dan lima orang bisa saling menimpa pekerjaan orang lain di branch
yang sama tanpa ada titik review. Solusinya: setiap orang kerja di branch
sendiri, dan `main` hanya menerima perubahan lewat Pull Request yang sudah
di-review (lihat [03](./03-pull-request-review-merge.md)).

## Alur satu putaran kerja

```
1. Sinkronkan main lokal dengan remote
2. Buat branch baru dari main
3. Edit kode
4. git add → git commit  (ulangi sesuai kebutuhan)
5. git push branch ke GitHub
6. Buka Pull Request
7. Review, revisi kalau perlu
8. Merge lewat GitHub, branch feature dihapus
9. Kembali ke langkah 1 untuk kerjaan berikutnya
```

### 1. Sinkronkan `main` lokal

Selalu mulai kerjaan baru dari `main` yang paling baru, bukan dari `main` versi
lama yang kebetulan masih ada di komputer:

```bash
git checkout main
git pull origin main
```

`git pull` sebenarnya adalah dua langkah sekaligus: `git fetch` (ambil riwayat
terbaru dari remote tanpa mengubah file lokal) lalu `git merge` (gabungkan ke
branch yang sedang aktif). Kalau ingin cek dulu ada perubahan apa sebelum
digabung, bisa `git fetch` saja lalu `git log main..origin/main`.

### 2. Buat branch baru

```bash
git checkout -b feat/rating-course
```

`-b` berarti buat branch baru sekaligus pindah ke sana. (`git switch -c` adalah
perintah yang lebih baru untuk hal yang sama — boleh pakai salah satu, project
ini tidak mewajibkan salah satunya.)

### Penamaan branch

Format: `<tipe>/<deskripsi-singkat-kebab-case>`.

| Tipe | Dipakai untuk |
|---|---|
| `feat/` | Fitur baru |
| `fix/` | Perbaikan bug |
| `docs/` | Dokumentasi saja, tanpa ubah kode |
| `refactor/` | Ubah struktur kode tanpa ubah perilaku |
| `chore/` | Tugas perawatan: dependency, config, tooling |

Contoh: `feat/rating-course`, `fix/login-google-redirect`,
`docs/panduan-github`. Deskripsi singkat dan dalam Bahasa Indonesia atau
Inggris, konsisten dengan gaya commit message di repo ini — yang penting
tipe di depan selalu benar, karena dari situ orang lain langsung tahu isi
branch tanpa perlu membuka PR-nya.

### 3–4. Edit kode, lalu `add` dan `commit`

```bash
git status              # lihat file apa saja yang berubah
git add backend/src/modules/rating/
git commit -m "feat: tambah modul rating course"
```

Beberapa catatan:

- `git add .` menambahkan **semua** file yang berubah di direktori saat ini,
  termasuk yang tidak sengaja tersentuh. Lebih aman cek dengan `git status`
  dulu, terutama sebelum commit pertama di branch baru.
- Commit sebaiknya kecil dan fokus satu perubahan logis, bukan satu commit
  raksasa di akhir hari. Riwayat commit yang granular memudahkan review dan
  memudahkan `git revert` kalau ada satu bagian yang perlu dibatalkan tanpa
  membatalkan semuanya.
- Husky pre-commit akan menjalankan Prettier dan ESLint otomatis pada file
  yang di-stage. Kalau commit gagal karena error lint, perbaiki dulu — jangan
  pakai `git commit --no-verify` untuk melewatinya (aturan ini berlaku untuk
  semua kontributor, bukan cuma pemilik repo).

#### Format commit message

Prefix di depan, deskripsi singkat setelahnya:

```
feat: tambah endpoint rating course
fix: perbaiki validasi email pada registrasi
docs: tambah panduan kolaborasi github
refactor: pindahkan validasi ke validator terpisah
chore: update dependency prisma ke v6.2
```

Prefix yang dipakai: `feat`, `fix`, `docs`, `refactor`, `chore`, `style`
(perubahan format, tanpa ubah logika). Deskripsi boleh Bahasa Indonesia —
itu konvensi commit message yang sudah dipakai di riwayat repo ini.

### 5. Push branch ke GitHub

Push pertama kali dari branch baru perlu `-u` (atau `--set-upstream`) supaya
Git tahu branch lokal ini terhubung ke branch remote yang mana:

```bash
git push -u origin feat/rating-course
```

Setelah itu, push berikutnya dari branch yang sama cukup:

```bash
git push
```

### Menjaga branch tetap sinkron saat `main` berubah

Kalau kerja di satu branch berlangsung lebih dari sehari, kemungkinan besar
`main` sudah berubah karena PR orang lain sudah di-merge. Sebelum melanjutkan
kerja atau sebelum membuka PR, gabungkan perubahan `main` terbaru ke branch
sendiri:

```bash
git checkout main
git pull origin main
git checkout feat/rating-course
git merge main
```

Ini memakai `git merge`, bukan `git rebase`. Untuk tim yang baru mulai
kolaborasi lewat Git, `merge` lebih aman: riwayat commit tidak ditulis ulang,
jadi tidak ada risiko force-push menimpa pekerjaan orang lain. `rebase`
menghasilkan riwayat yang lebih rapi (linear), tapi menulis ulang hash commit
dan menuntut `git push --force` — sengaja tidak dipakai sebagai default di
project ini sampai tim terbiasa dulu dengan alur dasarnya.

Kalau `git merge main` menghasilkan konflik, lihat cara menyelesaikannya di
[03 — Menyelesaikan Konflik](./03-pull-request-review-merge.md#menyelesaikan-konflik).

Lanjut ke [03 — Pull Request, Review, dan Merge](./03-pull-request-review-merge.md).
