# 🔍 Competitor Price Monitor — n8n Automation

Workflow otomatis untuk memantau perubahan harga produk kompetitor di Tokopedia setiap hari, dengan alert WhatsApp real-time. Dibangun sebagai lanjutan dari portfolio automation UMKM "Warung Nasi Uduk Bu Neli", dengan fokus mempelajari HTTP Request tingkat lanjut, GraphQL API, dan data comparison logic.

## 📋 Cara Kerja

1. **Schedule Trigger** — jalan otomatis tiap hari jam 7 pagi
2. **Google Sheets (Read)** — baca daftar produk yang dipantau (cukup isi URL produk)
3. **Parse URL** — otomatis ekstrak `shopDomain` dan `productKey` dari URL Tokopedia
4. **Loop Over Items** — proses setiap produk satu per satu
5. **HTTP Request** — hit endpoint GraphQL internal Tokopedia (`PDPMainInfo`) untuk ambil data harga real-time
6. **Compare Logic** — bandingkan harga baru vs harga tersimpan sebelumnya
7. **Conditional Alert** — kirim notifikasi WhatsApp (via Fonnte) hanya jika harga berubah
8. **Google Sheets (Update)** — simpan harga terbaru sebagai baseline berikutnya

## 🛠️ Tech Stack

- **n8n** — workflow automation platform (self-hosted di Railway)
- **Tokopedia GraphQL API** — endpoint internal untuk data produk (reverse-engineered via browser DevTools)
- **Google Sheets** — penyimpanan data produk & histori harga
- **Fonnte** — WhatsApp API untuk notifikasi
- **JavaScript (Code node)** — parsing data, comparison logic, URL parsing

## 📸 Screenshots

*(lihat folder `/screenshots`)*

- Diagram alur workflow lengkap
- Contoh alert WhatsApp
- Struktur Google Sheets

## ⚠️ Known Limitations & Lessons Learned

Selama pengembangan, ditemukan beberapa tantangan nyata bekerja dengan API pihak ketiga yang tidak resmi/tidak terdokumentasi:

- **Produk dengan varian kompleks**: Tokopedia menyimpan data harga produk bervarian (pilihan warna/ukuran/tipe) dengan struktur yang tidak konsisten antar produk — kadang harga valid ada di komponen utama, kadang di komponen varian terpisah. Sistem ini dioptimalkan untuk produk tanpa varian aktif.
- **Ketidakstabilan data saat stok habis**: Endpoint API dapat mengembalikan harga placeholder yang tidak valid ketika produk kehabisan stok. Sistem menangani ini dengan mendeteksi `stock = 0` dan mempertahankan baseline harga terakhir yang valid, alih-alih menimpa dengan data yang meragukan.
- **Deteksi baseline baru**: Produk yang baru ditambahkan ke sheet tidak langsung memicu alert WhatsApp — sistem mengenali "pengecekan pertama" dan hanya menyimpan baseline tanpa notifikasi palsu.

## 🚀 Setup

1. Import `workflows/Competitor_Price_sanitized.json` ke n8n
2. Ganti placeholder berikut dengan kredensial Anda:
   - `YOUR_FONNTE_TOKEN_HERE` — token API Fonnte
   - `YOUR_WHATSAPP_NUMBER` — nomor tujuan alert
   - `YOUR_GOOGLE_SHEET_ID` — ID Google Sheet Anda
3. Buat Google Sheet dengan kolom: `nama_produk`, `url_produk`, `harga_terakhir`, `harga_format`, `terakhir_dicek`
4. Hubungkan credential Google Sheets OAuth2 di n8n
5. Aktifkan workflow

## 👤 Author

M. Naufal Awalludin — [GitHub](https://github.com/mnaufal-a) | [LinkedIn](https://www.linkedin.com/in/m-naufal-awalludin-0736b3309/)
