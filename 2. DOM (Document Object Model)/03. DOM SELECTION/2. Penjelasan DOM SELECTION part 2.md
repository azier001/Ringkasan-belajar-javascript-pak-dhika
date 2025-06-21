# Panduan Lengkap DOM Manipulation dengan JavaScript

## 📖 Pengantar

DOM (Document Object Model) adalah representasi struktur HTML dalam bentuk objek yang dapat dimanipulasi menggunakan JavaScript. Manipulasi DOM memungkinkan kita untuk mengubah konten, gaya, dan struktur halaman web secara dinamis.

---

## 🎯 Metode Pemilihan Elemen

### 1. `querySelector()` - Memilih Satu Elemen

`querySelector()` digunakan untuk memilih **elemen pertama** yang cocok dengan selector CSS.

#### Sintaks Dasar:
```javascript
document.querySelector('selector');
```

#### Cara Penggunaan:
- **Class**: Gunakan titik (`.`) sebelum nama class
- **ID**: Gunakan hashtag (`#`) sebelum nama ID
- **Tag**: Langsung tulis nama tag
- **Pseudo-class**: Gunakan titik dua (`:`)

#### Contoh Implementasi:

```html
<!DOCTYPE html>
<html>
<head>
    <title>DOM Manipulation Example</title>
    <style>
        .highlight { background-color: yellow; }
        #special { font-weight: bold; }
    </style>
</head>
<body>
    <section id="a">
        <p class="intro">Paragraf pertama</p>
        <p id="special">Paragraf kedua</p>
    </section>
    
    <section id="b">
        <p>Paragraf ketiga</p>
        <p>Paragraf keempat</p>
        <ul>
            <li>Item 1</li>
            <li>Item 2</li>
            <li>Item 3</li>
        </ul>
    </section>

    <script>
        // Memilih elemen berdasarkan ID
        const paragrafKhusus = document.querySelector('#special');
        paragrafKhusus.style.color = 'blue';
        
        // Memilih elemen dengan nested selector
        const paragrafDiB = document.querySelector('#b p');
        paragrafDiB.style.color = 'green';
        
        // Memilih dengan pseudo-class
        const itemKedua = document.querySelector('li:nth-child(2)');
        itemKedua.style.backgroundColor = 'orange';
    </script>
</body>
</html>
```

**Output yang dihasilkan:**
- Paragraf dengan ID "special" akan berwarna biru
- Paragraf pertama dalam section#b akan berwarna hijau
- Item kedua dalam list akan memiliki background orange

---

### 2. `querySelectorAll()` - Memilih Semua Elemen

`querySelectorAll()` mengembalikan **NodeList** yang berisi semua elemen yang cocok dengan selector.

#### Contoh Penggunaan:

```javascript
// Memilih semua paragraf
const semuaParagraf = document.querySelectorAll('p');
console.log(semuaParagraf); // NodeList [p, p, p, p]

// Mengubah semua paragraf dengan loop
for (let i = 0; i < semuaParagraf.length; i++) {
    semuaParagraf[i].style.fontFamily = 'Arial';
}

// Cara modern dengan forEach
semuaParagraf.forEach(paragraf => {
    paragraf.style.padding = '10px';
});
```

**Output:**
- Semua paragraf akan menggunakan font Arial
- Semua paragraf akan memiliki padding 10px

---

### 3. Metode Pemilihan Klasik (Tercepat)

#### `getElementById()` - Paling Efisien
```javascript
const elemenKhusus = document.getElementById('special');
elemenKhusus.style.textDecoration = 'underline';
```

#### `getElementsByTagName()` - Efisien untuk Tag
```javascript
const semuaList = document.getElementsByTagName('li');
// Mengembalikan HTMLCollection, bukan NodeList
```

---

## 🔄 Mengubah Node Root

Kadang kita perlu membatasi pencarian elemen pada bagian tertentu dari dokumen:

```javascript
// Memilih container terlebih dahulu
const sectionB = document.querySelector('section#b');

// Mencari elemen hanya dalam container tersebut
const paragrafPertamaDiB = sectionB.getElementsByTagName('p')[0];
paragrafPertamaDiB.style.backgroundColor = 'lightblue';

// Alternatif dengan querySelector
const paragrafKeduaDiB = sectionB.querySelector('p:nth-child(2)');
paragrafKeduaDiB.style.fontStyle = 'italic';
```

**Keuntungan:** Pencarian lebih efisien dan tidak akan mempengaruhi elemen di luar container.

---

## 💡 Contoh Kasus Nyata

### Sistem Validasi Form Sederhana

```html
<!DOCTYPE html>
<html>
<head>
    <title>Form Validation</title>
    <style>
        .error { 
            border: 2px solid red; 
            background-color: #ffe6e6; 
        }
        .success { 
            border: 2px solid green; 
            background-color: #e6ffe6; 
        }
        .message {
            padding: 10px;
            margin: 10px 0;
            border-radius: 4px;
        }
    </style>
</head>
<body>
    <form id="loginForm">
        <div>
            <label>Username:</label>
            <input type="text" id="username" placeholder="Masukkan username">
        </div>
        <div>
            <label>Password:</label>
            <input type="password" id="password" placeholder="Masukkan password">
        </div>
        <button type="button" onclick="validateForm()">Login</button>
        <div id="messageContainer"></div>
    </form>

    <script>
        function validateForm() {
            // Mengambil elemen input
            const usernameInput = document.querySelector('#username');
            const passwordInput = document.querySelector('#password');
            const messageContainer = document.querySelector('#messageContainer');
            
            // Reset styling
            const allInputs = document.querySelectorAll('input');
            allInputs.forEach(input => {
                input.classList.remove('error', 'success');
            });
            
            let isValid = true;
            let messages = [];
            
            // Validasi username
            if (usernameInput.value.trim() === '') {
                usernameInput.classList.add('error');
                messages.push('Username tidak boleh kosong');
                isValid = false;
            } else {
                usernameInput.classList.add('success');
            }
            
            // Validasi password
            if (passwordInput.value.length < 6) {
                passwordInput.classList.add('error');
                messages.push('Password minimal 6 karakter');
                isValid = false;
            } else {
                passwordInput.classList.add('success');
            }
            
            // Tampilkan pesan
            if (isValid) {
                messageContainer.innerHTML = '<div class="message success">Login berhasil!</div>';
            } else {
                messageContainer.innerHTML = `<div class="message error">${messages.join('<br>')}</div>`;
            }
        }
    </script>
</body>
</html>
```

---

## ⚠️ Kesalahan Umum dan Cara Menghindarinya

### 1. **Mengakses Elemen Sebelum DOM Selesai Dimuat**

❌ **Salah:**
```javascript
// Script di <head> tanpa event listener
const tombol = document.querySelector('#tombol');
tombol.style.color = 'red'; // Error: Cannot read property 'style' of null
```

✅ **Benar:**
```javascript
// Tunggu sampai DOM selesai dimuat
document.addEventListener('DOMContentLoaded', function() {
    const tombol = document.querySelector('#tombol');
    if (tombol) {
        tombol.style.color = 'red';
    }
});
```

### 2. **Tidak Memeriksa Apakah Elemen Existe**

❌ **Salah:**
```javascript
const elemen = document.querySelector('#tidakAda');
elemen.style.color = 'red'; // Error: Cannot read property 'style' of null
```

✅ **Benar:**
```javascript
const elemen = document.querySelector('#tidakAda');
if (elemen) {
    elemen.style.color = 'red';
} else {
    console.log('Elemen tidak ditemukan');
}
```

### 3. **Salah Menggunakan getElementsByTagName vs querySelectorAll**

❌ **Salah:**
```javascript
const elements = document.getElementsByTagName('p');
elements.forEach(el => console.log(el)); // Error: forEach is not a function
```

✅ **Benar:**
```javascript
// Dengan getElementsByTagName (HTMLCollection)
const elements = document.getElementsByTagName('p');
Array.from(elements).forEach(el => console.log(el));

// Atau gunakan querySelectorAll (NodeList)
const elements2 = document.querySelectorAll('p');
elements2.forEach(el => console.log(el)); // Langsung bisa forEach
```

---

## 📊 Perbandingan Performa

| Metode | Kecepatan | Fleksibilitas | Penggunaan |
|--------|-----------|---------------|------------|
| `getElementById()` | ⭐⭐⭐⭐⭐ | ⭐⭐ | ID saja |
| `getElementsByTagName()` | ⭐⭐⭐⭐ | ⭐⭐ | Tag saja |
| `querySelector()` | ⭐⭐⭐ | ⭐⭐⭐⭐⭐ | Semua selector CSS |
| `querySelectorAll()` | ⭐⭐ | ⭐⭐⭐⭐⭐ | Semua selector CSS |

---

## 🎓 Tips untuk Pemula

1. **Mulai dengan yang sederhana**: Pahami `getElementById()` dan `querySelector()` terlebih dahulu
2. **Selalu periksa elemen**: Gunakan `if (element)` sebelum memanipulasi
3. **Gunakan console.log()**: Untuk debug dan melihat hasil seleksi
4. **Praktikkan dengan project kecil**: Buat calculator, todo list, atau form sederhana
5. **Pelajari CSS selector**: Karena `querySelector()` menggunakan sintaks yang sama

---

## 🚀 Latihan Mandiri

Coba buat mini project berikut untuk mempraktikkan konsep di atas:

1. **Theme Switcher**: Tombol untuk mengubah tema terang/gelap
2. **Interactive Gallery**: Galeri gambar dengan navigasi
3. **Dynamic Table**: Tabel yang bisa menambah/hapus baris
4. **Modal Dialog**: Pop-up window dengan overlay

---

## 📚 Kesimpulan

DOM manipulation adalah fondasi penting dalam JavaScript untuk membuat website interaktif. Dengan memahami berbagai metode pemilihan elemen dan cara menghindari kesalahan umum, Anda dapat membuat aplikasi web yang dinamis dan user-friendly.

**Ingat:** Latihan konsisten adalah kunci untuk menguasai DOM manipulation. Mulai dari yang sederhana, kemudian tingkatkan kompleksitas project Anda secara bertahap.
