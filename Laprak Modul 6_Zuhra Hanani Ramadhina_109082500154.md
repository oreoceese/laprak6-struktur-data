# <h1 align="center">Laporan Praktikum Modul 6 - DOUBLY LINKED LIST (BAGIAN PERTAMA)</h1>
<p align="center">Zuhra Hanani Ramadhina - 109082500143</p>

## Dasar Teori

Doubly linked list atau senarai berantai ganda adalah struktur data dinamis yang terdiri dari node-node yang saling terhubung secara dua arah[1]. Setiap node memiliki dua pointer, yaitu `next` yang menunjuk ke node berikutnya dan `prev` yang menunjuk ke node sebelumnya, sehingga list dapat ditelusuri dari depan maupun dari belakang[2]. Struktur `List` menyimpan dua penunjuk, yaitu `First` untuk node pertama dan `Last` untuk node terakhir. Node pertama memiliki `prev` bernilai `Nil`, dan node terakhir memiliki `next` bernilai `Nil`.

### A. ADT Doubly Linked List<br/>
ADT doubly linked list memisahkan deklarasi tipe dan prosedur di file header (`.h`) dari implementasinya di file `.cpp`. Pada praktikum ini, data yang disimpan dalam tiap node berupa struct `buku` yang berisi kode buku, judul, dan tahun terbit.

#### 1. Tipe data — `infotype`, `address`, `ElmList`, dan `List` dengan pointer `First` dan `Last`
#### 2. Alokasi dan dealokasi — `alokasi()` membuat node dengan `new`, `dealokasi()` menghapusnya dengan `delete`
#### 3. Insert dan print — `insertLast()` menambah node di akhir list, `printInfo()` menampilkan isi list

### B. Pencarian dan Penghapusan Elemen<br/>
Pencarian dilakukan dengan menelusuri list dari node pertama sambil membandingkan data tiap node. Penghapusan pada doubly linked list harus memperbarui pointer `next` dan `prev` dari node di sekitarnya, serta pointer `First` dan `Last` jika node yang dihapus berada di ujung list.

#### 1. Pencarian elemen — `findElm()` mengembalikan alamat node yang cocok, atau `Nil` jika tidak ditemukan
#### 2. Penghapusan di ujung list — `deleteFirst()` dan `deleteLast()` memperbarui `First` dan `Last`
#### 3. Penghapusan di tengah list — `deleteAfter()` menghapus node setelah node tertentu dan menyambung ulang `next` dan `prev`

## Guided 

### 1. Doublylist.h

```C++
#ifndef DOUBLYLIST_H
#define DOUBLYLIST_H

#include <iostream>
#include <string>

#define Nil NULL

// Latihan 1
struct infotype
{
    std::string nopol;
    std::string warna;
    int thnBuat;
};
typedef struct elmlist *address;
struct elmlist
{
    infotype info;
    address next;
    address prev;
};
struct List
{
    address First;
    address Last;
};

// Latihan 1
void CreateList(List &L);
address alokasi(infotype x);
void dealokasi(address &P);
void printInfo(List L);
void insertLast(List &L, address P);

// Latihan 2

address findElm(List L, std::string nopol);

// Latihan 3
void deleteFirst(List &L, address &P);
void deleteLast(List &L, address &P);
void deleteAfter(List &L, address Prec, address &P);
void deleteElm(List &L, std::string nopol, bool &status);

#endif
```
File header ini mendefinisikan ADT doubly linked list untuk data kendaraan. Struct `infotype` menyimpan nomor polisi (`nopol`), warna, dan tahun pembuatan (`thnBuat`). Struct `elmlist` adalah node yang berisi data (`info`) serta dua pointer, yaitu `next` ke node berikutnya dan `prev` ke node sebelumnya, sedangkan `address` adalah alias pointer ke `elmlist`. Struct `List` menyimpan pointer `First` dan `Last` sebagai penunjuk node pertama dan terakhir. File ini juga mendeklarasikan prototipe seluruh prosedur dan fungsi yang dikelompokkan per latihan (pembuatan dan insert, pencarian, serta penghapusan), dan `#define Nil NULL` dipakai sebagai penanda ujung list.

### 2. Doublylist.cpp

```C++
#include "Doublylist.h"

// Latihan 1
void CreateList(List &L) {
    L.First = Nil;
    L.Last = Nil;
}

address alokasi(infotype x) {
    address P = new elmlist;
    P->info = x;
    P->next = Nil;
    P->prev = Nil; 
    return P;
}

void dealokasi(address &P) {
    delete P;
}

void printInfo(List L) {
    address P = L.Last;
    if (P == Nil) {
        std::cout << "List kosong." << std::endl;
        return;
    }
    while (P != Nil) {
        std::cout << "no polisi : " << P->info.nopol << std::endl;   
        std::cout << "warna     : " << P->info.warna << std::endl;   
        std::cout << "tahun     : " << P->info.thnBuat << std::endl; 
        P = P->prev;
    }
}

void insertLast(List &L, address P) {
    if (L.First == Nil) { 
        L.First = P;
        L.Last = P;
    } else {
        P->prev = L.Last;         
        (L.Last)->next = P;     
        L.Last = P;               
    }
}

// Latihan 2
address findElm(List L, std::string nopol) {
    address P = L.First;
    while (P != Nil) {
        if (P->info.nopol == nopol) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}

// Latihan 3
void deleteFirst(List &L, address &P) {
    if (L.First == Nil) return;

    P = L.First; 
    if (L.First == L.Last) { 
        L.First = Nil;
        L.Last = Nil;
    } else {
        L.First = P->next;     
        (L.First)->prev = Nil; 
        P->next = Nil;         
    }
}

void deleteLast(List &L, address &P) {
    if (L.First == Nil) return;

    P = L.Last; 
    if (L.First == L.Last) { 
        L.First = Nil;
        L.Last = Nil;
    } else {
        L.Last = P->prev;      
        (L.Last)->next = Nil; 
        P->prev = Nil;         
    }
}

void deleteAfter(List &L, address Prec, address &P) {
    if (Prec == Nil || Prec->next == Nil) return;

    P = Prec->next;
    if (P == L.Last) {
        deleteLast(L, P);
    } else {
        Prec->next = P->next;     
        (P->next)->prev = Prec; 
        P->next = Nil;            
        P->prev = Nil;           
    }
}

void deleteElm(List &L, std::string nopol, bool &status) {
    address P = findElm(L, nopol);
    status = false;

    if (P != Nil) {
        if (P == L.First) {
            deleteFirst(L, P);
        } else if (P == L.Last) {
            deleteLast(L, P);
        } else {
            address Prec = P->prev;
            deleteAfter(L, Prec, P);
        }
        dealokasi(P); 
        status = true;
    }
}
```
File ini berisi implementasi seluruh operasi pada list. `CreateList()` membuat list kosong dengan mengisi `First` dan `Last` dengan `Nil`, `alokasi()` membuat node baru dengan `new` dan mengisi kedua pointernya dengan `Nil`, dan `dealokasi()` menghapus node dengan `delete`. Prosedur `insertLast()` menambahkan node di akhir list, yaitu mengisi `prev` node baru dengan `Last` lama, mengisi `next` dari `Last` lama dengan node baru, lalu memindahkan `Last` ke node baru. Prosedur `printInfo()` menelusuri list dari `Last` mundur lewat pointer `prev`, sehingga data tampil dari yang terakhir dimasukkan. Fungsi `findElm()` menelusuri list dari `First` dan mengembalikan alamat node yang nomor polisinya cocok, atau `Nil` jika tidak ditemukan. Untuk penghapusan, `deleteFirst()` dan `deleteLast()` melepas node di ujung list sambil memperbarui `First` dan `Last` (dengan penanganan khusus jika list hanya berisi satu node), sedangkan `deleteAfter()` melepas node setelah node tertentu dan menyambung ulang pointer `next` dan `prev` di sekitarnya. Prosedur `deleteElm()` menggabungkan semuanya, yaitu mencari node dengan `findElm()`, memilih prosedur hapus yang sesuai dengan posisinya (awal, akhir, atau tengah), mendealokasi node, lalu mengisi `status` dengan `true` jika berhasil.

### 3. main.cpp

```C++
#include <iostream>
#include "Doublylist.h"
using namespace std;

void inputData(List &L) {
    infotype data;
    address P;

    cout << "masukkan nomor polisi: "; cin >> data.nopol;
    cout << "masukkan warna kendaraan: "; cin >> data.warna;
    cout << "masukkan tahun kendaraan: "; cin >> data.thnBuat;
    if (findElm(L, data.nopol) == Nil) {
        P = alokasi(data); 
        insertLast(L, P);  
    } else {
        cout << "nomor polisi sudah terdaftar" << endl; 
    }
}

int main() {
    List L;
    CreateList(L); 
    address P;

    // Latihan 1
    inputData(L); 
    inputData(L); 
    inputData(L); 
    inputData(L); 

    cout << "\nDATA LIST 1" << endl; 
    printInfo(L); 

    // Latihan 2
    string nopolCari;
    cout << "\nMasukkan Nomor Polisi yang dicari : "; 
    cin >> nopolCari; 
    
    P = findElm(L, nopolCari); 
    if (P != Nil) {
        cout << "Nomor Polisi : " << P->info.nopol << endl; 
        cout << "Warna        : " << P->info.warna << endl; 
        cout << "Tahun        : " << P->info.thnBuat << endl; 
    } else {
        cout << "Data dengan nomor polisi " << nopolCari << " tidak ditemukan." << endl;
    }

    // Latihan 3
    string nopolHapus;
    bool hapusBerhasil;
    cout << "\nMasukkan Nomor Polisi yang akan dihapus : ";
    cin >> nopolHapus;

    deleteElm(L, nopolHapus, hapusBerhasil); 

    if (hapusBerhasil) {
        cout << "Data dengan nomor polisi " << nopolHapus << " berhasil dihapus." << endl; 
    } else {
        cout << "Data dengan nomor polisi " << nopolHapus << " tidak ditemukan." << endl;
    }
    cout << "\nDATA LIST 1" << endl; 
    printInfo(L); 

    return 0;
}
```
Program utama membuat list kosong, lalu memanggil fungsi `inputData()` sebanyak empat kali. Fungsi tersebut meminta input nomor polisi, warna, dan tahun kendaraan, kemudian mengecek dengan `findElm()` apakah nomor polisi sudah terdaftar. Jika belum, node dialokasikan dan dimasukkan dengan `insertLast()`, jika sudah, program menampilkan pesan "nomor polisi sudah terdaftar" dan data tidak dimasukkan. Setelah itu isi list ditampilkan dengan `printInfo()`. Pada latihan 2, program meminta nomor polisi yang dicari lalu memanggil `findElm()` dan menampilkan data kendaraan jika ketemu. Pada latihan 3, program meminta nomor polisi yang akan dihapus dan memanggil `deleteElm()`, lalu menampilkan pesan berhasil atau tidak berdasarkan nilai `status`, dan terakhir menampilkan sisa isi list.

## Unguided 

### 1.  Pembuatan ADT Dasar dan Insert Data 
#### Source Code Buku.h
```C++
#ifndef BUKU_H
#define BUKU_H
#define Nil NULL

#include <iostream>
#include <string>
using namespace std;

struct buku {
    string kodeBuku;
    string judul;
    int thnTerbit;
};

typedef buku infotype;
typedef struct ElmList *address;

struct ElmList {
    infotype info;
    address next;
    address prev;
};

struct List {
    address First;
    address Last;
};

void CreateList(List &L);
address alokasi(infotype x);
void dealokasi(address &P);
void printInfo(List L);
void insertLast(List &L, address P);

address findElm(List L, infotype x);

void deleteFirst(List &L, address &P);
void deleteLast(List &L, address &P);
void deleteAfter(address Prec, address &P);
void printDaftar(List L);

#endif
```
File header ini mendefinisikan ADT doubly linked list. Struct buku menyimpan kodeBuku, judul, dan thnTerbit, lalu infotype dijadikan alias dari struct tersebut. address adalah pointer ke struct ElmList, yang berisi data (info) serta dua pointer, yaitu next dan prev. Struct List menyimpan pointer First dan Last sebagai penunjuk node pertama dan terakhir. File ini juga mendeklarasikan prototipe semua prosedur dan fungsi yang dipakai pada ketiga soal, dan #define Nil NULL dipakai sebagai penanda ujung list.

#### Source Code Buku.cpp
```C++
#include "Buku.h"

void CreateList(List &L) {
    L.First = Nil;
    L.Last = Nil;
}

address alokasi(infotype x) {
    address P = new ElmList;
    P->info = x;
    P->next = Nil;
    P->prev = Nil;
    return P;
}

void dealokasi(address &P) {
    delete P;
    P = Nil;
}

// ditampilkan dari belakang (Last) ke depan, sesuai contoh output soal
void printInfo(List L) {
    address P = L.Last;
    while (P != Nil) {
        cout << "kode buku   : " << P->info.kodeBuku << endl;
        cout << "judul       : " << P->info.judul << endl;
        cout << "tahun       : " << P->info.thnTerbit << endl;
        P = P->prev;
    }
}

void printDaftar(List L) {
    address P = L.Last;
    while (P != Nil) {
        cout << "Kode Buku    : " << P->info.kodeBuku << endl;
        cout << "Judul        : " << P->info.judul << endl;
        cout << "Tahun Terbit : " << P->info.thnTerbit << endl;
        P = P->prev;
    }
}

void insertLast(List &L, address P) {
    if (L.First == Nil) {
        L.First = P;
        L.Last = P;
    } else {
        P->prev = L.Last;
        L.Last->next = P;
        L.Last = P;
    }
}

// mencari node berdasarkan kodeBuku
address findElm(List L, infotype x) {
    address P = L.First;
    while (P != Nil) {
        if (P->info.kodeBuku == x.kodeBuku) {
            return P;
        }
        P = P->next;
    }
    return Nil;
}

void deleteFirst(List &L, address &P) {
    if (L.First == Nil) {
        P = Nil;
    } else {
        P = L.First;
        if (L.First == L.Last) {
            L.First = Nil;
            L.Last = Nil;
        } else {
            L.First = P->next;
            L.First->prev = Nil;
        }
        P->next = Nil;
    }
}

void deleteLast(List &L, address &P) {
    if (L.Last == Nil) {
        P = Nil;
    } else {
        P = L.Last;
        if (L.First == L.Last) {
            L.First = Nil;
            L.Last = Nil;
        } else {
            L.Last = P->prev;
            L.Last->next = Nil;
        }
        P->prev = Nil;
    }
}

void deleteAfter(address Prec, address &P) {
    if (Prec != Nil && Prec->next != Nil) {
        P = Prec->next;
        Prec->next = P->next;
        if (P->next != Nil) {
            P->next->prev = Prec;
        }
        P->next = Nil;
        P->prev = Nil;
    } else {
        P = Nil;
    }
}
```
File ini mengimplementasikan operasi pada list. CreateList() membuat list kosong dengan mengisi First dan Last dengan Nil. alokasi() membuat node baru dengan new dan mengisi kedua pointernya dengan Nil, sedangkan dealokasi() menghapus node dengan delete. insertLast() menyambungkan node baru ke akhir list, yaitu mengisi prev node baru dengan Last lama, mengisi next dari Last lama dengan node baru, lalu memindahkan Last. printInfo() menelusuri list dari Last mundur lewat pointer prev, sehingga data tampil dari yang terakhir dimasukkan, sesuai contoh output soal. File ini juga memuat findElm(), deleteFirst(), deleteLast(), deleteAfter(), dan printDaftar() yang dipakai pada soal 2 dan 3.

#### Source Code main.cpp
```C++
#include "Buku.h"

int main() {
    List L;
    CreateList(L);

    for (int i = 0; i < 4; i++) {
        infotype x;
        cout << "masukkan kode buku: ";
        cin >> x.kodeBuku;
        cout << "masukkan judul buku: ";
        cin >> x.judul;
        cout << "masukkan tahun terbit: ";
        cin >> x.thnTerbit;

        if (findElm(L, x) != Nil) {
            cout << "kode buku sudah terdaftar" << endl;
        } else {
            insertLast(L, alokasi(x));
        }
        cout << endl;
    }

    cout << "DATA LIST 1" << endl << endl;
    printInfo(L);

    return 0;
}
```
Program membuat list kosong, lalu meminta input kode buku, judul, dan tahun terbit sebanyak empat kali. Sebelum data dimasukkan, program memanggil findElm() untuk mengecek apakah kodeBuku sudah ada di list. Jika sudah ada, program menampilkan pesan “kode buku sudah terdaftar” dan data tidak dimasukkan, sedangkan jika belum, node dialokasikan dan dimasukkan dengan insertLast(). Karena B001 dimasukkan dua kali, data kedua ditolak, sehingga list akhir berisi B001, B003, dan B004 yang ditampilkan dari belakang.

### Output Unguided 1 :

##### Output 
![Screenshot Output Unguided 1](https://github.com/oreoceese/laprak6-struktur-data/blob/6713983ae028af319d777a5240c8a598d82d85b6/6-soal1.png)


### 2. Pencarian Elemen (Searching) 

#### Source Code main2.cpp
```C++
#include "Buku.h"

int main() {
    List L;
    CreateList(L);

    infotype b1 = {"B001", "Algoritma", 2020};
    infotype b2 = {"B003", "Struktur_Data", 2018};
    infotype b3 = {"B004", "Jaringan_Komputer", 2022};
    insertLast(L, alokasi(b1));
    insertLast(L, alokasi(b2));
    insertLast(L, alokasi(b3));

    infotype cari;
    cout << "Masukkan Kode Buku yang dicari : ";
    cin >> cari.kodeBuku;
    cout << endl;

    address P = findElm(L, cari);
    if (P != Nil) {
        cout << "Kode Buku    : " << P->info.kodeBuku << endl;
        cout << "Judul        : " << P->info.judul << endl;
        cout << "Tahun Terbit : " << P->info.thnTerbit << endl;
    } else {
        cout << "Buku dengan kode " << cari.kodeBuku << " tidak ditemukan." << endl;
    }

    return 0;
}
```
Program membentuk list berisi tiga buku (B001, B003, B004), lalu meminta pengguna memasukkan kode buku yang dicari. Fungsi findElm() menelusuri list dari First dan membandingkan kodeBuku tiap node dengan kode yang dicari. Jika ketemu, fungsi mengembalikan alamat node tersebut dan program menampilkan kode buku, judul, dan tahun terbitnya. Jika tidak ketemu, findElm() mengembalikan Nil dan program menampilkan pesan bahwa buku tidak ditemukan.

### Output Unguided 2 :

#### Output
![Screenshot Output Unguided 2](https://github.com/oreoceese/laprak6-struktur-data/blob/6713983ae028af319d777a5240c8a598d82d85b6/6-soal2.png)


### 3. Penghapusan Elemen (Delete) 

#### Source Code main3.cpp
```C++
#include "Buku.h"

int main() {
    List L;
    CreateList(L);

    infotype b1 = {"B001", "Algoritma", 2020};
    infotype b2 = {"B003", "Struktur_Data", 2018};
    infotype b3 = {"B004", "Jaringan_Komputer", 2022};
    insertLast(L, alokasi(b1));
    insertLast(L, alokasi(b2));
    insertLast(L, alokasi(b3));

    infotype hapus;
    cout << "Masukkan Kode Buku yang akan dihapus : ";
    cin >> hapus.kodeBuku;

    address target = findElm(L, hapus);
    if (target == Nil) {
        cout << "Buku dengan kode " << hapus.kodeBuku << " tidak ditemukan." << endl;
    } else {
        address P;
        if (target == L.First) {
            deleteFirst(L, P);
        } else if (target == L.Last) {
            deleteLast(L, P);
        } else {
            deleteAfter(target->prev, P);
        }
        dealokasi(P);
        cout << "Data dengan kode buku " << hapus.kodeBuku << " berhasil dihapus." << endl;
    }

    cout << endl << "DATA LIST 1" << endl << endl;
    printDaftar(L);

    return 0;
}
```
Program membentuk list yang sama seperti soal 2, lalu meminta kode buku yang akan dihapus. Node target dicari dengan findElm(), kemudian program memilih prosedur penghapusan berdasarkan posisinya. Jika target adalah First, dipakai deleteFirst(), jika Last, dipakai deleteLast(), dan jika berada di tengah, dipakai deleteAfter() dengan node sebelumnya (target->prev). Setelah node dilepas dari list, memorinya dikembalikan dengan dealokasi(), lalu sisa list ditampilkan. Untuk kode B003, node di tengah terhapus dan yang tersisa adalah B004 dan B001 yang ditampilkan dari belakang.

### Output Unguided 3 :

#### Output
![Screenshot Output Unguided 3](https://github.com/oreoceese/laprak6-struktur-data/blob/6713983ae028af319d777a5240c8a598d82d85b6/6-soal3.png)


## Kesimpulan
Berdasarkan praktikum yang telah dilakukan, dapat disimpulkan bahwa doubly linked list merupakan struktur data dinamis yang setiap node-nya memiliki dua pointer, yaitu `next` dan `prev`, sehingga list dapat ditelusuri dari dua arah. Dengan adanya pointer `First` dan `Last`, penambahan node di akhir list dapat dilakukan langsung tanpa menelusuri seluruh list dari awal. Operasi penghapusan pada doubly linked list harus memperbarui pointer `next` dan `prev` dari node di sekitarnya agar rantai node tetap utuh. Pengecekan duplikasi menggunakan `findElm()` sebelum insert juga membantu menjaga agar data pada list tidak ganda. Melalui latihan unguided, mahasiswa dilatih membangun ADT doubly linked list serta menerapkan operasi insert, pencarian, dan penghapusan node.

## Referensi
