# **AdproModul9-Publisher**
### Nama: Allan Kwek
### NPM: 2306152134

### 1. How much data your publisher program will send to the message broker in one run??
#### Di dalam main() terlihat ada 5 panggilan ke

```rust
p.publish_event("user_created".to_owned(), …);
```
#### Artinya program publisher akan mengirim 5 buah pesan (event) ke broker dalam sekali jalan (one run).

### 2. The url of: “amqp://guest:guest@localhost:5672” is the same as in the subscriber program, what does it mean?
#### URL tersebut menunjukkan bahwa publisher dan subscriber sama‑sama tersambung ke broker RabbitMQ yang sama:

1. guest:guest

- Username = guest

- Password = guest

2. localhost:5672

- Broker dijalankan di mesin lokal (localhost)

- Port standar AMQP (5672)

#### Karena kedua aplikasi (publisher dan subscriber) menggunakan URI yang identik, mereka berbicara pada broker yang sama. Ini memungkinkan publisher mengirim pesan ke antrian/exchange tertentu, lalu subscriber bisa “mendengarkan” dan memproses pesan‑pesan itu dari broker yang sama pula. Dengan kata lain, mereka terhubung ke pintu masuk (port) dan akun yang sama pada broker, sehingga pesan bisa berpindah dari satu program ke program lain lewat RabbitMQ.

### 3. Running RabbitMQ as message broker
![image](https://github.com/user-attachments/assets/25f6f4de-6eb2-437c-b526-42b4fa338783)

### 4. Sending and processing event
![image](https://github.com/user-attachments/assets/fcc3904d-5521-47ce-a019-3b098ff550e4)
![image](https://github.com/user-attachments/assets/c0a5207a-cd6f-4b0c-af35-96b4414f105a)
#### Saat program publisher dijalankan, ia membuat koneksi ke broker RabbitMQ di `localhost:5672` menggunakan kredensial `guest:guest` lalu mengemas lima buah objek `UserCreatedEventMessage` dengan data `user_id` dan `user_name` ke dalam format byte melalui Borsh dan mengirimkannya satu per satu ke broker dengan topik `"user_created"`. Broker kemudian menempatkan kelima pesan tersebut ke dalam antrean sesuai properti yang telah ditentukan. Di sisi listener, program membuka koneksi yang sama ke broker dan mendaftar `UserCreatedHandler` untuk topik `"user_created"`, kemudian memasuki loop yang terus-menerus menunggu kedatangan pesan. Ketika broker mengirim salah satu dari kelima pesan tersebut, library `crosstown_bus` otomatis melakukan deserialisasi byte stream menjadi struct `UserCreatedEventMessage` dan memanggil metode `handle` pada `UserCreatedHandler`, di mana data pesan tersebut ditampilkan lewat `println!` sebelum handler mengembalikan hasil sukses. Dengan cara ini, publisher dan subscriber berjalan terpisah sehingga pengiriman dan pemrosesan pesan dapat dilakukan secara asinkron.

### 5. 
![alt text](image.png)
#### Gambar ini menunjukkan halaman Overview dari RabbitMQ Management UI yang memperlihatkan aktivitas broker selama satu menit terakhir. Terlihat bahwa antrean pesan (Ready, Unacked, Total) semuanya bernilai nol karena kelima pesan yang Anda kirim langsung segera diambil oleh konsumen dan di‑ack tanpa menumpuk, sehingga pada saat snapshot tidak ada pesan yang menunggu. Pada grafik Message rates, puncak sekitar 2 pesan per detik muncul pada metrik Publish, Deliver (manual ack), dan Consumer ack, menggambarkan aliran cepat lima pesan yang Anda terbitkan dan segera diproses; setelah itu laju kembali ke nol karena tidak ada lagi pesan yang dikirim. Di bagian Global counts, hanya ada satu koneksi dan satu channel aktif—yaitu listener Anda—sementara broker menampilkan sepuluh exchange default dan dua queue (termasuk antrean `user_created` dan antrean standar atau dead‑letter jika ada), serta satu konsumen. Di bagian Ports and contexts juga terlihat RabbitMQ mendengarkan pada port 5672 untuk protokol AMQP dan 15672 untuk HTTP UI. Secara keseluruhan, UI ini memperlihatkan broker yang sedang aktif dan responsif, tetapi pada saat tertentu tidak memiliki antrean yang menunggu karena pesan langsung diproses.