# Advance Programming C
**Syauqi Armanaya Syaki** </br>
**2206829010** </br>

# Module 6

## Reflection

### Commit 1
Fungsi `handle_connection` adalah method yang memiliki fungsi untuk mengatur perilaku server saat mendapatkan connection request dari browser, seperti membaca data dari TcpStream, mengelola permintaan HTTP yang diterima, dan mencetak permintaan tersebut. Langkah-langkahnya adalah sebagai berikut:

1. Menerima parameter `stream` dengan tipe `TcpStream` yang mewakili koneksi TCP yang diterima dari client.
2. Membuat sebuah `BufReader` baru yang dibuat dari `stream` untuk melakukan buffering dan membaca data dari stream dengan efisien.
3. Membuat vektor `http_request` yang berisi baris-baris permintaan HTTP yang diterima dari client. Proses ini dilakukan dengan menggunakan metode `lines()` dari `BufReader` untuk menghasilkan iterator yang mengembalikan setiap baris dari stream sebagai `Result<String, std::io::Error>`. Kemudian, menggunakan `map()` untuk melakukan unwrapping hasil dari setiap baris, diikuti dengan `take_while()` untuk mengambil baris-baris sampai baris kosong ditemukan. Akhirnya, hasilnya dikumpulkan ke dalam `Vec<_>` dengan menggunakan `collect()`.
4. Mencetak isi dari `http_request` dengan menggunakan `println!()`.