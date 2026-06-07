# e-Kuratorial Satwa - Panduan Deploy

## Struktur File

```
e-kuratorial/
├── index.html      → Frontend (deploy ke Vercel)
├── Code.gs         → Backend API (deploy ke Google Apps Script)
├── vercel.json     → Konfigurasi Vercel
└── README.md       → Panduan ini
```

---

## BAGIAN 1: Deploy Code.gs ke Google Apps Script

1. Buka Google Spreadsheet Anda:
   https://docs.google.com/spreadsheets/d/1FjL4z7YJ8SErrhJYEoCYBWojyj3RecfwRD0QPy2qk3w/

2. Klik menu **Extensions → Apps Script**

3. Hapus semua kode yang ada di editor

4. Copy-paste seluruh isi file **Code.gs** ke editor

5. Klik **Save** (ikon floppy disk / Ctrl+S)

6. Klik **Deploy → New deployment**

7. Pada "Select type", pilih **Web app**

8. Isi pengaturan:
   - Description: `e-Kuratorial API v1`
   - Execute as: **Me** (akun Google Anda)
   - Who has access: **Anyone**

9. Klik **Deploy**

10. Akan muncul jendela "Authorization required":
    - Klik **Authorize access**
    - Pilih akun Google Anda
    - Klik **Advanced** → **Go to e-Kuratorial API (unsafe)**
    - Klik **Allow**

11. **COPY URL Web App** yang tampil (format:
    `https://script.google.com/macros/s/XXXXX/exec`)
    → Simpan URL ini, akan dipakai di langkah terakhir!

12. Klik **Done**

13. Jalankan fungsi `setupDatabase` sekali untuk membuat sheet:
    - Di Apps Script editor, pilih fungsi `setupDatabase` dari dropdown
    - Klik tombol **Run**

---

## BAGIAN 2: Deploy index.html ke Vercel via GitHub

### Langkah 2a: Upload ke GitHub

1. Buka https://github.com dan login

2. Klik **+** → **New repository**

3. Nama repo: `e-kuratorial-satwa` (atau nama lain)

4. Pilih **Public**, klik **Create repository**

5. Upload file-file berikut ke repo:
   - `index.html`
   - `vercel.json`

   Cara upload: klik **uploading an existing file** di halaman repo baru,
   drag & drop kedua file, klik **Commit changes**

### Langkah 2b: Connect ke Vercel

1. Buka https://vercel.com dan login (bisa pakai akun GitHub)

2. Klik **Add New... → Project**

3. Pilih repository `e-kuratorial-satwa` dari daftar

4. Klik **Import**

5. Di halaman konfigurasi:
   - Framework Preset: **Other**
   - Root Directory: `.` (default)
   - Build Command: kosongkan
   - Output Directory: `.` (titik satu)

6. Klik **Deploy**

7. Tunggu beberapa detik sampai muncul **"Congratulations!"**

8. Klik **Visit** atau copy URL Vercel Anda
   (format: `https://e-kuratorial-satwa.vercel.app`)

---

## BAGIAN 3: Hubungkan Frontend ke Apps Script API

1. Buka URL Vercel Anda di browser

2. Login dengan PIN: **1234**

3. Klik tombol **⚙ API URL** di pojok kanan bawah

4. Paste URL Apps Script dari Langkah 1.11

5. Klik **OK** → Halaman akan reload

6. Klik **Muat dari Sheet** untuk ambil data dari Spreadsheet

7. Klik **Simpan ke Sheet** untuk sinkronisasi data lokal ke Spreadsheet

---

## Cara Kerja Sistem

```
[Browser / Vercel]  ←→  [Google Apps Script API]  ←→  [Google Spreadsheet]
   index.html              Code.gs (Web App)           Database utama
   (localStorage)          /exec?action=getData
                           POST body: {action, data}
```

- Data disimpan di **localStorage** browser secara real-time
- Tombol **"Muat dari Sheet"** = download data terbaru dari Spreadsheet
- Tombol **"Simpan ke Sheet"** = upload semua data lokal ke Spreadsheet
- Data Spreadsheet = database permanen yang bisa diakses dari perangkat manapun

---

## Update Kode di Masa Depan

Jika ada perubahan pada `index.html`:
1. Upload file baru ke GitHub (replace file lama)
2. Vercel otomatis redeploy dalam 1-2 menit

Jika ada perubahan pada `Code.gs`:
1. Edit di Apps Script editor
2. Klik **Deploy → Manage deployments**
3. Pilih deployment yang ada → klik **Edit** (ikon pensil)
4. Versi: pilih **New version**
5. Klik **Deploy**
