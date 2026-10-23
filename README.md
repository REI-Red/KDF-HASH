# 🔐 KDF + Salt Hash

Tool berbasis web untuk mengubah **master password** dan **salt** menjadi **hash** menggunakan **PBKDF2** (Password-Based Key Derivation Function). Seluruh aplikasi berada dalam satu file HTML dan berjalan di browser tanpa server, tanpa library eksternal, dan tanpa instalasi.

```
master password + salt ──► PBKDF2 (HMAC-SHA, N iterasi) ──► derived key (hash)
```

## ✨ Fitur

- Generate salt acak 16 byte memakai `crypto.getRandomValues`
- Pilihan algoritma hash: SHA-256, SHA-384, SHA-512
- Jumlah iterasi dapat diatur (default 600.000)
- Panjang hash dapat dipilih: 16, 32, atau 64 byte
- Output dalam format hex dan base64
- Format simpan: `PBKDF2-SHA256$iterasi$salt$hash`
- Verifikasi password dengan perbandingan constant-time
- Menampilkan waktu proses derivasi
- Tampilan responsif dengan mode terang dan gelap otomatis

## 🚀 Cara Menjalankan

1. Download file `kdf-hash.html`.
2. Buka file tersebut di browser modern (Chrome, Edge, Firefox, Safari).
3. Masukkan master password.
4. Klik **Generate** untuk membuat salt, atau isi salt sendiri dalam format hex.
5. Atur algoritma, iterasi, dan panjang hash bila perlu, lalu klik **Buat hash**.
6. Untuk verifikasi, tempel format simpan ke kolom verifikasi, masukkan master password, lalu klik **Verifikasi**.

## 🧠 Cara Kerja

- **Salt** membuat password yang sama menghasilkan hash berbeda, sehingga serangan rainbow table tidak efektif.
- **Iterasi** yang banyak memperlambat proses tebak-menebak (brute force).
- **Derivasi** dilakukan oleh [Web Crypto API](https://developer.mozilla.org/docs/Web/API/Web_Crypto_API) bawaan browser (`crypto.subtle.deriveBits`) dengan HMAC sebagai fungsi pseudo-random.
- **Verifikasi** menurunkan ulang hash dari password yang dimasukkan memakai parameter dalam format simpan, lalu membandingkannya dengan hash tersimpan tanpa berhenti di karakter pertama yang berbeda (constant-time).

## 📁 Struktur Proyek

```
.
├── kdf-hash.html   # seluruh aplikasi (HTML + CSS + JavaScript)
├── README.md
└── LICENSE
```

### Isi `kdf-hash.html`

| Bagian | Keterangan |
|---|---|
| `<style>` | Tampilan responsif, warna lewat CSS variable, mendukung `prefers-color-scheme: dark` |
| Kartu input | Master password, salt, algoritma hash, iterasi, panjang hash |
| Kartu output | Hash dalam hex, base64, dan format simpan, serta waktu proses |
| Kartu verifikasi | Pengecekan password terhadap format simpan |
| `<script>` | Seluruh logika aplikasi (lihat tabel fungsi) |

### Fungsi JavaScript

| Fungsi | Keterangan |
|---|---|
| `hex(buffer)` | Mengubah `ArrayBuffer` menjadi string hex |
| `unhex(string)` | Mengubah string hex menjadi `Uint8Array`, menolak input yang bukan hex valid |
| `kdf(pw, saltHex, alg, it, len)` | Menurunkan hash memakai PBKDF2 melalui `crypto.subtle` |
| Handler `gen` | Membuat salt acak 16 byte |
| Handler `go` | Menjalankan derivasi dan menampilkan hasil |
| Handler `vf` | Mem-parse format simpan, menurunkan ulang hash, dan membandingkannya |
| Handler `tg` / `cp` | Menampilkan/menyembunyikan password dan menyalin format simpan |

### Format simpan

```
PBKDF2-SHA256$600000$<salt hex>$<hash hex>
```

Empat bagian dipisahkan tanda `$`: algoritma, jumlah iterasi, salt, dan hash. Panjang hash pada verifikasi dihitung otomatis dari panjang hex yang tersimpan.

## 🔒 Catatan Keamanan

- Seluruh proses berjalan lokal di browser. Tidak ada data yang dikirim ke server.
- Master password tidak disimpan di mana pun oleh aplikasi.
- Proyek ini dibuat sebagai proyek belajar untuk memahami KDF dan salt, dan bukan merupakan sistem autentikasi atau password manager yang siap produksi.

## 🛠️ Teknologi

- HTML, CSS, dan JavaScript (vanilla)
- Web Crypto API

## 📄 Lisensi

Dirilis di bawah [MIT License](LICENSE).
