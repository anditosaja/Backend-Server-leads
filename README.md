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
   ``` bash
   cd gateaway-service
   npm install express http-proxy-middleware express-rate-limit morgan dotenv
   ```
2. Main Service
   ``` bash
   cd main-service
   npm install express bcryptjs jsonwebtoken mysql2 amqplib
   ```
3. Worker Service
   ``` bash
   cd worker-service
   npm install amqplib
   ```

## Konfigurasi RabbitMQ
Menjalankan RabbitMQ: 
``` bash
sudo systemctl start rabbitmq-server 
```
Cek status: 
``` bash 
sudo systemctl status rabbitmq-server
```

## Konfigurasi Database MySQL
Masuk ke MySQL didalam server remote (LeaDS)
``` Bash
mysql -u mahasiswa -p
```
*masukkan password server remote*
- Import database:
``` bash
mysql -u mahasiswa -p toko_db < schema.sql\
```
## Database Schema
1. Tabel Users

| Field | Tipe | 
| :--- | :--- | 
| id | INT | 
| username | VARCHAR | 
| password | VARCHAR | 
| email | VARCHAR | 
| role | ENUM(admin, customer) | 

2. Tabel Products

| Field | Tipe | 
| :--- | :--- | 
| id | INT | 
| name | VARCHAR | 
| price | DECIMAL | 
| stock | INT | 
| user_id | INT(FK users.id) |

## Running Service
1. Gateaway Service
 ``` bash
cd gateaway-service
node gateaway.js
```
Berjalan pada `http://103.147.92.134:4015`

2. Main Service
``` bash
cd main-service
node app.js
```
Berjalan pada `http://localhost:3115`

3. Worker Service
``` bash
cd worker-service
node consumer.js
```
Mendengarkan event RabbitMQ pada queue `product_logs (Setelah POST Product)`

## Endpoint API
Semua endpoint diakses melalui API Gateway: `http://103.147.92.134:4015`

## Daftar Endpoint
Testing dilakukan di Postman
1. Auth Service

| Method | Endpoint | Deskripsi |
| :--- | :--- | :--- |
| POST | /auth/register | registrasi user |
| POST | /auth/login | Login user |

*Request Body -> Raw JSON -> login user hanya memasukkan username dan password*

2. Product Service
| Method | Endpoint | Deskripsi |
| :--- | :--- | :--- |
| GET | /store/products | Ambil semua produk |
| POST | /store/products | Tambah produk |

*Header : `Authorization: Bearer <token>`*

## JWT Authentication
- Sistem menggunakan JWT untuk autentikasi user dan didapat dari POST login user.
- Token dikirim melalui header: `Authorization: Bearer <token>`
- Sistem memiliki 2 role: `admin & customer`
Hanya role admin yang dapat menambahkan produk.

## Message Broker (RabbitMQ)
- Sistem menggunakan RabbitMQ untuk komunikasi asynchronous.
- Saat produk berhasil ditambahkan:
  1. Main Service mengirim event ke RabbitMQ
  2. Worker Service menerima event
  3. Event diproses secara asynchronous
 
## API Gateway
API Gateway berfungsi sebagai:
1. Single Entry Point
2. Routing Request
3. Rate Limiting
4. Logging Request

Gateway berjalan pada port: `4015`
