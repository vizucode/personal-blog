---
title: "Berkenalan dengan Message Broker untuk Efisiensi Microservices"
description: "Message broker adalah jembatan komunikasi dalam konsep asynchronous, yang bertujuan untuk meningkatkan efisiensi waktu dan keandalan microservices."
tags: ["microservice", "rabbit-mq"]
createdAt: 2024-07-07T08:02:00+07:00
updatedAt: 2024-07-07T08:02:00+07:00
thumbnail: ""
author: "Hafiz Iqbal"
---

Hai Solo Learner! sebagai seorang programmer apakah kamu pernah membayangkan bagaimana sebuah aplikasi terkenal semacam Netflix, Whatsapp, mungkin E-Commerce langganan kamu Shopee atau tokopedia melakukan jutaan  transaksi?

Salah satu teknologi yang banyak diadopsi untuk memfasilitasi komunikasi antar layanan dalam arsitektur microservice adalah Message Broker. Teknologi ini memungkinkan layanan-layanan yang berbeda untuk bertukar pesan secara efisien dan andal, tanpa harus saling bergantung secara langsung. Dengan menggunakan message broker, sistem dapat diatur menjadi lebih terdistribusi dan scalable, serta meminimalisir risiko kegagalan. Message broker memainkan peran krusial dalam memastikan data dapat dikirim dan diterima dengan tepat waktu dan aman, memungkinkan arsitektur microservice berfungsi secara optimal.

Saat ini, kebanyakan perusahaan teknologi besar sudah menggunakan message broker untuk menangani banyak request, karena keandalan mereka dalam menjaga data dengan aman dan memastikan setiap pesan sampai ke tujuan. Dalam dunia teknologi, message broker bertindak seperti tukang pos yang sangat canggih, memastikan setiap pesan sampai ke tujuan dengan aman dan tepat waktu. Untuk memahami lebih jauh tentang message broker dan penggunaannya secara tepat, mari kita bahas lebih lanjut! Selain itu, akan ada sesi praktikum (hands-on) menggunakan message broker secara lokal.

## Pengenalan Komunikasi Asyncronus Dan Syncronus
Konsep komunikasi dengan Message Broker berbeda dengan komunikasi yang biasa dilakukan oleh sebagian besar sistem yang menggunakan RPC atau REST. Sementara RPC (Remote Procedure Call) dan REST (Representational State Transfer) bergantung pada jenis komunikasi Syncronus, di mana pengirim data harus menunggu respons sebelum melanjutkan, message broker menggunakan jenis komunikasi messaging yang bersifat Asyncronus. 

Dalam komunikasi Asyncronus, pesan dapat dikirim tanpa harus menunggu penerimaan dan respons dari pihak penerima. Hal ini memungkinkan pengirim untuk terus beroperasi dan mengirim pesan lainnya tanpa terhambat oleh latensi atau kegagalan di sisi penerima. Komunikasi Asyncronus ini sangat penting dalam arsitektur microservice, di mana banyak layanan perlu berinteraksi secara bersamaan dan tidak ingin terganggu oleh keterlambatan komunikasi.

Dengan message broker, pesan dapat dikirimkan ke antrian atau topik tertentu dan kemudian diproses oleh layanan yang berlangganan pada antrian atau topik tersebut. Ini akan kita bahas di sesi selanjutnya.

## Apa itu Message Broker?

Message broker adalah suatu layanan yang secara umum digunukan untuk melakukan pertukaran informasi antar service, Ibaratnya kamu (service pengirim) mengirim sebuah paket ke ekspedisi jasa kirim (message broker) dan kemudian ekspedisi mengirim paketmu ke penerima (service penerima) dan ekspedisi itu memberikanmu garansi jika paket rusak atau hilang, itu merupakan analogi sederhana sebuah message broker, berikut diagram lebih jelasnya!

![Analogi Message Broker]('https://ucarecdn.com/e16abbd3-6ea4-4e10-bb70-8f31b6f8855d/-/preview/900x900/-/quality/smart/-/format/auto/')

### AMQP (Advanced Message Queuing Protocol)
Di dalam komunikasi menggunakan message broker protocol yang digunakan adalah AMQP (Advanced Message Queuing Protocol), protocol inilah yang digunakan standarisasi oleh beberapa layanan message broker terkenal seperti RabbitMQ, Kafka, Solace dll.

### Komponen Utama dalam AMQP
Terdapat beberapa istilah dalam komunikasi menggunakan amqp:
- **Exchange**: Analogikan seperti kotak surat, penerima akan mengirim melalui kotak surat / paket di kantor pos. Exchange akan meneruskan data ke Queue.

- **Queue**: Analogikan seperti jasa ekspedisi yang menampung semua paket / surat, jadi Queue berfungsi sebagai penyimpanan yang akan di ambil oleh penerima.

- **Binding**: Hubungan antara Queue dengan Exchange, jadi binding itu ibaratnya menghubungkan antara exchange dan queue bisa jadi beberapa queue tidak menerima data dari tipe bind exchange tertentu. terdapat beberapa tipe binding yakni: direct, topic dan fanout

- **Subscriber / Consumer**: Penerima data yang akan menerima data dari Queue (Tukang Pos)

- **Publisher / Sender**: Pengirim data yang akan mengirim data ke Exchange, dan tidak akan pernah tau data tersebut di konsumsi oleh service apa saja!

Berikut adalah diagram dari rangkaian diatas:

![Diagram Microservice Memakai Message Broker Sederhana]('https://ucarecdn.com/1f6a261b-c08e-4525-992f-e72cadc1a4af/-/preview/900x900/-/quality/smart/-/format/auto/')

## Mengapa Harus Pakai Message Broker?

#### Kelebihan menggunakan message broker
Penggunaan message broker sangat efektif dalam pertukaran banyak data dan membutuhkan response yang cepat secara Asyncronus, dan juga keandalan dalam menjaga transfer data antar service 1 dan service lainnnya.

Beberapa layanan message broker terkenal menawarkan fitur namanya **Retry Failure**, dimana jikalau terdapat service consumer yang mati, maka data tersebut akan dikirim kembali jika service penerima yang mati tersebut sudah bisa menerima data lagi. Lebih cocoknya kita sebut sebagai Garansi Data.

Tak hanya itu, keunggulan ketika kita menggunakan message broker arsitektur microservice menjadi lebih tertata dan tidak complex karena pertukaran antar data sentralisasi menggunakan message broker, dan mengurangi ketergantungan antar service.

#### Kekurangan menggunakan message broker
Kekurangan dari penggunaan message broker, arsitektur microservice kita sangat ketergantungan pada message broker itu sendiri. Ketika service message broker kita mati maka seluruh lalu lintas komunikasi kita akan lumpuh!

## Hands-On Penggunaan Message Broker!
Sekarang kita akan membuat sebuah layanan microservice sangat sederhana menggunakan message broker sebagai jalur lalu lintasnya!, kita akan membuat sebuah microservice seperti yang ditujukkan diagram berikut:

![Diagram Microservice Memakai Message Broker Sederhana]('https://ucarecdn.com/1f6a261b-c08e-4525-992f-e72cadc1a4af/-/preview/900x900/-/quality/smart/-/format/auto/')

#### Prerequisite
- Docker version 24.0.2 and Up
- Docker Compose version v2.18.1 and Up

#### 1. Inisialisasi Project
```bash
    mkdir microservice-with-message-broker

    cd microservice-with-message-broker

    mkdir payment-svc

    mkdir shipping-svc

    mkdir notification-svc

    mkdir message-broker-docker
```

#### 2. Struktur Folder
```
microservice-with-message-broker/
├── payment-svc
├── shipping-svc
├── notification-svc
└── message-broker-docker
```
- **payment-svc**: Digunakan sebagai publisher yang mengirim data ke Message Broker.

- **shipping-svc**: Digunakan sebagai subscriber yang menerima data dari queue.

- **notification-svc**: Digunakan sebagai subscriber lain yang menerima data dari queue.

- **message-broker-docker**: digunakan untuk menjalankan server message broker, menggunakan docker compose

#### 3. Inisialisasi Server Message Broker RabbitMQ Dengan Docker Compose

Untuk dapat menjalankan microservice ini, kita perlu menghidupkan dahulu message brokernya, disini kita menggunakan layanan message broker yang terkenal cepat dan ringan yakni Rabbit MQ

Buat sebuah file di **message-broker-docker** bernama `docker-compose.yaml` kemudian masukkan script berikut:

```yaml
version: '3'

services:
  rabbitmq-broker:
    image: rabbitmq:management-alpine
    ports:
      - 5672:5672
      - 15672:15672
    environment:
      RABBITMQ_DEFAULT_USER: testing
      RABBITMQ_DEFAULT_PASS: secrettesting
```

Setelah itu kita jalankan script dibawah ini, untuk menjalankan docker compose kita
```bash
docker compose up -d
```

Untuk mengujinya, Kemudian kita bisa akses ke `http://localhost:15672` kemudian pastikan muncul halaman message broker management ini

![Halaman Login RabbitMQ Management]('https://ucarecdn.com/b6b32a9e-f747-4ac6-a0ae-f794a176297c/-/preview/900x900/-/quality/smart/-/format/auto/')

Setelah itu login menggunakan credential berikut, yang sudah di setup di docker-compose bagian environtment
```
username: testing
password: secrettesting
```

Dan pastikan sukses masuk ke halaman management RabbitMQ seperti ini:

![Halaman RabbitMQ Management Rabbit MQ]('https://ucarecdn.com/fcb06836-8495-49b3-b6fb-c6c0b4aab322/-/preview/900x900/-/quality/smart/-/format/auto/')

#### 4. Setup RabbitMQ
Setelah RabbitMQ berlajan, kita akan melakukan beberapa langkah untuk menambahkan Exchange, Binding Dan Juga Queues

##### Menambahkan Exchange
Untuk menambahkan Exchange masuk ke Tab `Exchanges` dan klik `Add a new exchange`, seperti dibawah ini:

![Add New Exchange]('https://ucarecdn.com/8da0bea4-a6ee-4e74-b08a-c015f100de26/-/preview/500x500/-/quality/smart/-/format/auto/')

Isikan form berikut:
- **Name**: Berguna untuk menambahkan nama exchange yang akan di buat, isikan dengan `event` atau bebas
- **Type**: Berguna untuk binding ke Queue, isikan dengan tipe `topic`, topic akan membuat binding key bisa menggunakan regex

Setelah itu klik `Add Exchange` untuk menambahkan Exchange

##### Menambahkan Queue
Untuk menambahkan Queue kita masuk ke Tab `Queues and Streams`

Lalu buka dropdown menu `Add a new queue` dan kemudian isikan form berikut:
- **Type**: Tipe dari queue / algoritma queue, gunakan Quorum
- **Name**: Masukkan nama dari queue yang akan dibuat
- **Durability**: Pilih `Durable` karena queue akan tetap ada ketika RabbitMQ di restart kembali

Kemudian tambah queue dengan cara `add queue`

##### Membuat Binding
Ketika queue selesai dibuat pastikan queue bisa dilihat di tabel seperti berikut:

![Table Queues]('https://ucarecdn.com/c960d65b-8b64-4ae2-bce0-6251df9b5245/-/preview/500x500/-/quality/smart/-/format/auto/')

Nah, setelah queue muncul kamu buat sebuah binding dengan cara klik nama queue tersebut dan halaman akan diarahkan seperti ini:

![Halaman Binding Exchange]('https://ucarecdn.com/1bd7d724-f463-42f0-bacd-d3ddb521537c/-/preview/500x500/-/quality/smart/-/format/auto/')

Buka dropdown `Bindings`, dan isikan Form Berikut
- **From Exchange**: Isikan dengan nama exchange yang sudah dibuat
- **Routing Key**: Routing Key berfungsi membuat sebuah path untuk mengirim sebuah data ke queue dengan routing key yg sama, isikan dengan `event.*`, berarti semua data yang dikirim exchange `event.*` akan di consume oleh queue yang ter bind

Jika sudah, klik `Bind`, maka queue dan exchange otomatis terhubung

#### 5. Membuat Service Payment sebagai publisher
Untuk membuat publisher kita menggunakan bahasa pemrograman Golang, Masuk kedalam folder `payment-svc` dan lakukan langkah ini

```bash
go mod init paymentsvc
go get github.com/rabbitmq/amqp091-go 
```

Ini akan menginstal library protocol amqp, digunakan untuk komunikasi ke message broker.

Buat file broker.go, kode ini berfungsi object untuk mengirim data dari service `payment-svc` ke message broker:

```go

package main

import (
	"context"
	"log"
	"time"

	"github.com/rabbitmq/amqp091-go"
)

type broker struct {
	addr string
	conn *amqp091.Connection
	ch   *amqp091.Channel
}

func NewBroker(addr string) *broker {
	conn, err := amqp091.Dial(addr)
	if err != nil {
		log.Fatal(err)
	}

	ch, err := conn.Channel()
	if err != nil {
		log.Fatal(err)
	}

	return &broker{
		addr: addr,
		conn: conn,
		ch:   ch,
	}
}

func (b *broker) Send(payload []byte) error {
	defer b.conn.Close()

	ctx, cancel := context.WithTimeout(context.TODO(), 3*time.Second)
	defer cancel()

	err := b.ch.PublishWithContext(ctx,
		"event",
		"event.*",
		false,
		false,
		amqp091.Publishing{
			ContentType: "application/json",
			Body:        payload,
		},
	)

	if err != nil {
		log.Println(err)
		return nil
	}

	log.Println("sended successfully...")

	return nil
}

```


kemudian kita buat file main.go, berfungsi untuk menjalankan REST API, rest API disini berfungsi untuk interface pengguna / client request saja, berikut codenya:

```go
package main

import (
	"encoding/json"
	"fmt"
	"io"
	"log"
	"net/http"

	"math/rand"
)

type Payload struct {
	Id          uint
	AccountNo   string `json:"account_no"`
	BankName    string `json:"bank_name"`
	AccountName string `json:"account_name"`
}

func main() {
	http.HandleFunc("/payment", func(w http.ResponseWriter, r *http.Request) {

		if r.Method != http.MethodPost {
			w.Header().Set("Content-Type", "application/json")
			json.NewEncoder(w).Encode(map[string]interface{}{
				"message": "method not allowed",
				"data":    nil,
			})
			return
		}

		body, err := io.ReadAll(r.Body)
		if err != nil {
			log.Fatal(err)
		}

		var payload Payload
		err = json.Unmarshal(body, &payload)
		if err != nil {
			log.Fatal(err)
		}
		payload.Id = uint(rand.Intn(100))

		payloadMinify, err := json.Marshal(payload)
		if err != nil {
			log.Fatal(err)
		}

		// send to broker
		err = NewBroker("amqp://testing:secrettesting@localhost:5672/").Send(payloadMinify)
		if err != nil {
			log.Fatal(err)
		}

		// response to client
		w.Header().Set("Content-Type", "application/json")
		json.NewEncoder(w).Encode(map[string]interface{}{
			"message": "sended to broker..",
		})
	})

	fmt.Println("runnning on http://localhost:8081")
	http.ListenAndServe(":8081", nil)
}

```

#### 5. Membuat Service Notify sebagai consumer pertama
Untuk membuat consumer kita menggunakan bahasa pemrograman Golang juga, Masuk kedalam folder `notify-svc` dan lakukan langkah ini

```bash
go mod init notifysvc
go get github.com/rabbitmq/amqp091-go 
```

Kemudian buat sebuah file main.go, disini kita membuat receiver / penerima yang terus menerus menerima dari channel broker, Hal ini akan mendapatkan data secara Realtime ketika queue mendapatkan data baru.

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"

	"github.com/rabbitmq/amqp091-go"
)

func main() {
	addr := "amqp://testing:secrettesting@localhost:5672/"

	conn, err := amqp091.Dial(addr)
	if err != nil {
		log.Fatal(err)
	}
	defer conn.Close()

	ch, err := conn.Channel()
	if err != nil {
		log.Fatal(err)
	}
	defer ch.Close()

	dlv, err := ch.Consume(
		"notify",
		"",
		true,
		false,
		false,
		false,
		nil,
	)
	if err != nil {
		log.Fatal(err)
	}

	for d := range dlv {
		var body map[string]interface{}

		fmt.Println("service notify received data...")
		err := json.Unmarshal(d.Body, &body)
		if err != nil {
			log.Println(err)
		}

		fmt.Println(body)
	}
}
```

#### 6. Membuat Service Shipping sebagai consumer kedua
Untuk membuat consumer kita menggunakan bahasa pemrograman Golang juga, Masuk kedalam folder `shipping-svc` dan lakukan langkah ini

```bash
go mod init shipping
go get github.com/rabbitmq/amqp091-go 
```

Kemudian buat sebuah file main.go, disini kita membuat receiver / penerima yang terus menerus menerima dari channel broker, Hal ini akan mendapatkan data secara Realtime ketika queue mendapatkan data baru.

```go
package main

import (
	"encoding/json"
	"fmt"
	"log"

	"github.com/rabbitmq/amqp091-go"
)

func main() {
	addr := "amqp://testing:secrettesting@localhost:5672/"

	conn, err := amqp091.Dial(addr)
	if err != nil {
		log.Fatal(err)
	}
	defer conn.Close()

	ch, err := conn.Channel()
	if err != nil {
		log.Fatal(err)
	}
	defer ch.Close()

	dlv, err := ch.Consume(
		"shipping",
		"",
		true,
		false,
		false,
		false,
		nil,
	)
	if err != nil {
		log.Fatal(err)
	}

	for d := range dlv {
		var body map[string]interface{}

		fmt.Println("service shipping received data...")
		err := json.Unmarshal(d.Body, &body)
		if err != nil {
			log.Println(err)
		}

		fmt.Println(body)
	}

}
```

#### 7. Jalankan Microservices
Setelah kita membuat beberapa service diatas, kita jalankan secara pararel service diatas, kamu bisa buat beberapa tab terminal dan buka tiap service di terminal baru.

- Terminal 1
```bash
    cd payment-svc
    go run .
```

- Terminal 2
```bash
    cd notify-svc
    go run .
```

- Terminal 3
```bash
    cd shipping-svc
    go run .
```

Pastikan jalan semua seperti contoh dibawah:
##### Terminal 1
![service payment-svc]('https://ucarecdn.com/b71afdcb-fa67-4b35-ac60-d6b10d4ff995/-/preview/500x500/-/quality/smart/-/format/auto/')
##### Terminal 2
![service notify-svc]('https://ucarecdn.com/6314b4fa-8861-4fc7-9d8d-06db99a81e99/-/preview/500x500/-/quality/smart/-/format/auto/')
##### Terminal 3
![service shipping-svc]('https://ucarecdn.com/1500c5ec-28ce-4305-82a1-e0c31d2f7872/-/preview/500x500/-/quality/smart/-/format/auto/')

#### 8. Uji Coba
Langkah terakhir kita lakuin uji coba, jadi ekspektasi kita ketika publisher `payment-svc` mengirim suatu pesan maka consumer `notify-svc` dan `shipping-svc`

Lakukan request ke REST-API menggunakan postman dengan endpoint:

`http://localhost:8081/payment`

dengan body request seperti:
```json
{
    "account_no": "001232323",
    "bank_name": "BCA",
    "account_name": "Edward Newgate"
}
```

Pastikan response dari service `notify-svc` dan `shipping-svc` seperti ini:

##### Terminal 2
![service notify-svc]('https://ucarecdn.com/6314b4fa-8861-4fc7-9d8d-06db99a81e99/-/preview/500x500/-/quality/smart/-/format/auto/')
##### Terminal 3
![service shipping-svc]('https://ucarecdn.com/1500c5ec-28ce-4305-82a1-e0c31d2f7872/-/preview/500x500/-/quality/smart/-/format/auto/')

Jika, sudah mendapatkan response seperti diatas tandanya sudah sukses menjalankan message broker di microservices.

## Kesimpulan
Jadi, Message Broker merupakan teknologi yang sangat bermanfaat ketika kita berhadapan dengan microservices, dan dengan message broker juga data yang kita kirimkan akan aman karena message broker menjamin kandalannya, namun juga perlu diketahui bahwa message broker akan membuat ketergantungan sangat tinggi, makanya untuk mengurangi resiko downtime, perlu memilih message broker yang bagus.

