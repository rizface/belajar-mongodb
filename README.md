# BELAJAR MONGO DB
![mongodb](https://webimages.mongodb.com/_com_assets/cms/kuyjf3vea2hg34taa-horizontal_default_slate_blue.svg?auto=format%252Ccompress)

## Cara melihat semua Database yang yang terdaftar
```shell
use namaDatabase
```

## Perintah pada Object Database
1. db.dropDatabase(), digunakan untuk menghapus database
2. db.getName(), digunakan untuk mendapatkan nama database yang sedang digunakan
3. db.hostInfo(), digunakan untuk mendapatkan informasi host tempat mongo DB diinstall
4. db.version(), digunakan untuk melihat versi dari database
5. db.stats, digunakan untuk melihat statistik penggunaan database

## Collection
Collection adalah tempat menyimpan document,Collection sama dengan table pada database relational

### Database Methods untuk Collection
1. db.getCollectionNames(), digunakan untuk mengambil semua nama collection
2. db.createCollection(name), digunakan untuk membuat collection baru
3. db.getCollection(name), digunakan untuk membuat collection
4. db.getCollectionInfos() mendapatkan semua informasi collection

### Collection Methods
1. db.<collection>.find(), mengambil semua document yang tersimpan pada sebuah collection
2. db.<collection>.count(), menghitung jumlah document
3. db.<collection>.drop(), menghapus collection
4. db.<collection>.totalSize(), mengambil total ukuran collection
5. db.<collection>.stats(), mengambil statistik penggunaan collection


### Data Modelling
1. penggunaan document database tidak akan mendatangkan manfaat yang besar jika tidak mengerti cara memodelkan data untuk kebutuhan aplikasi
2. saat memodelkan data menggunakan document database, kita harus mengacu pada penggunaan aplikasi dalam melakukan query, update dan proses data 
3. struktur document pada document database bukan mengacu pada seberapa normal atau seberapa simple strukturnya, tapi mengacu pada apakah struktur tersebut bisa mendukung query yang dilakukan

#### Macam - Macam Data Modelling
1. Embedded, data yang saling berhubungan disimpan dalam object yang sama
    - digunakan saat mengakses satu data harus selalu mengakses data yang lainnya 
    - digunakan jika saat terjadi perubahan pada satu data harus melakukan juga pada data lainnya

2. Referenced, data yang saling berhubungan disimpan dalam collection dan document yang berbeda
   - digunakan saat masing - masing object dapat berdiri sendiri
   - digunakan saat terjadi perubahan pada satu data tidak harus malakukan perubahan pada data yang lainnya

### Insert Document
1. db.<collection>.insertOne(document), menambah satu data ke collection
2. db.<collection>.insertMany(document), menambah banyak data sekaligus banyak ke collection

```javascript

// jika input data ke document tipe data otomatis menjadi double, untuk agar bisa menjadi integer 32 atau integer 64
// gunakan fungsi new NumberLong (int 64) atau new NumberInt (int32)

// insert one
db.products.insertOne({"name": "indomie kari ayam", "price": new NumberLong(3000)});

// insert many
db.products.insertMany([{ "name": "indomie kari ayam", "price":new NumberLong(2000)},{"name": "indomie soto medan","price": new NumberLong(3000)}]);

// embedded 
db.orders.insertOne( { "total": new NumberLong(8000), "items":[ {"product_id":1, "price": new NumberLong(2000), "quantity": new NumberLong(2)}, {"product_id":2,"price":new NumberLong(2000),"quantity": new NumberLong(2)}]});
```

### Query Document (GET)
```javascript
// SQL : SELECT * FROM products WHERE price = 2000;
db.products.find({price: 2000});

// SQL: SELECT * FROM products WHERE price = 2000 AND name = "indomie kari ayam"
db.products.find({price:2000, name:"indomie kari ayam"})

// Akses embedded object, gunakan titik(.)
db.orders.find({"items.price":2000})
```

#### Comparison Operator (Opeartor Perbandingan)
1. $eq, membandingkan value dengan value lain
2. $gt, membandingkan value lebih besar dai value lain
3. $gte, membandingkan value lebih besar atau sama dengan value lain
4. $lt, membandingkan value lebih kecil dari value lain
5. $lte, membandingkan value lebih kecil atau sama dengan value lain
6. $in, membandingkan value dengan value yang ada pada array
7. $nin, membandingkan value tidak ada dalam array
8. $ne, membandingkan value tidak sama dengan value lain


##### Cara Menggunakan Comparison Operator
operator ini digunakan untuk melakukan perbandingan

```javascript
// SELECT * FROM products WHERE price = 2000;
db.products.find({price:{$eq:2000}})

// SELECT * FROM products WHERE price > 2000;
db.products.find({price:{$gt:2000}});

// SELECT * FROM products WHERE price >= 2000;
db.products.find({price:{$gte:2000}})

// SELECT * FROM products WHERE price < 3000;
db.products.find({price:{$lt:3000}})

// SELECT * FROM products WHERE price <= 3000
db.products.find({price:{$lte:3000}})

// SELECT * FROM product WHERE category IN ("laptop", "handphone)
db.products.find( { category:{$in:["handphone","laptop"]}})
```

#### Logical Operator
operator ini digunakan untuk melakukan operasi logika

1. $and, menggabungkan query dengan operasi AND
2. $or, menggabungkan query dengan operasi OR
3. $nor, menggabungkan query dengan operasi NOR,mengembalikan document yang gagal disemua kondisi
4. $not, mengembalikan document yang tidak sesuai kondisi

```javascript
// SELECT * FROM products WHERE price = 3000 AND name = 'indomie kari ayam'
db.products.find({$and:[{price:3000},{name:"indomie kari ayam"}]})

// SELECT * FROM products WHERE prince = 3000 OR price < 25000000
db.products.find({$or:[{price:3000},{price:{$lt:25000000}}]});

// SELECT * FROM products WHERE price != 3000 (Logical Operator)
db.products.find({price:{$not:{$eq:3000}}}) // Logical Operator
db.products.find({price:{$ne:3000}}) // Comparison Operator
```

#### Element Operator
operator ini digunakan untuk melakukan pengecekan pada field yang ada pada dokumen
1. $exists, mencocokan document yang memiliki field tersebut (NOT NULL jika pada SQL)
2. $type, mencocokan document yang memiliki type field tersebut

```javascript
// SELECT * FROM products WHERE category IS NULL;
db.products.find({category:{$exists:false}})

// SELECT * FROM products WHERE category IS NOT NULL;
db.products.find({category:{$exists:true}})
```

#### Evaluation Operator

#### Full Text Search

1. buat index terlebih dahulu, gunanya agar tau harus mencari di field yang mana
2. buat query pencarian

```javascript
// membuat index
// text adalah index untuk full text search
db.products.createIndex({name:"text", "price": "text", category:"text"})

// fulltext
db.products.find({$text:{$search:"sedap"}})
```

#### Array Operator
1. $all, mencocokan array yang mengandung elemen - elemen tertentu
2. $elemMatch, mengambil dokument jika tiap element di array memenuhi kondisi tertentu
3. $size, mengambil dokument jika ukuran array sesuai

```javascript
// SELECT * FROM products WHERE tags = "monitor" AND tags = "mouse
db.products.find({tags:{$all:["monitor","mouse"]}})

// SELECT * FROM products WHERE tags IN ("monitor","mouse")
db.products.find({tags:{$elemMatch:{$in:["samsung","monitor","mouse"]}}})
```

#### Projection Operator
memilih field mana yang akan ditampilkan atau di hide

1. $, jika hasil array maka akan mengembalikan hasil pertama dari array yang match dengan array operator
2. $elemMatch, mengembalikan data pertama array yang match dengan kondisi query
3. $meta, mengembalikan informasi metadata yang didapat dari setiap matching document
4. $slice, mengontrol jumlah data yang ditampilkan pada array

```javascript
// SELECT id,namce,price,category FROM products WHERE category IS NOT NULL AND tags IN NOT NULL
db.products.find({$and:[{tags:{$exists:true}},{category:{$exists:true}}]},{"name":1,"price":1,"category":1})

// membatas jumlah array yang ingin ditampilkan
db.products.find({$and:[{tags:{$exists:true}},{category:{$exists:true}}]},{"name":1,"price":1,"category":1,tags:1,"tags":{$slice:2}})

// hanya menampilkan array pertama yang sesuai dengan array operator
db.products.find({tags:{$all:["monitor"]}},{name:1,price:1,category:1,"tags.$":1})

// digunakan untuk mendapatkan metadata dari dokumen, dalam kasus ini mendapatkan textScore dari fulltext search
// yang berisi relevansi data yang didapat dengan data yang dicari
db.products.find({$text:{$search:"monitor"}},{score:{$meta:"textScore"}})
```

#### Query Modifier
Query Modifier adalah memodifikasi hasil query yang telah dilakukan, contohnya adalah mengubah hasil query menjadi jumlah data.

1. count(), menganbil jumlah data hasil query
2. limit(size), membatasi jumlah data yang didapat dari query
3. skip(size), menghiraukan data pertama hasil query sejumlah data yang ditentukan
4. sort(query), mengurutkan hasil query

```javascript
// SELECT COUNT(*) FROM products;
db.products.find().count()

// SELECT * FROM products LIMIT 3
db.products.find().limit(3)

// SELECT * FROM products OFFET 3
db.products.find().skip(3)

// SELECT * FROM products LIMIT 4 OFFSET 4 (Pagination)
db.products.find().skip(4).limit(4)


// SELECT * FROM product ORDER BY price ASC
db.products.find().sort({price:1})

// SELECT * FROM products ORDER BY price DESC
db.products.find().sort({price: -1})

// SELECT * FROM products ORDER BY price DESC,name ASC
```

### Update Document
Formula : db.<collection>.<updateFunction>()

1. updateOne(), mengubah satu document
2. updateMany(), mengubah banyak document sekaligus
3. replaceOne(), mengubah satu document dengan document baru

sifat update disini adalah, melakukan update jika field ada, dan membuat field baru jika tidak ada

1. $set, membuat / update field
2. $unset, menghapus field

```javascript
// UPDATE products SET category = "food" WHERE id = ?
db.products.updateOne({"_id": ObjectId("61be9243385b24e489a83af5")},{$set:{"category":"food"}})

// UPDATE products SET category = "food" WHERE category IS NULL
db.products.updateMany({category:{$exists:false}},{$set:{category:"food"}})

// ALTER TABLE products DROP COLUMN wrong
db.products.updateMany({},[{$unset:["wrong"]}])
```

#### Field Update Operator
1. $set, mengubah nilai field di document
2. $unset, menghapus field dari document
3. $inc, menaikan nilai number di field sesuai dengan jumlah tertentu
4. $rename, mengubah nama field do document
5. $currentDate, mengubah field menjadi waktu saat ini

```javascript
// UPDATE products SET price = 3000 WHERE "_id" = 1;
db.products.updateOne({"_id": 1}, {$set:{"price":3000}})

// ALTER TABLE products DROP COLUMN wrong
db.products.updateMany({},[{$unset:["wrong"]}])

// ALTER TABLE products RENAME COLUMN wrong TO harga
db.products.updateMany({},{$rename:{price:"harga"}})

// UPDATE products SET harga = harga + 100 WHERE harga = 3000;
db.products.update({harga:{$eq:3000}},{$inc:{harga:100}})

// UPDATE products SET harga = harga - 100;
db.products.updateMany({},{$inc:{harga:-100}})

// current date
db.products.updateMany({},{
    $currentDate:{
        created_at:{
            $type:"timestamp"
        },
       updated_at: {
            $type: "date"
       }
    }
})
```

#### Array Update Operator
1. $, mengupdata data pertama sesuai kondisi query
2. $[], mengupdate semua data array sesuai kondisi query
3. $[<identifier>], mengupdate semua data array yang sesuai dengan arrayFilters
4. <index>, mengupdate data array sesuai dengan nomor index

kondisional tidak didasarkan pada field lain, tetapi pada array itu sendiri
bukan id = ?, tapi array = ?

```javascript
// update data pertama array sesuai dengan kondisi yang ditetapkan
{
   db.products.updateMany({tags: {$exists: true}}, {
      $set: {
         "tags.$": "Jadi yang pertama"
      }
   })
}

db.orders.updateOne({orders:"sabun"},{$set:{"tags.$":"tahu"}})

// update nilai array menjadi sama semuanya
db.products.updateMany({tags:{$exists:true}},{
    $set:{
        "tags.$[]": "semua nilai jadi sama"
    }
})
```

5. $addToset, menambahkan value ke array, dihiraukan jika sudah ada
6. $pop, menghapus element pertama jika(-1) atau terakhir jika(1)
7. $pull, menghapus semua element array yang sesuai kondisi
8. $push, menambahkan element ke array
9. $pullAll, menghapus seluruh element yang ada pada array

```javascript
db.products.updateOne({tags:"beauty"},{$push:{tags:"kecantikan"}})

// jika query dibawah dijalankan saat value 'wanita' tidak ada dalam array, maka value 'wanita' akan ditambahkan kedalam array
db.products.find({tags:{$in:["beauty"]}},{$addToSet:{tags:"kecantikan"}})
// tapi jika value 'wanita' sudah ada maka query akan diabaikan

// ini akan menghapus nilai array yang paling akhir
db.products.updateOne({tags:"beauty"},{$pop:{ratings:1}})

// ini akan menghapus nilai array yang paling depan
db.products.updateOne({tags:"beauty"},{$pop:{ratings:-1}})

// ini akan menambah nilai array, dipaling akhir
db.products.updateOne({tags:"beauty"},{$push:{ratings:100}})

// ini akan menghapus semua nilai array yang sesuai dengan kondisi
db.products.updateOne({tags:"beauty"},{$pull:{ratings:{$gte:100}}})

// ini akan menghapus semua nilai array
```

10. $each, digunakan pada $addToSet dan $push untuk menambahkan multiple element
11. $position, digunakan di $push untuk mengubah posisi menambahkan data
12. $slice, digunakan pada $push untuk menentukan jumlah maksimal data array
13. $sort, digunakan untuk mengurutkan array setelah operasi $push

```javascript
// digunakan untuk menambahkan banyak data kedalam array, tapi bukan sebagai array, melaikan sebagai value biasa
db.products.update({tags:"wanita"},{$push:{tags:{$each:["kesehatan wanita","cantik itu sehat"]}}})

// sedangkat syntax dibawah akan menambahkan value baru ke dalam array berupa array lagi
db.products.update({tags:"wanita"},{$push:{tags:["wanita cantik","sehat"]}})

// menambah nilai pada array tapi tidak masuk kepaling belakang melainkan bisa dipilih ingin berada pada index berapa
db.products.update({tags:"wanita"},{$push:{tags:{$each:["sabun muka"],$position:0}}})
```

### Bulk Write Operation
mengirimkan perintah sekaligus banyak / batch, dan hanya akan di respon sekali oleh mongo db

#### Supported Bulk Write Operation
1. insertOne
2. updateOne
3. updateMany
4. replaceOne
5. deleteOne
6. deleteMany

```javascript
// menjalankan 3 operasi sekaligus, tapi hanya 1 response
db.products.bulkWrite([{
    insertOne:{
        document:{
            "name":"khaf",
           "price":30000
        }
    }},
   {deleteOne:{
       filter:{
           "_id":ObjectId("61be924c385b24e489a83af6")
       }
   }}
   ,{
    updateMany:{
        filter:{"name":"khaf"},
       update:{
            $set:{
                "name":"khif khaf"
            }
        }
    }
}
])
```

### Schema Validation 
schema validation ini berguna untuk melakukan validasi terhadap struktur json yang akan masuk ke database
```javascript
// membuat collection baru dengan schema validation
db.createCollection("merchants", {
   validationAction: "error",
   validator: {
      $jsonSchema: {
         bsonType: "object",
         required: ["name", "balance", "type", "address"],
         properties: {
            name: {
               bsonType: "string",
               description: "Must be a string"
            },
            balance: {
               bsonType: "long",
               description: "Must be a long"
            },
            type: {
               enum: ["PREMIUM", "REGULAR"],
               description: "Can only be one of enum values"
            },
            address: {
               bsonType: "object",
               required: ["street", "city"],
               properties: {
                  street: {
                     bsonType: "string",
                     description: "Must be a string"
                  },
                  city: {
                     bsonType: "string",
                     description: "Must be a string"
                  },
                  country: {
                     bsonType: "string",
                     description: "Must be a string"
                  }
               }
            }
         }
      }
   }
})


// menambahkan schema validation pada existing collection
db.runCommand({
   collMod: "products",
   validationAction: "error",
   validator: {
       $jsonSchema: {
           bsonType: "object",
            required: ["name"],
          properties: {
               name: {
                   bsonType: "string",
                  description: "Must Be String"
               }
          }
       }
   }
})
```

### Indexes
index adalah fitur untuk mengefisiensikan proses query. tanpa ada index MongoDB harus mencari melakukan proses query dengan cara collection scan (mencari seluruh data dari awal hingga akhir)

1. db.<collection>.createIndex, membuat index di collection
2. ddb.<collection>.getIndexes, melihat semua index pada collection
3. db.<collection>.dropIndex, menghapus index di collection

```javascript
// single field index
db.collection.createIndex({
   field: 1, // ascending
})

// compound index (banyak field dalam satu index), batas maksimal 32 field dalam 1 index
db.collection.createIndex({
   field: 1, // ascending
   field: -1, // descending
})

// jika menggunakan compound index, ketika melakukan query harus berurutan sesuai dengan index yang dideklarasikan

db.collection.createIndex({
   stok: 1, 
   name: 1
})
    
// index hanya berlaku jika parameter sort sama dengan parameter saat membuat index atau kebalikannya
db.collection.find().sort({stok: 1, name: 1}) // ini sort menggunakan index
db.collection.find().sort({stok: -1, name: -1}) // ini tidak sort menggunakan index   
    
// urutan
stok // ini kena index
stok,name // ini kena index
name // ini tidak kena index
name,stok // ini tidak kena index

db.collection.find({stok:{$gt:10}})
```

Note: **Single dan Compound index digunakan jika ingin melakukan filter berdasarkan field tertentu** 

#### Indexing Strategy
1. buat index untuk meningkatkan performa query
   1. gunakan single index, jika query menggunakan 1 field saja
   2. gunakan compound index, jika sering melakukan query menggunakan kombinasi field secara berurutan
2. buat index untuk mengurutkan hasil query
3. sering gunakan function explain, untuk mengecek apakah query sudah menggunakan index atau belum

#### Text Indexes
text index digunakan untuk mendukung pencarian data dengan tipe data string, tidak hanya pada field bertipe data string, text index juga bisa digunakan pada array yang menyimpan value dengan tipe data string
name_text_price_text_category_text
```javascript

// parameter weights digunakan untuk melakukan scoring, jadi masing2 field yang terlebih dahulu di hit akan memberikan score yang telah ditentukan, score ini akan menentukan prioritas data yang akan ditampilkan terlebih dahulu
db.products.createIndex({
   name: "text",
   category: "text"
},{
    weights: {
        name: 10,
       category: 5
    }
})

db.products.find({$text:{$search:"gaming"}})

// akan mencari data yang mengandung kata gaming OR technology
db.products.find({$text:{$search:"gaming technology"}})

// akan mencari data yang mengandung kata gaming AND technology
db.products.find({$text:{$search:'"gaming technology"'}})

// akan mencari data yang mengandung kata gaming dan tidak mengandung kata technology
db.products.find({$text:{$search:"gaming -technology"}})

// query diatas akan di sorting otomatis, hasil urutannya berdasarkan berat / bobot score yang diberikan kepada masing - masing field
```

#### Wildcard Indexes
membuat index untuk nested object yang fieldnya tidak pasti

```javascript
[
   {
       name: "string",
      category: [],
      add :{
           warna: []
      }
   },
   {
       name: "string",
      category: [],
      add: {
           variant: []
      }
   }
]
```

kedua object diatas pada field "add" memiliki field yang berbeda yang satu warna dan satu lagi variant
untuk membuat index pada kondisi seperti itu kita gunakan  **Wildcard Indexes**

```javascript
db.products.createIndex({
   "add.$**": 1
})

// contoh query
db.products.find({"add.variant":{$in:["128 gb"]}})
```


#### Index Properties
properties pada index adalah menambahkan kemampuan(behaviour) tertentu terhadapat index yang kita buat
1. TTL Index,digunakan untuk membuat data hilang secara otomatis dalam kurun waktu tertentu, TTL Index hanya dapat digunakan pada field dengan tipe data date, TTL Index ini berjalan secara otomatis setiap 60 detik sekali
2. Unique Index, field tidak boleh meyimpan nilai yang duplikat

```javascript
// ini akan hilang dalam waktu 10 detik, tapi background job mongodb berjalan setiap 60 detik
db.sessions.createIndex({createdAt:1},{expireAfterSeconds:10})

// unique, cara kerjanya sama dengan index pada relational database
db.users.createIndex({email:1},{unique: true})
```

### Security
secara default mongodb berjalan pada mode yang tidak aman (tidak butuh username & password), untuk mencegah hal ini kita bisa mengaktifkan **access controll** pada mongodb.
terdapat 2 role pada mongodb, yaitu userAdminAnyDatabase dan readWriteAnyDatabase

```javascript
use admin
db.createUser({
   user: "root", // bebas
   pwd: "root", // bebas
   roles: ["userAdminAnyDatabase", "readWriteAnyDatabase"]
})
```

### Authentication

1. db.createUser, membuat user
2. db.getUsers, mendapatkan semua user
3. db.dropUser, menghapus user
4. db.updateUser, mengupdate user
5. db.changeUserPassword, mengubah user password

```javascript
// membuat user di database lain
use test//database bebas

db.createUser({user:"contoh",pwd:"contoh",roles:[
      {
          role: "readWrite" // https://docs.mongodb.com/manual/reference/built-in-roles/
         db: "test" // sesuaikan nama database
      }
]})

// update user
db.updateUser("fariz",{roles:[{role:"read",db:"test"}]})

// delete user
db.dropUser("fariz")

// change user password
db.changeUserPassword("user","passwordBaru")
```

cara masuk menggunakan akun user per database 
mongo --username contoh --password contoh --authenticationDatabase contoh

### Authorization

authorization adalah proses setelah authentication, authorization bergfungsi untuk mengecek apakah sebuah akun memiliki akses untuk melakukan aksi tertentu
authorization disimpan dalam bentuk role

#### Database Role (per Database)
1. read, bisa membaca disemua collection yang bukan system collection
2. readWrite, bisa baca dan tulis disemua collectio yang bukan system collection
3. dbAdmin, bisa melakukan kemampuan administrasi database
4. userAdmin, mampu membuat user dan role
5. dbOwner, kombinasi readWrite, dbAdmin, dan userAdmin

#### All Database Roles
1. readAnyDatabase, bisa membaca semua collection disemua database
2. readWriteAnyDatabase, bisa tulis dan baca disemua database
3. userAdminAnyDatabase, bisa membuat user dan role untuk semua database
