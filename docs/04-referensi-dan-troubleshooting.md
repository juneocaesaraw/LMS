# 4. Referensi Cepat dan Troubleshooting

## Cheat sheet perintah

| Perintah | Fungsi |
|---|---|
| `git status` | Lihat file yang berubah, staged/belum, branch aktif |
| `git diff` | Lihat isi perubahan yang belum di-`add` |
| `git diff --staged` | Lihat isi perubahan yang sudah di-`add`, belum commit |
| `git add <file>` | Tandai file untuk commit berikutnya |
| `git add .` | Tandai semua file yang berubah di direktori saat ini |
| `git commit -m "pesan"` | Simpan perubahan yang di-stage sebagai commit |
| `git push` | Kirim commit ke remote (branch yang sedang aktif) |
| `git push -u origin <branch>` | Push pertama kali dari branch baru |
| `git pull origin main` | Ambil dan gabungkan perubahan terbaru dari `main` |
| `git fetch` | Ambil riwayat terbaru dari remote tanpa mengubah file lokal |
| `git checkout -b <branch>` | Buat branch baru dan pindah ke sana |
| `git checkout <branch>` | Pindah ke branch yang sudah ada |
| `git branch` | Daftar branch lokal |
| `git branch -a` | Daftar branch lokal + remote |
| `git log --oneline -10` | 10 commit terakhir, ringkas |
| `git log --oneline --graph --all` | Riwayat semua branch dalam bentuk pohon |
| `git stash` | Simpan sementara perubahan yang belum di-commit, tanpa commit |
| `git stash pop` | Kembalikan perubahan yang di-stash |

## Troubleshooting

### "Aku sudah `git add .` tapi belum sempat commit, ternyata harus pindah branch dulu"

```bash
git stash
git checkout main
# ...kerjaan lain...
git checkout feat/rating-course
git stash pop
```

`stash` menyimpan perubahan yang belum di-commit di tempat terpisah, supaya
bisa pindah branch dengan working directory yang bersih, lalu dikembalikan
lagi nanti.

### "Salah commit ke branch yang keliru, belum di-push"

```bash
git reset --soft HEAD~1     # batalkan commit terakhir, perubahan tetap ada
git stash
git checkout branch-yang-benar
git stash pop
git add .
git commit -m "pesan yang sama"
```

### "Sudah commit, mau ubah pesan commit-nya, belum di-push"

```bash
git commit --amend -m "pesan baru"
```

Jangan pakai `--amend` untuk commit yang **sudah** di-push, apalagi kalau
sudah ada di PR yang sedang direview — itu menulis ulang hash commit dan
memaksa `push --force`, yang bisa membingungkan reviewer karena riwayat yang
sedang mereka baca berubah. Untuk commit yang sudah di-push, tambah commit
baru untuk memperbaikinya.

### "Tidak sengaja mau commit file `.env`"

```bash
git status                  # pastikan .env muncul di daftar "not staged"
```

Kalau `.env` sampai ter-`add`:

```bash
git restore --staged .env
```

Kalau `.env` sudah ter-commit (tapi **belum di-push**):

```bash
git reset --soft HEAD~1
git restore --staged backend/.env
git commit -m "pesan commit yang sama, tanpa .env"
```

Kalau `.env` sudah ter-**push**: anggap kredensial di dalamnya bocor.
Ganti (rotate) semua secret yang ada di file itu — JWT secret, kredensial
SMTP, Google OAuth client secret, dsb — jangan cuma menghapus file dari
commit berikutnya, karena riwayat lama tetap menyimpannya. Ini situasi yang
harus dikoordinasikan dengan pemilik repo, bukan diselesaikan sendiri.

### "`git pull` menolak karena ada perubahan lokal yang belum di-commit"

```
error: Your local changes to the following files would be overwritten by merge
```

Commit dulu perubahan lokal, atau `git stash` kalau belum siap commit, baru
`git pull` lagi.

### "PR menampilkan lebih banyak file berubah dari yang aku edit"

Biasanya branch belum sinkron dengan `main` — PR ikut menampilkan perubahan
`main` yang belum masuk ke branch ini. Jalankan langkah sinkronisasi di
[02](./02-alur-kerja-harian.md#menjaga-branch-tetap-sinkron-saat-main-berubah).

### "Mau buang semua perubahan lokal yang belum di-commit, mulai bersih dari commit terakhir"

```bash
git status              # cek dulu, pastikan memang tidak butuh apa pun di sini
git restore .           # file yang sudah tracked, kembalikan ke versi commit terakhir
git clean -fd           # hapus file baru yang belum pernah di-add (untracked)
```

Perintah ini menghapus perubahan secara permanen — tidak ada undo. Jalankan
`git status` dulu dan pastikan memang tidak ada pekerjaan yang masih dibutuhkan
di working directory sebelum menjalankannya.

### "Husky pre-commit tidak jalan sama sekali di komputerku"

Kemungkinan `npm install` belum pernah dijalankan di **root** repo (bukan cuma
di `backend/` dan `frontend/`) — script `prepare` yang mengaktifkan Husky ada
di `package.json` root. Jalankan `npm install` di root, lalu coba commit lagi.
