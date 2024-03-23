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

### Commit 3
![Commit 3 screen capture](/assets/images/commit3.png)

Pada awalnya, website melakukan respons yang sama terhadap respons dari client. Sekarang website harus menangani dua respons yang berbeda, sehingga perlu dilakukan pemeriksaan terhadap `request_line` untuk menentukan tindakan selanjutnya. Jika `request_line` sesuai dengan permintaan GET dari path yang diinginkan, konten dari file `hello.html` akan dikirimkan. Jika tidak sesuai, maka konten dari file `404.html` akan dikirim sebagai tanda bahwa permintaan yang diminta tidak ditemukan. 

Adapun isi dari `404.html` adalah sebagai berikut:
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Hello!</title>
  </head>
  <body>
    <h1>Oops!</h1>
    <p>Sorry, I don't know what you're asking for.</p>
  </body>
</html>
```

Lalu perubahan `main.rs` yaitu di method `handle_connection` setelah dilakukan pembaruan adalah sebagai berikut:
```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    if request_line == "GET / HTTP/1.1" {
        let status_line = "HTTP/1.1 200 OK";
        let contents = fs::read_to_string("hello.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    } else {
        let status_line = "HTTP/1.1 404 NOT FOUND";
        let contents = fs::read_to_string("404.html").unwrap();
        let length = contents.len();

        let response = format!(
            "{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}"
        );

        stream.write_all(response.as_bytes()).unwrap();
    }
}
```

Refactoring diperlukan karena blok if-else sebelumnya mengulangi proses pembacaan file HTML dan penulisan kontennya ke dalam stream. Dengan menggunakan pendekatan yang lebih modular, kondisional hanya diterapkan pada `request_line`, kemudian prosedur yang sama dilakukan untuk membaca file dan mengirim kontennya ke dalam stream tanpa duplikasi kode. Hal ini mengurangi repetisi dan membuat kode menjadi lebih terstruktur. Berikut isi `handle_connection` setelah dilakukan refactoring:
```rust
fn handle_connection(mut stream: TcpStream) {
    let buf_reader = BufReader::new(&mut stream);
    let request_line = buf_reader.lines().next().unwrap().unwrap();

    let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
        ("HTTP/1.1 200 OK", "hello.html")
    } else {
        ("HTTP/1.1 404 NOT FOUND", "404.html")
    };

    let contents = fs::read_to_string(filename).unwrap();
    let length = contents.len();

    let response =
        format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");

    stream.write_all(response.as_bytes()).unwrap();
}
```

Jadi, dalam pembaruan fungsi `handle_connection`, pembacaan file HTML dan penulisan konten ke dalam stream hanya dilakukan sekali setelah pengecekan kondisi, berdasarkan nilai `request_line`.

### Commit 4
Setelah dilakukan penyesuaian if else dengan menggunakan match di `handle_connection`, sehingga nilai `request_line` bisa dibandingkan dengan pola yang ada. Untuk mengilustrasikan single-threadednya bisa dengan menambahkan endpoint baru seperti `/sleep`, maka penundaan proses akan dilakukan selama 10 detik dengan menggunakan `thread::sleep(Duration::from_secs(10));`.

Pada kasus ini, jika mengakses `127.0.0.1` dan `127.0.0.1/sleep` secara bersamaan, respons dari `/sleep` akan menunda respon dari permintaan lainnya. Website memberikan respon dengan delay 10 detik karena terdapat thread sleep yang menghentikan thread sementara. Dengan demikian, penggunaan single thread dapat menyebabkan penundaan dalam pemrosesan permintaan di server.