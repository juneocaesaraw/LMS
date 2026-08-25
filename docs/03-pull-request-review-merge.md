# 3. Pull Request, Review, dan Merge

## Membuka Pull Request

Setelah branch di-push (lihat [02](./02-alur-kerja-harian.md#5-push-branch-ke-github)),
GitHub biasanya langsung menampilkan tombol **"Compare & pull request"** di
halaman utama repo. Kalau tidak muncul:

1. Buka repo di GitHub → tab **Pull requests** → **New pull request**.
2. **base**: `main` (branch tujuan). **compare**: branch yang baru di-push
   (branch sumber).
3. Isi judul dan deskripsi. Judul boleh disalin dari commit message utama.
   Deskripsi minimal menjawab: perubahan apa, kenapa, dan bagaimana cara
   ngetes-nya (langkah manual, karena project ini belum punya test runner
   otomatis — pengujian fungsional dilakukan manual, bukan lewat CI).
4. Kalau PR ini masih setengah jalan dan belum siap direview, buka sebagai
   **Draft pull request** dulu (opsi di sebelah tombol Create) supaya tim tahu
   belum perlu direview.
5. **Create pull request.**

Satu PR sebaiknya = satu branch = satu perubahan logis (satu fitur atau satu
bug fix). PR yang mencampur beberapa hal tidak terkait menyulitkan reviewer
dan menyulitkan rollback kalau salah satu bagian ternyata bermasalah.

## Proses review

Dengan tim 5 orang, minimal **satu approval dari orang lain** sebelum merge
sudah cukup untuk menangkap kesalahan tanpa memperlambat kerja. Cara minta
review:

1. Di halaman PR, panel kanan → **Reviewers** → pilih 1 orang (atau lebih
   untuk perubahan yang lebih besar/berisiko, misalnya menyentuh skema
   Prisma atau alur auth).
2. Reviewer membuka tab **Files changed**, baca diff, dan bisa:
   - Comment di baris tertentu (klik `+` di sisi kiri baris).
   - **Approve** kalau sudah oke digabung.
   - **Request changes** kalau ada yang harus diperbaiki dulu sebelum merge.
3. Penulis PR merespons comment, melakukan perbaikan di branch yang sama
   (commit dan push seperti biasa — PR otomatis ter-update, tidak perlu buka
   PR baru), lalu me-reply comment atau klik **Resolve conversation** setelah
   diperbaiki.
4. Setelah approve didapat dan tidak ada conversation yang masih terbuka,
   PR siap di-merge.

Yang perlu diperhatikan reviewer di repo ini secara khusus, karena bukan hal
yang otomatis terlihat dari diff kecil:

- Import di backend pakai ekstensi `.js` eksplisit meski sumbernya `.ts`
  (project ESM) — kalau reviewer melihat import tanpa `.js` di file baru,
  itu kemungkinan bug, bukan gaya penulisan bebas.
- `process.env` cuma boleh dibaca di `backend/src/config/index.ts`. Kalau ada
  modul lain membaca `process.env` langsung, tandai untuk diperbaiki.
- Respons API harus lewat `sendSuccess` / `sendError` di
  `utils/apiResponse.ts`, bukan `res.json(...)` manual.
- Kalau PR menyentuh `schema.prisma`, pastikan ada file migration yang
  menyertainya (`npm run db:migrate` dijalankan penulis PR sebelum push).
- String yang tampil ke user, dan commit message, konsisten Bahasa Indonesia.

### (Opsional, disiapkan terpisah) Branch protection

Supaya aturan "tidak ada commit langsung ke `main`" dan "wajib 1 approval"
benar-benar dipaksakan GitHub — bukan cuma disiplin masing-masing orang —
pemilik repo bisa mengaktifkan lewat **Settings → Branches → Add branch
protection rule** untuk `main`: centang **Require a pull request before
merging** dan **Require approvals** (minimal 1). Ini pengaturan di level
GitHub, dilakukan sekali oleh pemilik repo, bukan bagian dari alur kerja
harian kontributor — disebut di sini supaya tim tahu proteksi ini ada dan
kenapa push ke `main` akan ditolak kalau dicoba langsung.

## Menyelesaikan konflik

Konflik terjadi kalau baris yang sama di file yang sama diubah secara berbeda
di dua branch. Git tidak tahu versi mana yang benar, jadi berhenti dan minta
manusia memutuskan.

Paling sering muncul saat menjalankan `git merge main` di branch sendiri
(lihat [02](./02-alur-kerja-harian.md#menjaga-branch-tetap-sinkron-saat-main-berubah)),
atau saat GitHub menandai PR sebagai **"This branch has conflicts that must be
resolved."**

Langkah menyelesaikannya:

```bash
git checkout feat/rating-course
git merge main
```

Git akan menandai file yang konflik dan menyisipkan marker di dalamnya:

```
<<<<<<< HEAD
kode dari branch feat/rating-course (versi kamu)
=======
kode dari main (versi yang datang)
>>>>>>> main
```

1. Buka file itu di editor, putuskan versi mana yang benar — bisa salah satu,
   bisa gabungan keduanya, bisa ditulis ulang. Hapus ketiga baris marker
   (`<<<<<<<`, `=======`, `>>>>>>>`) setelah selesai.
2. Kalau konflik ada di `package-lock.json` atau `backend/package-lock.json`,
   cara paling aman adalah hapus file itu lalu jalankan ulang `npm install`
   di direktori terkait supaya lock file dibuat ulang secara konsisten,
   daripada mengedit manual.
3. Setelah semua konflik di semua file terselesaikan:

```bash
git add .
git commit
```

   (Git sudah menyiapkan commit message default untuk merge ini, biasanya
   cukup dipakai apa adanya.)

4. `git push`. PR di GitHub otomatis update dan tanda konflik hilang.

Kalau di tengah proses ternyata ingin membatalkan merge yang sedang berjalan
dan mulai ulang:

```bash
git merge --abort
```

## Strategi merge: Squash vs Merge commit

GitHub menawarkan tiga cara merge saat tombol **Merge pull request** ditekan:
**Create a merge commit**, **Squash and merge**, **Rebase and merge**.

Project ini pakai **Squash and merge** sebagai default. Alasannya: satu branch
biasanya berisi beberapa commit kecil ("wip", "fix typo", "coba lagi") selama
proses development — itu wajar dan tidak masalah di branch sendiri, tapi kalau
semuanya masuk ke `main` apa adanya, riwayat `main` jadi berisik dan sulit
dibaca saat suatu saat perlu `git log` atau `git blame` untuk cari kapan sebuah
perubahan masuk. Squash meringkas seluruh commit di satu PR menjadi **satu
commit** di `main`, dengan pesan yang bisa diedit ulang saat itu juga jadi satu
ringkasan yang jelas (judul PR biasanya sudah jadi default yang layak pakai).

Setelah merge, centang **Delete branch** (tombol yang muncul otomatis di
halaman PR setelah merge) supaya branch yang sudah selesai tidak menumpuk di
daftar branch remote.

Lanjut ke [04 — Referensi Cepat dan Troubleshooting](./04-referensi-dan-troubleshooting.md).
