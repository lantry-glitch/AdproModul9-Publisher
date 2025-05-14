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

