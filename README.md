# Reflection
## Module 6

### Reflection Commit 1
1. Mengimport std::io::prelude and std::io::BufReader untuk memungkinkan kita membaca dan menulis ke stream
2. Membuat BufReader instance yang akan meng-wrap mutable reference ke instance TcpStream. BufReader akan menambahkan buffer dengan mengelola pemanggilan ke std::io::Read
```
let buf_reader = BufReader::new(&mut stream);
```
3. Membuat variable http_request yang akan mengumpulkan baris dari request yang dikirimkan ke server. Baris tersebut disimpan dalam bentuk vector. Kita menggunakan method lines() untuk memecah stream data setiap kali menemui byte newline. Untuk mendapatkan tiap string, kita map dan unwrap tiap string dengan map(|result| result.unwrap()). Jika result berupa  error, maka program akan berhenti. Selanjutnya, take_while(|line| !line.is_empty) digunakan untuk mengambil tiap baris hingga ditemukan baris berupa string kosong. Dan terakhir collect() digunakan untuk mengumpulkan semua baris kedalam vector.
```
    let http_request: Vec<_> = buf_reader 
    .lines() 
    .map(|result| result.unwrap())
    .take_while(|line| !line.is_empty()) 
    .collect();
```

### Reflection Commit 2
![Commit 2](assets/images/commit2.png)
1. Membuat file html baru "hello.html" yang akan ditampilkan sebagai berikut
```
<!DOCTYPE html>
<html lang="en">
 <head>
 <meta charset="utf-8"> <title>Hello!</title>
 </head> <body> <h1>Hello!</h1> <p>Hi from Rust, running from Hanau’s machine.</p>
 </body>
</html>
```
2. Menambahkan fs yang memungkinkan untuk membaca suatu file
3. Pada handle_connection, kita membaca file "hello.html" dengan fs dalam bentuk string lalu mengambil panjang dari isi file "hello.html" tersebut
```
let contents = fs::read_to_string("hello.html").unwrap(); 
let length = contents.len();
```
4. Menggunakan format! untuk menambahkan kontent dari file "hello.html" sebagai body dari response
```
let response = format!("{status_line}\r\nContent-Length: {length}\r\n\r\n{contents}");
stream.write_all(response.as_bytes()).unwrap();
```

### Reflection Commit 3
![Commit 3](assets/images/commit3.png)
1. Mengambil request line dari suatu http request. Request line tersebut berisi request method dan path yang digunakan. Berikut codenya
```
let request_line = buf_reader.lines().next().unwrap().unwrap();
```
2. Set status line dan file html sesuai dengan request line. Jika request_line tidak sesuai, maka akan menampilkan page 404 error. Berikut codenya
```
let (status_line, filename) = if request_line == "GET / HTTP/1.1" {
    ("HTTP/1.1 200 OK", "hello.html")
} else {
    ("HTTP/1.1 404 NOT FOUND", "404.html")
};
```
3. Berikut adalah code 404.html
```
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
Mengapa refactor diperlukan:
Pada code sebelum refactor, ada beberapa repetisi atau code yang duplikat. Bagian code yang duplikat adalah bagian saat kita membaca dari suatu file html lalu memasukkannya sebagai body dari response. Kita dapat mengeluarkan bagian code tersebut setelah block if-else, dimana if-else hanya berisi peng-assign an dari status line dan file html yang akan dijadikan body response.