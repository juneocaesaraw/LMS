# Panduan Kolaborasi Git & GitHub — Web LMS HIMSIKA

Repositori ini sebelumnya dikerjakan solo. Dokumen ini disiapkan karena tim akan
bertambah menjadi sekitar 5 orang, dan belum ada kesepakatan tertulis soal alur
kerja Git di project ini. Tujuannya: setiap kontributor baru bisa clone, kerja,
dan submit perubahan tanpa menabrak pekerjaan orang lain atau merusak `main`.

Baca urut sesuai nomor kalau baru pertama kali kolaborasi lewat Git/GitHub.
Kalau sudah terbiasa, langsung ke [04](./04-referensi-dan-troubleshooting.md)
untuk cheat sheet, dan ke [Aturan khusus repo ini](#aturan-khusus-repo-ini) di
bawah.

1. [Dasar Git dan Setup Awal](./01-dasar-git-dan-setup.md) — instal Git, buat
   akun GitHub, autentikasi, clone repo. Mulai di sini kalau belum pernah pakai
   Git sama sekali.
2. [Alur Kerja Harian](./02-alur-kerja-harian.md) — branch, `add`/`commit`/`push`,
   menjaga branch tetap sinkron dengan `main`.
3. [Pull Request, Review, dan Merge](./03-pull-request-review-merge.md) — buka
   PR di GitHub, proses review, cara menyelesaikan konflik, strategi merge.
4. [Referensi Cepat dan Troubleshooting](./04-referensi-dan-troubleshooting.md) —
   cheat sheet perintah, dan solusi untuk kesalahan yang paling sering terjadi.

## Aturan khusus repo ini

Ringkasan cepat, detail dan alasannya ada di dokumen masing-masing:

- **`main` adalah branch stabil dan siap deploy.** Railway men-deploy dari
  `main`. Tidak ada commit langsung ke `main` — semua perubahan lewat Pull
  Request.
- **Satu branch per fitur/perbaikan**, dibuat dari `main` terbaru. Format nama:
  `feat/nama-fitur`, `fix/nama-bug`, `docs/topik`, `refactor/area`,
  `chore/topik`. Lihat [02](./02-alur-kerja-harian.md#penamaan-branch).
- **Commit message pakai prefix**: `feat:`, `fix:`, `docs:`, `refactor:`,
  `chore:`, `style:`. Deskripsi boleh Bahasa Indonesia (konvensi repo ini
  memang begitu). Lihat [02](./02-alur-kerja-harian.md#format-commit-message).
- **Merge PR pakai Squash and merge**, bukan merge commit biasa. Lihat
  [03](./03-pull-request-review-merge.md#strategi-merge-squash-vs-merge-commit).
- **Husky pre-commit wajib aktif** di setiap clone — jalan otomatis setelah
  `npm install` di root. Jangan commit dengan `--no-verify`.
- **Jangan pernah commit file `.env`.** Yang di-commit adalah `.env.example`.
  Kalau butuh kredensial nyata (Google OAuth, SMTP, dsb.), minta ke pemilik
  repo lewat jalur privat (bukan chat grup, bukan issue GitHub).
- **Branch lama** (`dev`, `staging`, `production`, `refactor`,
  `blackbox-testing`, `iterasi-pertama`, `user-acceptance-testing`) adalah
  peninggalan fase solo development sebelum `main` distabilkan. Branch-branch
  itu tidak dipakai lagi di alur kerja ini — jangan branch dari sana, dan jangan
  buka PR ke sana. Pembersihannya menyusul terpisah, di luar dokumen ini.
