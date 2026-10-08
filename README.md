# Deteksi Posisi Jari — MediaPipe Hands

Sistem deteksi koordinat tangan dan kalkulasi ekstensi jari secara real-time
melalui sensor optik (webcam) menggunakan **MediaPipe Hands**.

Tugas Praktik AI & Machine Learning — XII RPL 2.

## Fitur

- Deteksi hingga **2 tangan** sekaligus (maksimal 10 jari).
- Menghitung jumlah jari terbuka secara real-time.
- Status ekstensi tiap jari (ibu jari, telunjuk, tengah, manis, kelingking).
- Label otomatis **tangan kiri / kanan**.
- Penggambaran kerangka tangan (21 titik koordinat) di atas video.
- Indikator **FPS** dan jumlah tangan terdeteksi.
- Tombol nyala/mati kamera.

## Teknologi

| Bagian     | Keterangan                                  |
| ---------- | ------------------------------------------- |
| Struktur   | HTML5                                       |
| Tampilan   | CSS3 (custom properties, grid, animasi)     |
| Logika     | JavaScript (ES6)                            |
| Deteksi    | MediaPipe Hands `@mediapipe/hands` via CDN  |
| Kamera     | MediaPipe Camera Utils                      |

## Struktur Berkas

```
.
├── index.html            # Struktur halaman + seluruh logika JavaScript
├── style.css             # Tampilan instrumen (tema gelap + aksen oranye)
├── LAPORAN-UJI-COBA.html # Laporan: tabel uji coba, gejala, dan refleksi
└── modul-deteksi-jari.pdf# Modul praktik dari guru
```

## Cara Menjalankan

> **Wajib memakai Live Server.** Membuka berkas dengan klik dua kali
> (`file://`) membuat kamera diblokir oleh browser.

1. Buka folder ini di **VS Code**.
2. Pasang ekstensi **Live Server** (Ritwick Dey) bila belum ada.
3. Klik kanan `index.html` → **Open with Live Server**.
4. Izinkan akses webcam saat browser meminta.
5. Halaman terbuka di `http://127.0.0.1:5500/index.html`.

Tekan **F12 → Console** untuk melihat pesan atau error.

## Alur Program (12 Langkah)

Seluruh logika berada di dalam `<script>` pada `index.html` dan ditandai dengan
komentar `// LANGKAH 1` sampai `// LANGKAH 12`:

| Langkah | Isi                                                    |
| ------- | ------------------------------------------------------ |
| 1       | Menghubungkan elemen HTML dengan `getElementById`      |
| 2       | Variabel status dan tiga array daftar jari             |
| 3       | Fungsi `onResults`: kerangka dasar (FPS + gambar frame)|
| 4       | Menjalankan MediaPipe Hands dan kamera                 |
| 5       | Menyiapkan penampung hasil per frame                   |
| 6       | Kerangka perulangan untuk tiap tangan                  |
| 7       | Menggambar kerangka tangan                             |
| 8       | Mengenali tangan kiri atau kanan                       |
| 9       | Deteksi 4 jari dengan perulangan `for`                 |
| 10      | Deteksi ibu jari (10a jarak · 10b hOpen · 10c vOpen · 10d gabung) |
| 11      | Menampilkan hasil ke layar                             |
| 12      | Tombol nyala/mati kamera                               |

## Konsep: Komputer Melihat 21 Titik

Model MediaPipe tidak "melihat jari" seperti manusia, melainkan menemukan
**21 titik** pada tangan. Setiap titik punya koordinat `x` dan `y` bernilai
0–1 yang disimpan dalam array `landmarks`.

| Jari       | Ujung (tip) | Sendi pembanding | Sumbu                  |
| ---------- | ----------- | ---------------- | ---------------------- |
| Ibu jari   | 4           | 3 (IP), 2 (MCP)  | `x`, lalu `y` cadangan |
| Telunjuk   | 8           | 6 (PIP)          | `y`                    |
| Tengah     | 12          | 10 (PIP)         | `y`                    |
| Manis      | 16          | 14 (PIP)         | `y`                    |
| Kelingking | 20          | 18 (PIP)         | `y`                    |

Titik **17** (pangkal kelingking) dipakai untuk memeriksa ibu jari.

> **Perhatian:** pada layar komputer sumbu `y` dihitung dari atas, sehingga
> nilai `y` yang **lebih kecil** berarti posisi **lebih tinggi**. Ini kebalikan
> dari grafik pada pelajaran matematika.

## Hasil Uji Coba

| Gerakan tangan                     | Seharusnya | Hasil |
| ---------------------------------- | ---------- | ----- |
| Kepalan                            | 0          | 0     |
| Telunjuk saja                      | 1          | 1     |
| Simbol "peace" (V)                 | 2          | 2     |
| Tiga jari (telunjuk, tengah, manis)| 3          | 3     |
| Telapak terbuka                    | 5          | 5     |
| Dua tangan terbuka                 | 10         | 10    |

Rincian lengkap, tabel gejala & penyebab, serta jawaban refleksi ada di
[`LAPORAN-UJI-COBA.html`](LAPORAN-UJI-COBA.html).

## Pemecahan Masalah

| Gejala                                    | Kemungkinan penyebab                                              |
| ----------------------------------------- | ----------------------------------------------------------------- |
| Layar kosong / "Memuat model…" terus      | Lupa Langkah 4, atau internet bermasalah (library dari internet)   |
| Console merah: `... is not defined`       | Salah ketik nama variabel atau lupa mendefinisikannya (Langkah 1–2)|
| Console merah: `Unexpected token`         | Kurung `{` tidak berpasangan atau kurang tanda koma               |
| Angka selalu 0                            | Kode jari di luar `for`, atau lupa `totalFingers++`               |
| Jari menyala saat dikepal                 | Tanda `<` dan `>` tertukar (ingat: `y` kecil berarti di atas)      |
| Angka lebih dari 5 untuk satu tangan      | `totalFingers++` berada di luar `if`                              |
| Kamera tidak muncul                       | Tidak memakai Live Server, atau izin kamera ditolak               |
| Ibu jari terbalik kiri-kanan              | Perbandingan `x` untuk `isRight` tertukar                         |

## Catatan

- Library MediaPipe diunduh dari CDN, jadi **koneksi internet diperlukan**
  saat halaman pertama kali dibuka.
- Ukuran buffer kamera 1280×720; nilai `minDetectionConfidence` dan
  `minTrackingConfidence` disetel `0.65`.
