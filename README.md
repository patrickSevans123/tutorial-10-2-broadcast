## 3.1. Original code of broadcast chat.

| Server | Client 1 | Client 2 | Client 3 |
| ------ | -------- | -------- | -------- |
| ![Server](img/server.png) | ![Client 1](img/client1.png) | ![Client 2](img/client2.png) | ![Client 3](img/client3.png) |

Pada gambar di atas, saya menjalankan program dengan membuat sebuah server dan tiga client. Melalui percobaan broadcast di atas, dapat dilihat bahwa di saat suatu client mengirimkan pesan, maka server akan menerima pesan tersebut dan mengirimkannya kembali kepada semua client, termasuk client yang mengirim pesan.

# 2.2. Modifying the websocket port.
| Server | Client 1 | Client 2 | Client 3 |
| ------ | -------- | -------- | -------- |
| ![Server](img/server2.png) | ![Client 1](img/client21.png) | ![Client 2](img/client22.png) | ![Client 3](img/client23.png) |

Client akan mengalami error seperti gambar di atas apabila hanya server yang diubah portnya. Hal ini disebabkan karena port antara server dan client berbeda. Oleh karena itu kita perlu menyeleraskan port yang diakses oleh client dengan port server.

Client berhasil mengakses server dan mengirimkan pesan ke server jika memiliki port yang sama. Kode pada client.rs yang perlu diubah adalah:
```rust
let (mut ws_stream, _) =
        ClientBuilder::from_uri(Uri::from_static("ws://127.0.0.1:2000"))
            .connect()
            .await?;
```
menjadi
```rust
let (mut ws_stream, _) =
        ClientBuilder::from_uri(Uri::from_static("ws://127.0.0.1:8080"))
            .connect()
            .await?;
```
Selanjutnya,
```rust
 let listener = TcpListener::bind("127.0.0.1:2000").await?;
    println!("listening on port 2000");
```
perlu diganti menjadi
```rust
 let listener = TcpListener::bind("127.0.0.1:8080").await?;
    println!("listening on port 8080");
```
Berikut adalah hasil dari penjalanan program tersebut.
| Server | Client 1 | Client 2 | Client 3 |
| ------ | -------- | -------- | -------- |
| ![Server](img/server3.png) | ![Client 1](img/client31.png) | ![Client 2](img/client32.png) | ![Client 3](img/client33.png) |

Kedua program baik client maupun server memiliki protokol yang tetap sama seperti sebelumnya, yaitu menggunakan library `tokio_websockets` dan koneksi TCP.

## 2.3. Small changes. Add some information to client 
Pertama, saya mengubah `println!("From server: {}", text);` pada client.rs menjadi `println!("Evans's computer - From server: {}", text);` untuk menambah nama saya. Kedua, saya mengubah `println!("New connection from {addr:?}");` pada server.rs menjadi
```rust
println!("New connection from Evans's Computer {addr:?}");
let bcast_tx = bcast_tx.clone();
```
Selanjutnya, saya mengubah kode berikut pada server.rs
```rust
println!("From client {addr:?} {text:?}");
bcast_tx.send(text.into())?;
```
menjadi
```rust
println!("From client {addr:?} {text:?}");
bcast_tx.send(format!("{addr} : {text}"))?;
```

Berikut adalah hasil dari penjalanan program yang telah mengalami pengubahan.
| Server | Client 1 | Client 2 | Client 3 |
| ------ | -------- | -------- | -------- |
| ![Server](img/server4.png) | ![Client 1](img/client41.png) | ![Client 2](img/client42.png) | ![Client 3](img/client43.png) |