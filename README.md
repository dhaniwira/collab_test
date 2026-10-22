# Collab Test

Uji coba workflow kolaborasi dengan Git (CLI) dan GitHub.

## Daftar Isi

- [Workflow Kolaborasi](#workflow-kolaborasi)
- [Panduan Kelola Remote](#panduan-kelola-remote)
- [Panduan Semantic Commit Message](#panduan-semantic-commit-message)

## Workflow Kolaborasi

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
