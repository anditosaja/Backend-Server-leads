# Sistem Manajemen Toko - Microservices API Gateway

Sistem backend berbasis microservices menggunakan Node.js, MySQL, JSON Web Token (JWT), RabbitMQ Message Broker, dan API Gateway.
Project ini dibuat untuk memenuhi tugas Backend Service Remote ke Website misalnya LeaDS yang mencakup implementasi database, autentikasi JWT, komunikasi asynchronous menggunakan message broker, dan API Gateway sebagai single entry point.

## Arsitektur Sistem
Sistem terdiri dari beberapa service:

1. Main Service 
   - Registrasi & Login
   - JWT Authentication
   - CRUD Produk
   - Integrasi MySQL
   - Producer RabbitMQ
2. Worker Service
   - Menerima event produk secara asynchronous
   - Logging aktivitas produk
3. API Gateway
   - Routing request
   - Rate Limiting
   - Logging request client

Seluruh request dari client hanya melewati API Gateway.
`client -> Gateaway (Port 4015) -> Main Service (3115) -> MySQL Database
Main Service -> RabbitMQ -> Worker Service`

## Teknologi yang Digunakan
1. Node.js
2. Express.js
3. MySQL
4. JSON Web Token (JWT)
5. RabbitMQ
6. API Gateway
7. Express Rate Limit
8. Morgan Logger
9. Postman

## Instalansi
1. Gateaway Service
   ` bash
   cd gateaway-service
   npm install express http-proxy-middleware express-rate-limit morgan dotenv
   `
2. Main Service
   ` bash
   cd main-service
   npm install express bcryptjs jsonwebtoken mysql2 amqplib
   `
3. Worker Service
   ` bash
   cd worker-service
   npm install amqplib
   `

## Konfigurasi Database MySQL
Masuk ke MySQL didalam server remote (LeaDS)
` Bash
mysql -u mahasiswa -p`
*masukkan password server remote*
Import database:
` bash
mysql -u mahasiswa -p toko_db < schema.sql`

