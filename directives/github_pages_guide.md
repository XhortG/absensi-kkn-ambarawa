# Panduan Deployment ke GitHub Pages

GitHub Pages memungkinkan Anda untuk menghosting website HTML statis (seperti `index.html` ini) secara gratis. Berikut adalah langkah-langkah mudah untuk menayangkannya ke internet.

## 1. Buat Repository Baru di GitHub
1. Login ke akun [GitHub](https://github.com/) Anda.
2. Klik tombol hijau **New** di pojok kiri atas (atau tombol **+** di pojok kanan atas > **New repository**).
3. Isi kolom **Repository name** (misal: `absensi-kkn-ambarawa`).
4. Pastikan opsi **Public** terpilih (agar GitHub Pages bisa diaktifkan versi gratis).
5. Centang kotak **"Add a README file"**.
6. Klik tombol **Create repository**.

## 2. Upload File index.html
1. Di halaman repository yang baru dibuat, klik **Add file** > **Upload files**.
2. *Drag and drop* (seret) file `index.html` Anda ke area upload.
3. Tunggu hingga proses upload selesai.
4. Di bagian bawah ("Commit changes"), tulis deskripsi singkat (misal: "Menambahkan index.html").
5. Klik tombol hijau **Commit changes**.

## 3. Aktifkan GitHub Pages
1. Di repository Anda, klik tab **Settings** (ikon gir) di menu bagian atas.
2. Di sidebar sebelah kiri, scroll ke bawah dan klik **Pages**.
3. Pada bagian **Build and deployment**, cari tulisan **Source**. Pastikan terpilih "Deploy from a branch".
4. Pada menu dropdown di bawah kata **Branch**, ubah dari `None` menjadi `main` (atau `master`). Biarkan folder di sebelahnya tetap `/ (root)`.
5. Klik **Save**.
6. Tunggu sekitar 1-3 menit. GitHub sedang memproses website Anda.
7. Refresh halaman tersebut, Anda akan melihat notifikasi berbunyi: *"Your site is live at https://username.github.io/absensi-kkn-ambarawa/"*.
8. Buka URL tersebut, dan website absensi KKN Anda sudah online dan siap digunakan oleh semua anggota!

## Tips
- Pastikan Anda sudah mengganti `SCRIPT_URL` di `index.html` dengan URL Web App dari Google Apps Script sebelum mengunggah file ke GitHub.
- Jika ada perubahan pada website di masa depan, Anda hanya perlu mengunggah ulan (`Upload files`) file `index.html` yang sudah di-update dan menimpa file yang lama. Perubahan akan otomatis tayang dalam beberapa menit.
