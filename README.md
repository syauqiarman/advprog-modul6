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

### Commit 2
![Commit 2 screen capture](/assets/images/commit2.png)

Terdapat pembaruan terhadap method `handle_connection`untuk melakukan respons terhadap permintaan HTTP yang diterima. Prosesnya adalah sebagai berikut:

1. Membuat `status_line` yang berisi status line HTTP yang menyatakan bahwa permintaan berhasil diproses dengan kode "200 OK".
2. Membaca konten dari file `hello.html` dan mengonversinya menjadi string lalu disimpan di variabel `contents`.
3. Menghitung panjang konten untuk disertakan dalam header respons.
4. Membuat respons HTTP lengkap yang mencakup status line, header, dan konten. Respons ini kemudian dikirim kembali ke klien melalui stream yang sama.