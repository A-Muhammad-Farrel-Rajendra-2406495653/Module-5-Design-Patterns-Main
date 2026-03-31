# BambangShop Publisher App
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
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
##### 1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?  
Sejauh ini di BambangShop cukup menggunakan single Model struct saja karena baru ada satu jenis subscriber yang struktur data dan perilakunya sejenis, yaitu subscriber yang menerima notifikasi lewat URL.  
Lalu, penggunaan interface (trait) dalam Observer Pattern tetap diperlukan untuk menerapkan polymorphism jika nanti ada jenis subscriber yang baru, misal subscriber yang menerima notifikasi lewat email atau SMS.  

##### 2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?  
Penggunaan DashMap (dictionary) lebih cocok daripada Vec (list) karena:  
* URL menjadi identifier untuk seorang Subscriber, artinya url harus unik. Dengan DashMap, kita bisa jadikan url sebagai key dan value-nya adalah Subscriber, sehingga url untuk setiap Subscriber dijamin unik dan kita bisa identifikasi Subscriber lewat URL-nya, itu lebih cepat (O(1)) daripada iterasi satu-satu jika kita pakai Vec (O(N)).  

* Bentuk Singleton of Database dari Subscriber yang seperti ini: `DashMap<String, DashMap<String, Subscriber>> = DashMap::new();` menjamin bahwa dalam satu kategori produk, hanya ada satu Subscriber dengan URL yang sama. Ini akan memudahkan kita untuk melakukan operasi-operasi seperti mencari siapa saja Subscriber di suatu kategori produk atau CRUD Subscriber dengan URL tertentu di suatu kategori produk.  

##### 3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?  
Singleton berperan sebagai pola desain yang memastikan suatu objek Subscriber hanya ada satu di memori, sehingga state dari suatu objek Subscriber itu konsisten di seluruh bagian BambangShop.
Tapi, Singleton tidak otomatis menjamin data objek Subscriber itu konsisten saat diakses oleh banyak orang dalam satu waktu (tidak thread-safety). Maka dari itu, kita butuh DashMap untuk menjaga konsistensi data itu dengan memastikan hanya ada satu thread dalam satu waktu yang bisa memodisikasi data itu di memori. Jadi, Singleton dan DashMap ini saling melengkapi.


#### Reflection Publisher-2
##### 1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?
Pemisahan Service dan Repository dari Model dilakukan untuk menerapkan Single responsiblity Principle dan memudahkan kita untuk me-maintain kode. 
Model seharusnya hanya bertanggung jawab mendefinisikan skema data, sedangkan Service mengelola alur logika bisnis (seperti to_uppercase pada product_type) dan Repository mengisolasi detail mekanisme penyimpanan data (seperti DashMap). Dengan pemisahan ini, perubahan pada cara penyimpanan data tidak akan merusak logika bisnis, dan logika bisnis dapat diuji secara independen tanpa bergantung pada state penyimpanan.

##### 2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?
Jika kita hanya menggunakan Model, setiap model akan menjadi sangat kompleks karena harus menangani validasi, logika bisnis, sekaligus operasi basis data secara bersamaan. 
Interaksi antar model, seperti saat Notification perlu mencari daftar Subscriber untuk mengirim pesan, akan membuat model memiliki ketergantungan langsung (tight coupling) ke logika penyimpanan model lain. Dampaknya, akan ada satu file model berisi ratusan baris kode yang sulit dipahami, sulit diuji secara modular, dan rawan terjadi bug saat ada perubahan nantinya.


##### 3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.
Dalam proyek ini dan Postman Collection yang diberikan, Postman akan berperan menjadi klien dalam simulasi yang menguji fungsionalitas Publisher dan Receiver.
Dengan Postman Collection yang diberikan, saya bisa memvalidasi alur Observer pattern mulai dari pembuatan produk, pendaftaran subscriber menggunakan metode POST dengan bodi JSON, hingga verifikasi penerimaan notifikasi pada endpoint `/receive`.
Fitur Collections milik Postman juga membantu untuk mengorganisir berbagai request berdasarkan modul (Product, Notification, Receiver) serta kemampuan untuk menyertakan Raw JSON Body yang memudahkan pengujian struktur data kompleks seperti pada endpoint Subscribe to Type.
Mungkin untuk proyek kelompok di masa depan, saya tertarik mengeksplorasi Variables (seperti {{base_url}}) untuk mempermudah penggantian host dari localhost ke server production,
atau fitur Automated Testing untuk memastikan setiap perubahan kode tidak merusak endpoint yang sudah ada.

#### Reflection Publisher-3
###### 1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?
Variasi yang digunakan dalam tutorial ini adalah Push model, terlihat dari  NotificationService yang secara aktif mengririmkan data dalam bentuk objek Notification terhadap setiap Subscriber setelah terjadi perubahan status pada produk.
Itu terjadi lewat fungsi notify yang mengiterasi daftar subscriber dan langsung memanggil metode update dengan membawa payload data. Dalam model ini, subscriber bersifat pasif dan hanya menerima informasi yang telah ditentukan oleh publisher tanpa perlu melakukan permintaan data tambahan.

###### 2.  What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)
Kalau menggunakan Pull model, keuntungan utamanya adalah efisiensi jaringan karena publisher hanya mengirimkan notifikasi perubahan minimal (misalnya hanya ID produk), sehingga subscriber memiliki kendali penuh untuk hanya mengambil data yang mereka butuhkan. 
Tapi kerugiannya adalah kompleksitas kode akan meningkat karena subscriber harus melakukan request balik ke API publisher untuk mendapatkan detail produk, yang menyebabkan beban kerja tambahan pada server. 
Selain itu, terdapat resiko state produk sudah berubah kembali saat subscriber baru akan menarik data, sehingga data yang diterima berpotensi tidak lagi sinkron dengan saat kejadian berlangsung.

###### 3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.
Jika proses notifikasi tidak menggunakan multi-threading, program akan berjalan secara sinkron dan berurutan (blocking), yang berarti setiap pengiriman notifikasi via HTTP harus selesai sebelum beralih ke subscriber berikutnya. 
Hal ini akan menyebabkan performa aplikasi menurun drastis dan meningkatkan waktu respon pada endpoint produk (seperti create, delete, atau publish) seiring bertambahnya jumlah subscriber. 
Pengguna akan mengalami penundaan yang lama karena thread utama tertahan oleh proses I/O jaringan di dalam looping notifikasi, yang pada akhirnya dapat menyebabkan request timeout atau kegagalan sistem saat melayani banyak pengguna secara bersamaan.