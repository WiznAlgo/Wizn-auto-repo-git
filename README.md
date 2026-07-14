# repo.sh — GitHub Repo Manager

Website statis (murni HTML/CSS/JS, tanpa backend) untuk membuat, mengedit,
merename, dan menghapus repo GitHub dengan mudah.

## Cara deploy ke Vercel

**Opsi A — lewat GitHub (paling gampang untuk update ke depannya):**
1. Push folder ini ke repo GitHub baru.
2. Buka [vercel.com/new](https://vercel.com/new), import repo tersebut.
3. Framework preset: pilih **Other** (Vercel akan otomatis serve `index.html` sebagai static site). Tidak perlu build command, tidak perlu environment variable apapun.
4. Klik Deploy. Selesai — dapat URL seperti `nama-project.vercel.app`.

**Opsi B — lewat Vercel CLI (langsung dari folder ini):**
```bash
npm install -g vercel
cd repo-manager-vercel
vercel --prod
```

## Kenapa tidak ada Personal Access Token di environment variable?

Ini sengaja. Karena tujuannya website ini bisa dipakai orang lain juga, kalau
tokenmu ditaruh di environment variable Vercel, itu berarti **semua orang yang
buka website akan pakai akun GitHub kamu** — mereka bisa bikin/hapus repo di
akunmu sendiri. Jelas bukan itu yang diinginkan.

Karena itu website ini didesain 100% jalan di sisi browser (client-side):
- Setiap orang yang buka website ini login pakai **Personal Access Token
  miliknya sendiri**.
- Token itu hanya disimpan di `localStorage` browser masing-masing orang
  (tidak dikirim ke Vercel, tidak ada database, tidak ada server yang
  memprosesnya).
- Semua request (buat repo, edit file, hapus, rename) dikirim langsung dari
  browser pengguna ke `api.github.com` memakai token mereka sendiri.

Efeknya: kamu sebagai pemilik deployment **tidak pernah melihat token orang
lain**, dan setiap orang hanya bisa mengatur repo di akun GitHub mereka
sendiri — bukan akunmu.

## Fitur

- Login dengan Personal Access Token (scope `repo`), opsional "ingat login"
  supaya tidak perlu login ulang tiap buka (tersimpan di browser, hilang
  kalau cache/data browser dibersihkan).
- Buat repo baru, dengan opsi auto-init sehingga langsung ada 1 initial
  commit (README.md).
- Rename repo.
- Hapus repo (dengan konfirmasi ketik nama repo).
- **Folder tree expand-in-place** — klik folder untuk buka/tutup isinya
  langsung di tempat (indentasi bertambah per level), tanpa pindah halaman.
  Klik folder juga menjadikannya "target" untuk file baru/upload berikutnya.
- Edit isi file lalu commit — setiap simpan menghasilkan tepat 1 commit.
- Buat file baru, hapus file individual.
- **Multi-select file** (checkbox) lalu hapus semuanya sekaligus dalam
  **1 commit** (pakai Git Data API: blobs → tree → commit → update ref).
- **Upload file biasa** (bisa multi-file sekaligus, 1 commit untuk semuanya)
  dan **upload .zip** — isi zip otomatis diekstrak dan diupload dengan
  struktur folder di dalamnya tetap terjaga, semua dalam **1 commit**.
- Tampilan responsif: di layar HP, tampilan jadi satu panel per layar
  (daftar repo → file tree → editor) dengan tombol "←" untuk kembali,
  supaya tidak sempit dan berantakan.
- Activity log lokal untuk melihat histori aksi di sesi berjalan.

### Catatan teknis: commit multi-file

Fitur hapus banyak file / upload banyak file dalam satu commit memakai
Git Data API (`git/blobs`, `git/trees`, `git/commits`, `git/refs`), bukan
Contents API biasa (yang selalu menghasilkan 1 commit per file). Untuk repo
kosong tanpa commit sama sekali, tools ini otomatis fallback membuat commit
pertama tanpa parent.

## Catatan keamanan untuk pengguna

- Pakai token dengan scope seminim mungkin. Kalau bisa, pakai
  **fine-grained personal access token** yang di-scope ke repo tertentu saja.
  Buat di [github.com/settings/tokens](https://github.com/settings/tokens).
- Jangan login di perangkat publik/bersama.
- Token disimpan di `localStorage`, jadi kalau orang lain punya akses fisik
  ke browser yang sama dan belum logout, mereka bisa memakai sesi itu.
