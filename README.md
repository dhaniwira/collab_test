# Collab Test

Uji coba workflow kolaborasi dengan Git (CLI) dan GitHub.

## Daftar Isi

- [Dasar-Dasar Git](#dasar-dasar-git)
- [Workflow Kolaborasi](#workflow-kolaborasi)
- [Panduan Pengelola (Maintainer)](#panduan-pengelola-maintainer)
- [Panduan Kelola Remote](#panduan-kelola-remote)
- [Panduan Semantic Commit Message](#panduan-semantic-commit-message)

## Dasar-Dasar Git

Git adalah sistem kontrol versi yang menyimpan riwayat perubahan file. Setiap perubahan yang di-commit tercatat sebagai snapshot, sehingga Anda bisa melihat, membandingkan, atau mengembalikan kondisi file kapan saja. GitHub adalah layanan hosting repository Git; Git bekerja di komputer Anda, sedangkan GitHub menyimpan salinan dan menjadi tempat kolaborasi.

Tiga area utama yang perlu dipahami:

- **Working directory** — file yang sedang Anda edit di folder proyek.
- **Staging area (index)** — daftar perubahan yang sudah ditandai dan akan masuk commit berikutnya. Diisi dengan `git add`.
- **Repository lokal** — riwayat commit yang tersimpan di folder `.git`.

Alur dasar: edit file → `git add` → `git commit`. Untuk berbagi, commit dikirim dengan `git push` ke remote, dan perubahan orang lain diambil dengan `git fetch`/`git pull`.

Istilah penting:

- **Commit** — catatan permanen berisi perubahan beserta pesan dan penulisnya, punya ID unik (hash).
- **Branch** — jalur pengembangan terpisah; memungkinkan Anda bekerja tanpa mengganggu `main`.
- **Merge** — menggabungkan riwayat dua branch menjadi satu.
- **Rebase** — memindahkan commit branch Anda di atas commit branch lain agar riwayat tampak lurus.
- **Remote** — alias untuk URL repository di server, misalnya `origin` (fork Anda) dan `upstream` (repository asli).
- **HEAD** — penunjuk ke commit/branch yang sedang aktif Anda kerjakan.

### Perintah Dasar

- **Membuat repository baru**
  ```bash
  git init   # mengubah folder saat ini menjadi repository Git baru
  ```
- **Menyalin repository dari GitHub**
  ```bash
  git clone https://github.com/<username>/<repo>.git   # mengunduh seluruh repository beserta riwayatnya
  ```
- **Melihat kondisi file**
  ```bash
  git status            # menampilkan file yang berubah, ter-stage, atau belum dilacak
  git status -s         # versi ringkas satu baris per file
  ```
- **Menandai perubahan untuk commit**
  ```bash
  git add <file>        # menandai satu file
  git add .             # menandai semua perubahan di folder saat ini
  ```
- **Menyimpan perubahan**
  ```bash
  git commit -m "feat: deskripsi singkat"   # membuat commit dengan pesan langsung
  ```
- **Melihat riwayat commit**
  ```bash
  git log --oneline         # daftar commit ringkas (hash + pesan)
  git log --oneline --graph # ditambah grafik branch dan merge
  ```
- **Melihat perubahan**
  ```bash
  git diff              # perubahan yang belum di-stage
  git diff --staged     # perubahan yang sudah di-stage dan akan di-commit
  git diff main...feat  # perbedaan branch fitur dibanding main (dari merge base)
  ```
- **Mengelola branch**
  ```bash
  git branch                      # daftar branch lokal (* menandai branch aktif)
  git checkout -b feat/nama-fitur # membuat branch baru sekaligus berpindah ke sana
  git checkout main               # berpindah ke branch main
  git switch main                 # alternatif modern untuk berpindah branch
  git branch -d feat/nama-fitur   # menghapus branch lokal yang sudah ter-merge
  ```
- **Berbagi dan mengambil perubahan**
  ```bash
  git push -u origin feat/nama-fitur   # mengunggah branch dan menyetel tracking ke origin
  git fetch origin                     # mengambil commit terbaru tanpa mengubah branch kerja
  git pull origin main                 # fetch lalu merge perubahan remote ke branch aktif
  ```
- **Membatalkan perubahan**
  ```bash
  git restore <file>              # membuang perubahan pada file yang belum di-stage
  git restore --staged <file>     # mengeluarkan file dari staging tanpa menghapus perubahannya
  git commit --amend              # mengubah commit terakhir (misalnya memperbaiki pesan)
  ```
- **Menyimpan sementara pekerjaan**
  ```bash
  git stash        # menyimpan perubahan yang belum selesai agar working directory bersih
  git stash pop    # mengembalikan perubahan yang terakhir di-stash
  ```

> **Tips:** jalankan `git status` sebelum dan sesudah perintah penting untuk memastikan kondisi repository sesuai harapan. Gunakan `git help <perintah>` untuk melihat dokumentasi lengkap.

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

Istilah penting yang dipakai di bagian ini:

- **Repository asli (upstream/origin milik pengelola)** — repository utama tempat perubahan akhirnya harus masuk.
- **Fork kontributor** — salinan repository di akun GitHub kontributor; PR dibuat dari branch di fork ini.
- **Remote** — nama pendek untuk sebuah URL repository di komputer Anda. Pengelola bisa menambahkan fork kontributor sebagai remote agar bisa `fetch` dari sana.
- **Ref PR** — GitHub menyimpan setiap PR sebagai ref khusus `refs/pull/<nomor>/head`, sehingga commit PR bisa diambil langsung tanpa menambah remote.
- **Branch review** — branch lokal sementara untuk meninjau PR secara terpisah sebelum masuk ke `main`.

Prinsipnya: **jangan merge sebelum meninjau**, dan **selalu sinkronkan `main` lokal dengan repository asli** sebelum merge agar hasilnya tidak menimpa perubahan terbaru. Detail tiap langkah dijelaskan di bawah.

1. **Lihat pull request yang masuk**  
   Buka tab **Pull requests** di halaman repository GitHub untuk melihat daftar PR, deskripsi, commit, dan file yang berubah. Langkah ini dilakukan sepenuhnya di GitHub, tanpa perintah Git.
2. **Tambahkan repository kontributor sebagai remote** (cukup sekali per kontributor)  
   ```bash
   git remote add <nama-kontributor> https://github.com/<username-kontributor>/<repo>.git   # mendaftarkan fork kontributor sebagai remote
   ```
   Gunakan nama yang mudah dikenali, misalnya `budi` atau `kontributor1`. Setelah ditambahkan, branch kontributor bisa diakses sebagai `<nama-kontributor>/<nama-branch>`. Periksa dengan `git remote -v`; bila URL berubah, gunakan `git remote set-url <nama-kontributor> <url-baru>`.
3. **Ambil perubahan dari fork kontributor**  
   ```bash
   git fetch <nama-kontributor>        # mengunduh branch kontributor tanpa mengubah branch lokal
   ```
   `fetch` hanya mengunduh; branch kerja Anda tidak berubah. Setelah itu branch kontributor tersedia sebagai remote-tracking branch, misalnya `<nama-kontributor>/feat/nama-fitur`.
   Cara cepat tanpa menambah remote: ambil langsung ref PR dari repository asli.
   ```bash
   git fetch origin pull/<nomor-pr>/head:pr-<nomor-pr>   # mengambil commit PR ke branch lokal pr-<nomor-pr>
   ```
   Ref `refs/pull/<nomor>/head` selalu menunjuk commit terbaru PR, jadi cara ini otomatis mengikuti pembaruan yang di-push kontributor.
4. **Buat branch review dari branch fitur kontributor**  
   ```bash
   git checkout -b review/feat-nama-fitur <nama-kontributor>/feat/nama-fitur   # membuat branch lokal untuk meninjau
   ```
   Branch review memisahkan pekerjaan PR dari `main`, sehingga Anda bisa bereksperimen, menyelesaikan konflik, atau membatalkan tanpa mengganggu branch utama. Jika memakai cara ref PR pada langkah 3, ganti sumbernya menjadi `pr-<nomor-pr>`.
5. **Tinjau perubahan sebelum merge**  
   ```bash
   git diff main...review/feat-nama-fitur   # melihat semua perubahan PR dibanding branch main
   git log --oneline main..review/feat-nama-fitur   # melihat daftar commit yang akan masuk
   ```
   Tiga titik (`...`) membandingkan dari titik temu (merge base), sehingga diff hanya menampilkan perubahan milik PR, bukan perubahan lain di `main`. Dua titik (`..`) pada `git log` menampilkan commit yang ada di branch review tetapi belum ada di `main`.
   Jalankan test/lint proyek untuk memastikan perubahan aman. Perhatikan juga kualitas kode, penamaan, dan apakah perubahan sesuai deskripsi PR. Jika perlu perbaikan, minta kontributor memperbarui branch-nya, lalu ulangi langkah 3-4 agar branch review memuat commit terbaru.
6. **Sinkronkan branch utama lalu merge**  
   ```bash
   git checkout main                   # berpindah ke branch utama
   git pull origin main                # mengambil sekaligus menggabungkan perubahan terbaru dari repository asli
   git merge --no-ff review/feat-nama-fitur   # menggabungkan branch fitur dengan commit merge eksplisit
   ```
   Sinkronisasi dulu agar `main` lokal sama dengan repository asli; ini mencegah merge yang menimpa perubahan terbaru. Opsi `--no-ff` memaksa commit merge tetap dibuat meskipun sebenarnya bisa *fast-forward*, sehingga riwayat menunjukkan kapan PR digabungkan. Tanpa `--no-ff`, Git bisa sekadar memajukan pointer `main` dan jejak merge-nya hilang.
7. **Push hasil merge ke repository asli**  
   ```bash
   git push origin main                # mengunggah branch main yang sudah berisi perubahan PR
   ```
   GitHub otomatis menandai PR sebagai *merged* setelah commit-nya masuk ke branch tujuan. Bila push ditolak karena `main` remote sudah bergerak, jangan force-push; ulangi `git pull origin main`, selesaikan konflik bila ada, lalu push lagi.
8. **Bersihkan branch setelah merge**  
   ```bash
   git branch -d review/feat-nama-fitur         # menghapus branch review lokal yang sudah tidak dipakai
   git push <nama-kontributor> --delete feat/nama-fitur   # menghapus branch fitur di fork kontributor
   ```
   `-d` hanya menghapus branch yang sudah ter-merge; jika Git menolak, berarti ada commit yang belum masuk `main` dan perlu diperiksa dulu. Menghapus branch di fork kontributor bersifat opsional dan sebaiknya dilakukan setelah PR benar-benar selesai. Alternatifnya, kontributor menghapusnya sendiri, atau Anda cukup menekan tombol **Delete branch** pada halaman PR di GitHub setelah merge.

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
