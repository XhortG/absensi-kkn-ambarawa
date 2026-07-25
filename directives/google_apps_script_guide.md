# Panduan Integrasi Google Apps Script (Backend Absensi)

Dokumen ini berisi kode backend (Google Apps Script) dan langkah-langkah untuk menyambungkan Form HTML ke Google Sheets dan Google Drive.

## 1. Persiapan Google Drive & Sheets
1. Buat **Folder Baru** di Google Drive Anda (misal: "Foto Absensi KKN").
2. Buka folder tersebut, lalu perhatikan URL-nya di browser. Copy **Folder ID** (kumpulan huruf dan angka acak di ujung URL).
3. Buat file **Google Sheets** baru (misal: "Data Absensi KKN").
4. Buat header di baris pertama secara berurutan: `Timestamp`, `Nama`, `Jabatan`, `Tanggal`, `Waktu`, `Status`, `Keterangan`, `URL Foto`.

## 2. Masukkan Kode Google Apps Script
1. Buka file Google Sheets yang baru Anda buat.
2. Klik menu **Extensions > Apps Script** (Ekstensi > Apps Script).
3. Hapus semua kode default (`function myFunction() {}`), lalu *paste* kode di bawah ini:

```javascript
// ==========================================
// KONFIGURASI - UBAH BAGIAN INI
// ==========================================
// Masukkan Folder ID dari Google Drive tempat menyimpan foto absensi
const FOLDER_ID = "MASUKKAN_FOLDER_ID_ANDA_DI_SINI";
const SHEET_NAME = "Sheet1"; // Sesuaikan jika nama sheet Anda berbeda
// ==========================================

function doPost(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);
    const data = JSON.parse(e.postData.contents);
    
    // Parse data dari frontend
    const timestamp = new Date();
    const nama = data.nama;
    const jabatan = data.jabatan;
    const tanggal = data.tanggal;
    const waktu = data.waktu;
    const status = data.status;
    const keterangan = data.keterangan || "";
    
    let fileUrl = "";
    
    // Proses upload gambar jika ada
    if (data.fileBase64 && data.fileName && data.mimeType) {
      const folder = DriveApp.getFolderById(FOLDER_ID);
      const blob = Utilities.newBlob(Utilities.base64Decode(data.fileBase64), data.mimeType, data.fileName);
      const file = folder.createFile(blob);
      
      // Set sharing agar bisa dilihat publik (Anyone with the link)
      file.setSharing(DriveApp.Access.ANYONE_WITH_LINK, DriveApp.Permission.VIEW);
      fileUrl = file.getUrl();
    }
    
    // Simpan ke Google Sheets
    sheet.appendRow([
      timestamp, 
      nama, 
      jabatan,
      tanggal, 
      waktu, 
      status, 
      keterangan, 
      fileUrl
    ]);
    
    return ContentService.createTextOutput(JSON.stringify({"status": "success", "message": "Data berhasil disimpan"}))
      .setMimeType(ContentService.MimeType.JSON);
      
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({"status": "error", "message": error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}

function doGet(e) {
  try {
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getSheetByName(SHEET_NAME);
    const data = sheet.getDataRange().getValues();
    
    // Skip header (baris 1)
    const rows = data.slice(1);
    
    // Dapatkan tanggal hari ini format YYYY-MM-DD
    const today = new Date();
    const yyyy = today.getFullYear();
    const mm = String(today.getMonth() + 1).padStart(2, '0');
    const dd = String(today.getDate()).padStart(2, '0');
    const todayStr = `${yyyy}-${mm}-${dd}`;
    
    // Filter hanya data hari ini
    const todayData = rows.filter(row => {
      // Asumsi kolom Tanggal ada di indeks 3 (D) karena Jabatan ada di indeks 2 (C)
      return row[3] == todayStr;
    });
    
    // Hitung yang hadir hari ini
    const hadirCount = todayData.filter(row => row[5] === "Hadir").length;
    
    // Balikkan urutan agar yang terbaru di atas
    todayData.reverse();
    
    return ContentService.createTextOutput(JSON.stringify({
      "status": "success", 
      "data": todayData,
      "hadirCount": hadirCount
    })).setMimeType(ContentService.MimeType.JSON);
    
  } catch (error) {
    return ContentService.createTextOutput(JSON.stringify({"status": "error", "message": error.toString()}))
      .setMimeType(ContentService.MimeType.JSON);
  }
}
```

## 3. Cara Deployment
1. Simpan kode (Ctrl+S atau ikon Save).
2. Klik tombol **Deploy** di kanan atas > **New deployment**.
3. Klik ikon gir (Settings) di sebelah "Select type", centang **Web app**.
4. Isi detail berikut:
   - **Description**: Absensi KKN API
   - **Execute as**: Me (email Anda)
   - **Who has access**: Anyone (PENTING!)
5. Klik **Deploy**.
6. Google akan meminta otorisasi akun. Klik **Authorize access**, pilih akun Google Anda. Jika muncul peringatan keamanan (Google hasn't verified this app), klik **Advanced (Lanjutan)** > **Go to Untitled project (unsafe)**. Allow semua perizinan.
7. Setelah selesai, Anda akan mendapatkan **Web app URL** (berakhiran `/exec`). **Copy URL tersebut!**

## 4. Hubungkan ke Website
1. Buka file `index.html` Anda.
2. Scroll ke bagian bawah, cari baris berikut:
   ```javascript
   const SCRIPT_URL = 'https://script.google.com/macros/s/AKfycby_PLACEHOLDER_URL/exec';
   ```
3. Ganti URL tersebut dengan Web app URL yang baru saja Anda copy dari Apps Script.
4. Simpan `index.html`. Selesai! Backend Anda sudah terhubung.
