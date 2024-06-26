# BambangShop Receiver App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a Rocket web framework skeleton that you can work with.

As this is an Observer Design Pattern tutorial repository, you need to implement a feature: `Notification`.
This feature will receive notifications of creation, promotion, and deletion of a product, when this receiver instance is subscribed to a certain product type.
The notification will be sent using HTTP POST request, so you need to make the receiver endpoint in this project.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Receiver" folder.

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    ROCKET_PORT=8001
    APP_INSTANCE_ROOT_URL=http://localhost:${ROCKET_PORT}
    APP_PUBLISHER_ROOT_URL=http://localhost:8000
    APP_INSTANCE_NAME=Safira Sudrajat
    ```
    Here are the details of each environment variable:
    | variable                | type   | description                                                     |
    |-------------------------|--------|-----------------------------------------------------------------|
    | ROCKET_PORT             | string | Port number that will be listened by this receiver instance.    |
    | APP_INSTANCE_ROOT_URL   | string | URL address where this receiver instance can be accessed.       |
    | APP_PUUBLISHER_ROOT_URL | string | URL address where the publisher instance can be accessed.       |
    | APP_INSTANCE_NAME       | string | Name of this receiver instance, will be shown on notifications. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)
3.  To simulate multiple instances of BambangShop Receiver (as the tutorial mandates you to do so),
    you can open new terminal, then edit `ROCKET_PORT` in `.env` file, then execute another `cargo run`.

    For example, if you want to run 3 (three) instances of BambangShop Receiver at port `8001`, `8002`, and `8003`, you can do these steps:
    -   Edit `ROCKET_PORT` in `.env` to `8001`, then execute `cargo run`.
    -   Open new terminal, edit `ROCKET_PORT` in `.env` to `8002`, then execute `cargo run`.
    -   Open another new terminal, edit `ROCKET_PORT` in `.env` to `8003`, then execute `cargo run`.

## Mandatory Checklists (Subscriber)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop-receiver to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create SubscriberRequest model struct.`
    -   [ ] Commit: `Create Notification database and Notification repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Notification repository.`
    -   [ ] Commit: `Implement list_all_as_string function in Notification repository.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-1" questions in this README.
-   **STAGE 3: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Commit: `Implement receive_notification function in Notification service.`
    -   [ ] Commit: `Implement receive function in Notification controller.`
    -   [ ] Commit: `Implement list_messages function in Notification service.`
    -   [ ] Commit: `Implement list function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Subscriber-2" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Subscriber) Reflections

#### Reflection Subscriber-1

1. RwLock (Read-Write Lock) adalah sejenis lock di mana data dapat diakses (di-read) oleh banyak thread sekaligus, namun data hanya dapat di-edit (write) oleh satu thread. Mutex (Mutual Exclusion) adalah sejenis lock di mana data hanya dapat diakses sepenuhnya (baik read atau write0 oleh satu thread di waktu tertentu. Untuk konteks notifikasi, tentunya kita tidak ingin semua thread bisa mengeditnya (yang boleh mengedit hanyalah pembuat notifikasi itu sendiri), karena dapat mengancam data integrity. Namun kita tetap ingin agar data notifikasi dapat diakses banyak thread (dalam kasus ini subscriber) dalam waktu yang sama. Jika tidak demikian, setiap subscriber harus mengantri untuk dapat mengakses notifikasi tertentu; tentu ini tidak efisien.

2. Rust tidak mengizinkan perubahan variabel static seperti Java demi alasan keamanan. Rust adalah bahasa yang dapat dijalankan secara concurrent, sehingga memilik risiko-risiko sendiri, seperti race condition dan lost update. Data yang mutable (termasuk data static) rawan terhadap kesalahan dalam pemrograman concurrent, dan masalahnya tidak selalu dapat ditemukan dengan mudah. Immutability meningkatkan prediktabilitas program paralel/concurrent sehingga lebih mudah untuk dikelola.

#### Reflection Subscriber-2

1. Lib.rs berisi dependencies yang diperlukan Rust untuk menjalankan aplikasi web, serta setting konfigurasi awal untuk mendukung aplikasi yang diambil dari file .env. Beberapa dependencies yang ada termasuk ```lazy_static``` untuk inisialisasi variable hanya saat pertama kali dibutuhkan saja, ```dotenv``` agar aplikasi dapat menggunakan konfigurasi dari file .env, ```rocket``` sebagai platform aplikasi web di Rust, dan ```reqwest``` untuk membuat request HTTP di Rust. Semua dependencies ini dispecify lebih jauh dengan adanya static variable ```REQWEST_CLIENT``` dan ```APP_CONFIG```, serta struct `AppConfig```. Terakhir, dibuat modul untuk menghandle jenis-jenis error yang dapat muncul dalam aplikasi.

2. Implementasi publisher dan subscriber yang terpisah memudahkan untuk menambahkan instance subscriber. Kita hanya perlu spawn beberapa instance subscriber sekaligus, dan main app dapat mengakomodasi subscriber yang banyak. Masing-masing subscriber dapat subscribe ke suatu product di main app, dan main app dapat menerima dan mengirim request ke banyak subscriber di waktu yang sama. Untuk spawning lebih dari satu Main app, menurut saya prosesnya akan lebih rumit. Hal ini dikarenakan publisher root url yang sudah diset oleh receiver, yaitu di port 8000 saja. Perlu dibuat konfigurasi agar receiver dapat mengakses publisher di port yang berbeda-beda. Selain itu, perlu dipastikan juga bahwa publisher yang berbeda tetap mengakses database yang sama. Untuk sekarang, saya belum bisa me-run main app menggunakan port yang berbeda, karena terjadi error: "Rocket failed to bind network socket to given address/port. Only one usage of each socket address (protocol/network address/port) is normally permitted. (os error 10048)"

3. Saya belum mencoba untuk membuat test yang baru. Saya hanya memodifikasi test yang sudah ada, contohnya mengganti port receiver menjadi 8002 atau 8003 untuk mengakses instance subscriber yang berbeda.
