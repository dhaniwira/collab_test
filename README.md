# Collab Test

Uji coba workflow kolaborasi dengan Git (CLI) dan GitHub.

## Daftar Isi

- [Workflow Kolaborasi](#workflow-kolaborasi)
- [Panduan Pengelola (Maintainer)](#panduan-pengelola-maintainer)
- [Panduan Kelola Remote](#panduan-kelola-remote)
- [Panduan Semantic Commit Message](#panduan-semantic-commit-message)

## Workflow Kolaborasi

Alur di bawah ini ditulis dari sudut pandang kontributor. Bila Anda pemilik repository yang menerima PR, lihat [Panduan Pengelola (Maintainer)](#panduan-pengelola-maintainer).

1. **Fork repository di GitHub**  
   Buka repository asli (milik pemilik proyek) di GitHub, lalu klik **Fork** untuk membuat salinan di akun Anda.
2. **Clone repository hasil fork**  
   ```bash
   git clone https://github.com/<username>/<repo>.git   # menyalin repository GitHub ke komputer
   cd <repo>                                            # masuk ke folder hasil clone
   ```
3. **Buat branch baru untuk pengajuan perubahan/fitur**  
   ```bash
   git checkout -b feat/nama-fitur   # membuat branch baru sekaligus berpindah ke branch tersebut
   ```
4. **Lakukan perubahan**  
   Edit, tambah, atau hapus file sesuai kebutuhan.
5. **Commit perubahan**  
   ```bash
   git add .                              # menandai semua perubahan agar masuk commit
   git commit -m "feat: menambahkan fitur x"   # menyimpan perubahan dengan pesan commit
   ```
6. **Push ke origin dengan branch fitur**  
   ```bash
   git push -u origin feat/nama-fitur   # mengunggah branch fitur ke GitHub (-u menyetel tracking)
   ```
7. **Ajukan pull request di GitHub**  
   Buka repo di GitHub, pilih branch fitur, lalu klik **Compare & pull request**.
8. **Lakukan sinkronisasi dengan pull/fetch**  
   Pastikan remote `upstream` sudah terdaftar (cukup sekali, lihat [Panduan Kelola Remote](#panduan-kelola-remote)):
   ```bash
   git remote add upstream https://github.com/<pemilik-asli>/<repo>.git   # mendaftarkan repository asli sebagai upstream
   ```
   Lalu sinkronkan branch utama dan branch fitur:
   ```bash
   git fetch upstream                  # mengambil perubahan terbaru dari repository asli tanpa menggabungkan
   git checkout main                   # berpindah ke branch main
   git merge upstream/main             # menggabungkan upstream/main ke branch main lokal
   git checkout feat/nama-fitur        # kembali ke branch fitur
   git rebase upstream/main            # memindahkan commit fitur di atas perubahan terbaru (hindari konflik PR)
   ```
   Atau gunakan `git pull --rebase upstream main` untuk branch yang sudah di-tracking.
9. **Ulangi langkah 3-8 sampai selesai**  
   Setelah PR di-merge atau perubahan selesai, ulangi dari pembuatan branch baru.

> **Tips konflik:** bila `git rebase` atau `git merge` menemukan konflik, buka file yang ditandai `<<<<<<<`, rapikan isinya, lalu jalankan `git add <file>` dan `git rebase --continue` (atau `git commit`). Gunakan `git rebase --abort` untuk membatalkan proses.

## Panduan Pengelola (Maintainer)

Bagian ini adalah sudut pandang pemilik repository asli yang menerima pull request (PR). Alurnya berkebalikan dengan kontributor: pengelola mengambil branch dari repository kontributor, meninjaunya, lalu menggabungkannya ke branch utama.

1. **Lihat pull request yang masuk**  
   Buka tab **Pull requests** di halaman repository GitHub untuk melihat daftar PR, deskripsi, commit, dan file yang berubah. Langkah ini dilakukan sepenuhnya di GitHub, tanpa perintah Git.
2. **Tambahkan repository kontributor sebagai remote** (cukup sekali per kontributor)  
   ```bash
   git remote add <nama-kontributor> https://github.com/<username-kontributor>/<repo>.git   # mendaftarkan fork kontributor sebagai remote
   ```
3. **Ambil perubahan dari fork kontributor**  
   ```bash
   git fetch <nama-kontributor>        # mengunduh branch kontributor tanpa mengubah branch lokal
   ```
   Cara cepat tanpa menambah remote: ambil langsung ref PR dari repository asli.
   ```bash
   git fetch origin pull/<nomor-pr>/head:pr-<nomor-pr>   # mengambil commit PR ke branch lokal pr-<nomor-pr>
   ```
4. **Buat branch review dari branch fitur kontributor**  
   ```bash
   git checkout -b review/feat-nama-fitur <nama-kontributor>/feat/nama-fitur   # membuat branch lokal untuk meninjau
   ```
5. **Tinjau perubahan sebelum merge**  
   ```bash
   git diff main...review/feat-nama-fitur   # melihat semua perubahan PR dibanding branch main
   git log --oneline main..review/feat-nama-fitur   # melihat daftar commit yang akan masuk
   ```
   Jalankan test/lint proyek untuk memastikan perubahan aman.
6. **Sinkronkan branch utama lalu merge**  
   ```bash
   git checkout main                   # berpindah ke branch utama
   git pull origin main                # mengambil sekaligus menggabungkan perubahan terbaru dari repository asli
   git merge --no-ff review/feat-nama-fitur   # menggabungkan branch fitur dengan commit merge eksplisit
   ```
7. **Push hasil merge ke repository asli**  
   ```bash
   git push origin main                # mengunggah branch main yang sudah berisi perubahan PR
   ```
   GitHub otomatis menandai PR sebagai *merged* setelah commit-nya masuk ke branch tujuan.
8. **Bersihkan branch setelah merge**  
   ```bash
   git branch -d review/feat-nama-fitur         # menghapus branch review lokal yang sudah tidak dipakai
   git push <nama-kontributor> --delete feat/nama-fitur   # menghapus branch fitur di fork kontributor
   ```

> **Bila terjadi konflik saat merge:** minta kontributor melakukan `git rebase` terhadap `upstream/main` dan push ulang (lihat [Tips konflik](#workflow-kolaborasi)), atau selesaikan konflik langsung di branch review lalu commit hasilnya sebelum merge.

## Panduan Kelola Remote

Remote adalah alamat repository Git yang tersimpan di komputer Anda. `origin` adalah fork Anda sendiri, sedangkan `upstream` adalah repository asli yang Anda fork.

- **Lihat daftar remote**
  ```bash
  git remote -v   # menampilkan nama dan URL remote yang terdaftar
  ```
- **Tambahkan remote upstream** (cukup sekali)
  ```bash
  git remote add upstream https://github.com/<pemilik-asli>/<repo>.git   # mendaftarkan repository asli sebagai upstream
  ```
- **Ganti alamat remote**
  ```bash
  git remote set-url upstream https://github.com/<pemilik-asli>/<repo>.git   # mengubah URL remote upstream
  ```
- **Hapus remote**
  ```bash
  git remote remove upstream   # menghapus remote upstream dari daftar
  ```
- **Ambil perubahan terbaru tanpa menggabungkan**
  ```bash
  git fetch upstream   # mengunduh perubahan upstream ke lokal tanpa mengubah branch kerja
  ```
- **Gabungkan perubahan upstream ke branch lokal**
  ```bash
  git checkout main          # berpindah ke branch main
  git merge upstream/main    # menggabungkan upstream/main ke branch main lokal
  ```

## Panduan Semantic Commit Message

Gunakan format `type: deskripsi singkat`:

- `feat:` menambahkan fitur baru
- `fix:` memperbaiki bug
- `docs:` perubahan dokumentasi
- `refac:` refaktor kode tanpa mengubah perilaku
- `chore:` tugas rutin/pemeliharaan (build, dependency, dll.)

Contoh:

```bash
git commit -m "feat: menambahkan halaman login"      # fitur baru
git commit -m "fix: memperbaiki validasi form"       # perbaikan bug
git commit -m "docs: memperbarui README"             # perubahan dokumentasi
git commit -m "refac: menyederhanakan fungsi auth"   # refaktor tanpa ubah perilaku
git commit -m "chore: update dependency"             # tugas rutin/pemeliharaan
```
