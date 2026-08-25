# 1. Dasar Git dan Setup Awal

## Konsep yang perlu dipahami dulu

Git bekerja dengan tiga area di komputer, plus satu tempat di server. Kalau ini
belum jelas, sisa dokumen ini akan terasa seperti hafalan perintah tanpa alasan.

| Area | Isi | Perintah yang memindahkan ke sana |
|---|---|---|
| **Working directory** | File yang sedang diedit di editor | (langsung, saat menyimpan file) |
| **Staging area** (index) | File yang ditandai "siap dimasukkan ke commit berikutnya" | `git add` |
| **Local repository** | Riwayat commit di komputer sendiri | `git commit` |
| **Remote (GitHub)** | Riwayat commit yang tersimpan di server, bisa diakses semua orang di tim | `git push` |

Kenapa ada staging area, bukan langsung working directory → commit? Supaya bisa
memilih sebagian file untuk di-commit, sementara file lain yang belum selesai
tetap di working directory. Di project ini staging area jarang dipakai secara
manual karena `git add .` cukup untuk kebanyakan kasus, tapi penting untuk tahu
bahwa `git add` **tidak** mengirim apa pun ke GitHub — itu baru terjadi saat
`git push`.

Istilah lain yang akan sering muncul:

- **Branch**: cabang riwayat commit yang independen. Bisa dikerjakan paralel
  tanpa saling menimpa sampai digabung (merge).
- **Remote**: alias untuk URL repository di server. Nama defaultnya `origin`.
- **Clone**: salinan penuh repository (semua riwayat commit, semua branch)
  dari remote ke komputer lokal.
- **Pull Request (PR)**: fitur GitHub (bukan Git) untuk mengajukan agar isi
  satu branch digabungkan ke branch lain, lengkap dengan ruang diskusi dan
  review sebelum digabung.

## Instal Git

Cek apakah sudah ada:

```bash
git --version
```

Kalau belum ada:

- **Windows**: instal [Git for Windows](https://git-scm.com/download/win), lalu
  pakai Git Bash atau terminal WSL.
- **WSL2 / Linux**: `sudo apt update && sudo apt install git`
- **macOS**: `brew install git`, atau lewat Xcode Command Line Tools.

## Konfigurasi identitas

Setiap commit menyimpan nama dan email pembuatnya. Set sekali per komputer:

```bash
git config --global user.name "Nama Lengkap"
git config --global user.email "email-yang-terdaftar-di-github@example.com"
```

Emailnya harus sama dengan email akun GitHub (atau salah satu email terverifikasi
di akun itu), supaya commit muncul terhubung ke profil GitHub yang benar di
riwayat PR — kalau tidak cocok, commit tetap jalan tapi tercatat sebagai
kontributor anonim di GitHub.

## Autentikasi ke GitHub

GitHub tidak lagi menerima login pakai password biasa lewat HTTPS. Dua opsi:

**Opsi A — Personal Access Token (PAT), lebih sederhana untuk pemula:**

1. GitHub → Settings → Developer settings → Personal access tokens → Tokens
   (classic) → Generate new token.
2. Scope minimal: `repo`.
3. Saat `git push` pertama kali, gunakan token ini sebagai password saat
   diminta (username tetap username GitHub).
4. Git akan menyimpan token lewat credential helper setelah dipakai sekali
   (di WSL biasanya `git config --global credential.helper store` atau
   `libsecret`), jadi tidak diminta ulang setiap push.

**Opsi B — SSH key, lebih nyaman jangka panjang, sekali setup:**

```bash
ssh-keygen -t ed25519 -C "email-yang-terdaftar-di-github@example.com"
cat ~/.ssh/id_ed25519.pub
```

Tempel output-nya ke GitHub → Settings → SSH and GPG keys → New SSH key. Setelah
itu clone pakai URL `git@github.com:...` bukan `https://github.com/...`.

Pilih salah satu saja. Kalau tim ini baru pertama kali pakai GitHub, PAT lebih
cepat dipahami; SSH key lebih nyaman kalau sudah terbiasa dan ingin auth tanpa
diminta token/password sama sekali.

## Undangan ke repository

Repo ini privat, jadi setiap kontributor harus diundang lebih dulu oleh pemilik
repo: GitHub → repo `web-lms-himsika` → Settings → Collaborators and teams →
Add people. Tanpa undangan ini, clone dan push akan ditolak walau kredensial
Git sudah benar.

## Clone repository

```bash
git clone https://github.com/relystryx/web-lms-himsika.git
cd web-lms-himsika
```

(Ganti URL dengan versi SSH kalau memilih Opsi B di atas.)

Setelah clone, lakukan setup project seperti biasa (lihat `README.md` repo
ini — bagian Setup Development): `npm install` di root **dan** di
`backend/` **dan** di `frontend/`, salin seluruh `.env.example` menjadi `.env`,
lalu isi nilainya. `npm install` di root juga yang mengaktifkan Husky
pre-commit hook lewat script `prepare` — tanpa langkah ini, lint-staged tidak
akan jalan otomatis saat commit di komputer tersebut.

Lanjut ke [02 — Alur Kerja Harian](./02-alur-kerja-harian.md).
