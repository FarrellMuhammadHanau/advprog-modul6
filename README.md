# Reflection
## Module 6

### Reflection 1
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