# E-Surat Keterangan Aktif Sekolah — SMKS Budi Mulya Kasiman

Frontend dipindahkan dari embed Blogger ke GitHub Pages. Backend tetap memakai Google Apps Script dan Google Spreadsheet.

## Struktur berkas
- `index.html` — antarmuka aplikasi dan cetak surat.
- `config.js` — URL backend dan sakelar preview.
- `backend_logic_gas.js` — kode backend untuk ditempel ke proyek Google Apps Script.

## 1. Siapkan backend Google Apps Script
1. Buat/buka Google Spreadsheet khusus database.
2. Buka **Extensions → Apps Script**.
3. Ganti kode proyek dengan isi `backend_logic_gas.js`.
4. Pilih fungsi `setupDatabase`, lalu klik **Run** dan berikan izin.
5. Buka sheet `Users`, ganti nilai password awal `GANTI_PASSWORD_ADMIN` dan `GANTI_PASSWORD_OPERATOR` dengan password unik. Pastikan kolom `aktif` berisi `TRUE`.
6. Deploy → **New deployment** → type **Web app**.
   - Execute as: **Me**
   - Who has access: **Anyone**
7. Salin URL Web App yang berakhir `/exec`.

> Password pada versi ini disimpan sebagai teks di sheet agar sederhana untuk admin sekolah. Batasi akses editor spreadsheet hanya kepada petugas tepercaya dan gunakan password unik. Untuk keamanan lebih kuat, gunakan sistem hash password dan pengelolaan akun khusus.

## 2. Atur `config.js`
Ganti `gasUrl` dengan URL `/exec` hasil deployment:

```js
window.APP_CONFIG = {
  gasUrl: "https://script.google.com/macros/s/ID_DEPLOYMENT/exec",
  preview: false
};
```

`preview: false` wajib untuk produksi. Mode preview memakai data dummy dan akun demo sehingga jangan dipakai pada situs publik produksi.

## 3. Upload ke GitHub Pages
1. Buat repository GitHub, misalnya `e-surat-aktif`.
2. Upload `index.html` dan `config.js` ke root repository.
3. Backend `backend_logic_gas.js` tidak perlu diunggah ke repository publik jika tidak ingin membagikan detail backend; simpan di Apps Script. Jika ikut diunggah, jangan pernah memasukkan password asli atau token.
4. Repository → **Settings → Pages**.
5. Pada Build and deployment pilih **Deploy from a branch**, branch `main`, folder `/ (root)`, lalu Save.
6. Buka URL GitHub Pages yang muncul.

## Perbaikan yang dilakukan
- Frontend mandiri untuk GitHub Pages; tidak lagi mengandalkan Blogger.
- URL backend dipisah di `config.js`.
- Login tidak otomatis sebagai Admin.
- Token sesi berlaku 12 jam dan endpoint data memerlukan token.
- Perubahan UI/cache disimpan ke `localStorage` terlebih dahulu, lalu disinkronkan ke backend.
- Pengaturan dan pengelolaan siswa dibatasi untuk role `admin`; operator dapat menggunakan fitur surat.
- Import siswa mengirim hanya baris yang baru diimpor, bukan mengirim ulang seluruh data.
- Data contoh tidak dimasukkan otomatis ke database baru.
- Pesan kesalahan API lebih informatif.

## Catatan penting
- `localStorage` hanya tersedia pada browser/perangkat yang sedang dipakai; database pusat tetap Google Spreadsheet.
- Jika backend berubah atau deployment diperbarui, deploy versi baru pada Apps Script dan periksa kembali `gasUrl`.
- Fitur arsip surat terbit belum ada pada kode sumber awal, jadi versi ini belum menyimpan salinan setiap surat yang dicetak. Cetak/simpan PDF tetap melalui dialog cetak browser.
- Periksa ulang nama kepala sekolah, alamat, logo, nomor surat, dan format surat sebelum digunakan resmi.
