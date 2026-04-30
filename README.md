# Reflection

**1. What are the key differences between unary, server streaming, and bi-directional streaming 
RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?**

Unary RPC adalah pola komunikasi paling sederhana di mana client mengirim satu request dan menerima satu response, cocok untuk operasi seperti autentikasi atau pembayaran. Server streaming memungkinkan server mengirim banyak response atas satu request dari client, ideal untuk kasus seperti pengiriman data historis dalam jumlah besar. Sedangkan bi-directional streaming memungkinkan keduanya saling mengirim dan menerima pesan secara bersamaan, sangat cocok untuk aplikasi real-time seperti chat.

**2. What are the potential security considerations involved in implementing a gRPC service in 
Rust, particularly regarding authentication, authorization, and data encryption?**

Dalam mengimplementasikan gRPC di Rust, keamanan menjadi aspek penting yang perlu diperhatikan. Autentikasi dapat dilakukan menggunakan TLS/SSL untuk mengenkripsi komunikasi antara client dan server. Untuk otorisasi, setiap request perlu divalidasi menggunakan token seperti JWT yang disisipkan pada metadata gRPC. Tanpa enkripsi yang tepat, data sensitif seperti informasi pembayaran dapat disadap oleh pihak yang tidak berwenang.

**3. What are the potential challenges or issues that may arise when handling bidirectional 
streaming in Rust gRPC, especially in scenarios like chat applications?**

Salah satu tantangan utama dalam bidirectional streaming adalah pengelolaan koneksi yang berjalan lama. Jika salah satu pihak (client atau server) mengalami error atau disconnect secara tiba-tiba, stream perlu ditangani dengan graceful shutdown agar tidak terjadi resource leak. Selain itu, sinkronisasi antara task pengirim dan penerima dalam konteks async Rust memerlukan perhatian ekstra agar tidak terjadi deadlock atau race condition.

**4. What are the advantages and disadvantages of using the `tokio_stream::wrappers::ReceiverStream` for streaming responses in Rust gRPC services?**

ReceiverStream dari tokio_stream::wrappers sangat memudahkan konversi channel mpsc menjadi stream yang kompatibel dengan tonic, sehingga implementasi server streaming menjadi lebih sederhana dan mudah dibaca. Namun kelemahannya, buffer size channel harus ditentukan secara manual dan jika tidak dikonfigurasi dengan tepat dapat menyebabkan bottleneck atau memory waste, terutama pada beban tinggi.

**5. In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, 
promoting maintainability and extensibility over time?**

Kode gRPC di Rust dapat distruktur lebih modular dengan memisahkan setiap implementasi service ke dalam file atau modul tersendiri, misalnya payment_service.rs, transaction_service.rs, dan chat_service.rs. Dengan pendekatan ini, setiap service dapat dikembangkan dan diuji secara independen, sehingga mempermudah pemeliharaan dan penambahan fitur di masa mendatang.

**6. In the MyPaymentService implementation, what additional steps might be necessary to 
handle more complex payment processing logic?**

Implementasi MyPaymentService saat ini hanya mengembalikan response sukses secara langsung. Pada skenario nyata, service ini perlu terhubung ke database untuk menyimpan transaksi, melakukan validasi saldo, memanggil payment gateway eksternal, serta menangani berbagai kemungkinan error seperti saldo tidak cukup atau timeout koneksi.

**7. What impact does the adoption of gRPC as a communication protocol have on the overall 
architecture and design of distributed systems, particularly in terms of interoperability with 
other technologies and platforms?**

Adopsi gRPC membawa perubahan signifikan pada desain sistem terdistribusi karena mendorong pendefinisian kontrak service yang ketat melalui file .proto. Hal ini meningkatkan interoperabilitas antar layanan yang ditulis dalam bahasa pemrograman berbeda, namun di sisi lain membutuhkan pemahaman tambahan tentang Protocol Buffers dan toolchain-nya. Browser support yang terbatas juga membuat gRPC lebih cocok digunakan untuk komunikasi antar service internal daripada komunikasi langsung dengan frontend web.

**8. What are the advantages and disadvantages of using HTTP/2, the underlying protocol for 
gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?**

HTTP/2 yang menjadi fondasi gRPC memberikan keunggulan berupa multiplexing, header compression, dan server push yang secara signifikan meningkatkan performa dibanding HTTP/1.1. Namun HTTP/2 lebih kompleks dalam implementasinya dan tidak semua infrastruktur lama mendukungnya secara penuh. Dibanding HTTP/1.1 dengan WebSocket, HTTP/2 lebih efisien untuk banyak request kecil secara bersamaan, tetapi WebSocket tetap lebih umum digunakan untuk komunikasi real-time di browser.

**9. How does the request-response model of REST APIs contrast with the bidirectional streaming 
capabilities of gRPC in terms of real-time communication and responsiveness?**

REST menggunakan model request-response yang sederhana dan stateless, di mana setiap interaksi berdiri sendiri. Ini membuatnya mudah dipahami tetapi kurang efisien untuk komunikasi real-time karena membutuhkan polling atau mekanisme tambahan seperti WebSocket. gRPC dengan bidirectional streaming memungkinkan komunikasi dua arah yang persisten dan efisien, sehingga jauh lebih responsif untuk kebutuhan real-time seperti chat atau live data feed.

**10. What are the implications of the schema-based approach of gRPC, using Protocol Buffers, 
compared to the more flexible, schema-less nature of JSON in REST API payloads?**

Penggunaan Protocol Buffers pada gRPC memastikan setiap pesan memiliki struktur yang terdefinisi ketat sehingga mengurangi risiko kesalahan format data dan mempermudah validasi secara otomatis. Sebaliknya, JSON pada REST bersifat fleksibel dan mudah dibaca manusia, namun membutuhkan validasi manual dan rentan terhadap perubahan struktur yang tidak terdokumentasi. Pendekatan schema-based gRPC lebih cocok untuk sistem skala besar yang membutuhkan konsistensi data, sementara JSON lebih mudah digunakan untuk API publik yang sering berubah.