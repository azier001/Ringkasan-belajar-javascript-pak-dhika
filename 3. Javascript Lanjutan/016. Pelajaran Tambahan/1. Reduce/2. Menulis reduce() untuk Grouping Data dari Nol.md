# 🎯 Tutorial: Menulis `reduce()` untuk Grouping Data dari Nol

> **Objektif**: Memahami MENGAPA dan BAGAIMANA `Array.reduce()` bekerja untuk mengelompokkan data berdasarkan kategori.

---

## 📋 Konsep yang Akan Dipelajari

| No | Konsep | Deskripsi |
|----|--------|-----------|
| 1 | Accumulator Pattern | Pola mengumpulkan hasil secara bertahap |
| 2 | Object sebagai Container | Menggunakan object untuk grouping dinamis |
| 3 | Dynamic Key Creation | Membuat property object secara dinamis |
| 4 | Conditional Initialization | Inisialisasi array hanya saat dibutuhkan |

---

## 🧩 Langkah 0: Memahami Problem & Data

### 📝 MENGAPA kita perlu grouping?

Bayangkan kamu punya **tumpukan nota belanja** dan ingin memisahkannya ke dalam **map-map berbeda** berdasarkan kategori. Itulah esensi grouping!

```javascript
// Data awal: array of objects (nota belanja campur aduk)
const orders = [
  { product: 'Laptop', category: 'electronics', price: 15000000 },
  { product: 'Mouse', category: 'electronics', price: 200000 },
  { product: 'Meja', category: 'furniture', price: 1500000 }
];

// Target: object dengan category sebagai key
// {
//   electronics: [...items...],
//   furniture: [...items...]
// }
```

### 🎨 Visualisasi Transformasi

```
SEBELUM (Array)                    SESUDAH (Object)
┌─────────────────┐               ┌─────────────────────────┐
│ Laptop  │ elec  │               │ electronics:            │
├─────────────────┤      ═══►     │   ├─ Laptop             │
│ Mouse   │ elec  │               │   └─ Mouse              │
├─────────────────┤               │ furniture:              │
│ Meja    │ furn  │               │   └─ Meja               │
└─────────────────┘               └─────────────────────────┘
```

---

## 🔄 Langkah 1: Siapkan Kerangka `reduce()`

### 📝 MENGAPA mulai dari kerangka kosong?

Seperti membangun rumah, kita perlu **fondasi** dulu. `reduce()` butuh 2 komponen wajib:
1. **Callback function** - logika per item
2. **Initial value** - nilai awal accumulator

### 💻 Kode

```javascript
const orders = [
  { product: 'Laptop', category: 'electronics', price: 15000000 },
  { product: 'Mouse', category: 'electronics', price: 200000 },
  { product: 'Meja', category: 'furniture', price: 1500000 }
];

// Kerangka dasar reduce
const grouped = orders.reduce((acc, order) => {
  // logika akan ditambah di langkah berikutnya
  return acc;  // WAJIB return accumulator!
}, {});        // {} = initial value (object kosong)
```

### 🧪 Test

```javascript
console.log(grouped);
console.log(typeof grouped);
```

### ✅ Expected Output

```
{}
object
```

### 🔧 Debugging Tips

| Problem | Penyebab | Solusi |
|---------|----------|--------|
| `undefined` | Lupa `return acc` | Selalu return accumulator |
| Error `reduce is not a function` | `orders` bukan array | Cek `Array.isArray(orders)` |

### ⚠️ Kesalahan Umum

```javascript
// ❌ SALAH: Lupa initial value
const grouped = orders.reduce((acc, order) => {
  return acc;
}); // Error! acc pertama = orders[0] (object, bukan {})

// ✅ BENAR: Selalu beri initial value
const grouped = orders.reduce((acc, order) => {
  return acc;
}, {}); // acc dimulai dari {}
```

---

## 🔀 Langkah 2: Pilih Pendekatan (Comparison)

### 📝 MENGAPA `reduce()` dan bukan yang lain?

| Approach | Pro | Kon | Use Case |
|----------|-----|-----|----------|
| **`reduce()`** ✅ | Single pass, flexible, functional | Learning curve | Grouping, aggregasi kompleks |
| `forEach()` + object | Mudah dipahami | Butuh variable external | Pemula, kode sederhana |
| `Object.groupBy()` | Super simple | Browser support terbatas (2024+) | Modern browsers only |
| Multiple `filter()` | Readable | Multiple passes (lambat) | Kategori sudah diketahui |

### 💻 Perbandingan Kode

```javascript
// Approach 1: reduce() - KITA PILIH INI
const grouped = orders.reduce((acc, order) => {
  // ... logika grouping
  return acc;
}, {});

// Approach 2: forEach (butuh variable luar)
const grouped2 = {};
orders.forEach(order => {
  // ... logika grouping
});

// Approach 3: Object.groupBy() - Modern tapi belum universal
const grouped3 = Object.groupBy(orders, order => order.category);
```

### 🎯 MENGAPA `reduce()` terbaik untuk kasus ini?

1. **Self-contained**: Tidak butuh variable external
2. **Single pass**: O(n) - hanya loop sekali
3. **Immutable-friendly**: Bisa digunakan dalam functional programming
4. **Universal**: Didukung semua browser

---

## 🔑 Langkah 3: Ekstrak Category Key

### 📝 MENGAPA perlu ekstrak key dulu?

Kita perlu tahu **"ke map mana"** setiap item harus dimasukkan. Category adalah **kunci pengelompokan** kita.

### 💻 Kode

```javascript
const orders = [
  { product: 'Laptop', category: 'electronics', price: 15000000 },
  { product: 'Mouse', category: 'electronics', price: 200000 },
  { product: 'Meja', category: 'furniture', price: 1500000 }
];

const grouped = orders.reduce((acc, order) => {
  const category = order.category;  // Ekstrak key
  
  console.log(`Processing: ${order.product} → category: ${category}`);
  
  return acc;
}, {});
```

### 🧪 Test

```javascript
console.log('Final result:', grouped);
```

### ✅ Expected Output

```
Processing: Laptop → category: electronics
Processing: Mouse → category: electronics
Processing: Meja → category: furniture
Final result: {}
```

### 🎨 Visualisasi: Flow per Iterasi

| Iterasi | `order` | `category` | `acc` (belum diisi) |
|---------|---------|------------|---------------------|
| 1 | `{product:'Laptop',...}` | `'electronics'` | `{}` |
| 2 | `{product:'Mouse',...}` | `'electronics'` | `{}` |
| 3 | `{product:'Meja',...}` | `'furniture'` | `{}` |

---

## 🏗️ Langkah 4: Inisialisasi Array per Category

### 📝 MENGAPA perlu cek `if (!acc[category])`?

Analoginya: Saat pertama kali dapat nota "electronics", kamu perlu **buat map baru berlabel "electronics"** dulu. Kalau map sudah ada, langsung masukkan saja.

### 💻 Kode

```javascript
const orders = [
  { product: 'Laptop', category: 'electronics', price: 15000000 },
  { product: 'Mouse', category: 'electronics', price: 200000 },
  { product: 'Meja', category: 'furniture', price: 1500000 }
];

const grouped = orders.reduce((acc, order) => {
  const category = order.category;
  
  // Cek: apakah "map" untuk category ini sudah ada?
  if (!acc[category]) {
    acc[category] = [];  // Belum ada? Buat array kosong!
    console.log(`📁 Created new group: "${category}"`);
  }
  
  return acc;
}, {});
```

### 🧪 Test

```javascript
console.log(grouped);
console.log(Object.keys(grouped));
```

### ✅ Expected Output

```
📁 Created new group: "electronics"
📁 Created new group: "furniture"
{ electronics: [], furniture: [] }
[ 'electronics', 'furniture' ]
```

### 🎨 Visualisasi: State `acc` per Iterasi

| Iterasi | `order.product` | `category` | Aksi | State `acc` |
|---------|-----------------|------------|------|-------------|
| 1 | Laptop | electronics | **CREATE** `electronics: []` | `{electronics: []}` |
| 2 | Mouse | electronics | Skip (sudah ada) | `{electronics: []}` |
| 3 | Meja | furniture | **CREATE** `furniture: []` | `{electronics: [], furniture: []}` |

### ⚠️ Kesalahan Umum

```javascript
// ❌ SALAH: Selalu overwrite (data hilang!)
acc[category] = [];  // Tanpa if-check

// ❌ SALAH: Typo pada pengecekan
if (!acc.category) {  // Ini cek literal "category", bukan nilai variabel!
  acc.category = [];
}

// ✅ BENAR: Gunakan bracket notation untuk dynamic key
if (!acc[category]) {
  acc[category] = [];
}
```

---

## 📥 Langkah 5: Push Item ke Group yang Sesuai

### 📝 MENGAPA `push()` di luar `if`?

Setiap item **PASTI** harus masuk ke grupnya. `if` hanya untuk membuat grup baru, tapi `push` dilakukan untuk **semua item** tanpa terkecuali.

### 💻 Kode (FINAL)

```javascript
const orders = [
  { product: 'Laptop', category: 'electronics', price: 15000000 },
  { product: 'Mouse', category: 'electronics', price: 200000 },
  { product: 'Meja', category: 'furniture', price: 1500000 }
];

const grouped = orders.reduce((acc, order) => {
  const category = order.category;
  
  if (!acc[category]) {
    acc[category] = [];
  }
  
  acc[category].push(order);  // Masukkan item ke grup
  
  return acc;
}, {});
```

### 🧪 Test

```javascript
console.log(grouped);
console.log('Electronics count:', grouped.electronics.length);
console.log('Furniture count:', grouped.furniture.length);
console.log('First electronic:', grouped.electronics[0].product);
```

### ✅ Expected Output

```javascript
{
  electronics: [
    { product: 'Laptop', category: 'electronics', price: 15000000 },
    { product: 'Mouse', category: 'electronics', price: 200000 }
  ],
  furniture: [
    { product: 'Meja', category: 'furniture', price: 1500000 }
  ]
}
Electronics count: 2
Furniture count: 1
First electronic: Laptop
```

### 🎨 Visualisasi: State Lengkap per Iterasi

| Iter | Item | Category | `if` triggered? | Aksi | State `acc` |
|------|------|----------|-----------------|------|-------------|
| 1 | Laptop | electronics | ✅ Ya | Create + Push | `{electronics: [Laptop]}` |
| 2 | Mouse | electronics | ❌ Tidak | Push only | `{electronics: [Laptop, Mouse]}` |
| 3 | Meja | furniture | ✅ Ya | Create + Push | `{electronics: [...], furniture: [Meja]}` |

### ⚠️ Kesalahan Umum

```javascript
// ❌ SALAH: push() di dalam if (item pertama saja yang masuk!)
if (!acc[category]) {
  acc[category] = [];
  acc[category].push(order);  // Hanya item pertama per category!
}

// ❌ SALAH: Lupa return
acc[category].push(order);
// return acc;  ← LUPA!

// ✅ BENAR: push() di luar if, return di akhir
if (!acc[category]) {
  acc[category] = [];
}
acc[category].push(order);
return acc;
```

---

## 🔬 Deep Dive: Memahami `acc[category].push(order)`

### 📦 Analogi: Lemari dengan Kotak-kotak

Untuk memahami kenapa kita menggunakan `acc[category].push(order)`, bayangkan proses seperti ini:

```
acc = LEMARI BESAR
├─ electronics (KOTAK berisi kertas-kertas) ← Array
│  ├─ 📄 {product: 'Laptop', ...}           ← Object
│  └─ 📄 {product: 'Mouse', ...}            ← Object
│
└─ furniture (KOTAK berisi kertas-kertas)   ← Array
   └─ 📄 {product: 'Meja', ...}             ← Object
```

**Konsep Penting:**
- **LEMARI** = `acc` (object besar)
- **KOTAK** = `acc[category]` (array)
- **KERTAS** = `order` (object individual)

### 🔍 Breakdown: 3 Bagian Kode

```javascript
acc[category].push(order);
// │     │       │     │
// │     │       │     └─ Object yang akan dimasukkan
// │     │       └─────── Method array untuk menambah item
// │     └─────────────── Variabel berisi nama category
// └───────────────────── Accumulator (object container)
```

### 🎬 Proses Step-by-Step (Iterasi 1)

#### **Sebelum Push:**

```javascript
// order saat ini:
order = { product: 'Laptop', category: 'electronics', price: 15000000 }

// category yang diekstrak:
category = 'electronics'

// State acc setelah if-check:
acc = {
  electronics: []  // ← Array KOSONG (baru dibuat)
}
```

#### **Saat Push Dijalankan:**

```javascript
// 1. Ambil array dari kotak "electronics"
acc['electronics']  // → []

// 2. Push object ke dalam array
acc['electronics'].push(order)

// 3. Hasil: array sekarang berisi 1 object
acc['electronics']  // → [{ product: 'Laptop', ... }]
```

#### **Sesudah Push:**

```javascript
acc = {
  electronics: [
    { product: 'Laptop', category: 'electronics', price: 15000000 }
  ]
}
```

### 🎯 Kenapa Struktur Ini?

#### **Q: Kenapa `acc[category]` harus array?**

**A:** Karena 1 kategori bisa memiliki BANYAK item!

```javascript
// ❌ SALAH: Pakai object (hanya bisa simpan 1 item)
acc['electronics'] = order;  // Item sebelumnya hilang!

// ✅ BENAR: Pakai array (bisa simpan banyak item)
acc['electronics'] = [];
acc['electronics'].push(order1);  // [order1]
acc['electronics'].push(order2);  // [order1, order2]
acc['electronics'].push(order3);  // [order1, order2, order3]
```

#### **Q: Kenapa `acc[category]` dan bukan `acc.category`?**

**A:** Karena `category` adalah **VARIABEL** dengan nilai dinamis!

```javascript
const category = 'electronics';  // Nilai berubah-ubah per iterasi

// ❌ SALAH: Mencari property literal bernama "category"
acc.category        // undefined (tidak ada property "category")

// ✅ BENAR: Menggunakan NILAI dari variabel
acc[category]       // acc['electronics'] ✓
acc['electronics']  // Sama artinya!
```

### 🧪 Eksperimen: Lihat Tipe Data

```javascript
const orders = [
  { product: 'Laptop', category: 'electronics', price: 15000000 }
];

const grouped = orders.reduce((acc, order) => {
  const category = order.category;
  
  if (!acc[category]) {
    acc[category] = [];
    
    // CEK TIPE DATA
    console.log(`✅ Tipe acc['${category}']:`, Array.isArray(acc[category]));
    // Output: ✅ Tipe acc['electronics']: true (INI ARRAY!)
  }
  
  acc[category].push(order);
  
  // CEK ISI ARRAY
  console.log(`📦 Isi acc['${category}'][0]:`, acc[category][0]);
  // Output: 📦 Isi acc['electronics'][0]: { product: 'Laptop', ... }
  
  // CEK TIPE ISI ARRAY
  console.log(`🔖 Tipe isi array:`, typeof acc[category][0]);
  // Output: 🔖 Tipe isi array: object (OBJECT DI DALAM ARRAY!)
  
  return acc;
}, {});

console.log('\n📊 Struktur Lengkap:');
console.log(JSON.stringify(grouped, null, 2));
```

### 📊 Perbandingan: Object vs Array di Dalam

```javascript
// ❌ STRUKTUR SALAH
acc = {
  electronics: { product: 'Laptop', ... }  // Cuma bisa 1 item!
}

// ✅ STRUKTUR BENAR
acc = {
  electronics: [                            // Array bisa banyak item!
    { product: 'Laptop', ... },
    { product: 'Mouse', ... },
    { product: 'Keyboard', ... }
  ]
}
```

### 🎨 Visualisasi: Transformasi Data

```
┌─────────────────────────────────────────────┐
│  INPUT: order (Object Individual)           │
│  { product: 'Laptop', category: 'elec' }    │
└───────────────┬─────────────────────────────┘
                │
                │ acc[category].push(order)
                ▼
┌─────────────────────────────────────────────┐
│  OUTPUT: acc (Object Berisi Array)          │
│  {                                          │
│    electronics: [                           │
│      { product: 'Laptop', ... }  ← Object   │
│    ]                              ← Array   │
│  }                                          │
└─────────────────────────────────────────────┘
```

### 💡 Kesimpulan Deep Dive

| Bagian Kode | Tipe Data | Fungsi |
|-------------|-----------|--------|
| `order` | **Object** | Data individual yang akan dimasukkan |
| `category` | **String** | Nama key untuk grouping (misal: "electronics") |
| `acc` | **Object** | Container utama untuk semua grup |
| `acc[category]` | **Array** | Container untuk 1 grup tertentu |
| `acc[category][0]` | **Object** | Item pertama di dalam array |

**Rumus:**
```javascript
acc[category].push(order)
//   │          │     │
//   │          │     └── Object individual
//   │          └──────── Method untuk tambah item ke array
//   └─────────────────── Array tempat menyimpan objects
```

---

## 🧪 Langkah 6: Edge Cases Testing

### 💻 Test dengan Berbagai Skenario

```javascript
// Test 1: Array kosong
const emptyOrders = [];
const grouped1 = emptyOrders.reduce((acc, order) => {
  const category = order.category;
  if (!acc[category]) acc[category] = [];
  acc[category].push(order);
  return acc;
}, {});
console.log('Empty array:', grouped1);

// Test 2: Single item
const singleOrder = [{ product: 'TV', category: 'electronics', price: 5000000 }];
const grouped2 = singleOrder.reduce((acc, order) => {
  const category = order.category;
  if (!acc[category]) acc[category] = [];
  acc[category].push(order);
  return acc;
}, {});
console.log('Single item:', grouped2);

// Test 3: Category undefined/null
const messyOrders = [
  { product: 'X', category: undefined },
  { product: 'Y', category: null },
  { product: 'Z' }  // tidak ada category
];
const grouped3 = messyOrders.reduce((acc, order) => {
  const category = order.category || 'uncategorized';  // Fallback!
  if (!acc[category]) acc[category] = [];
  acc[category].push(order);
  return acc;
}, {});
console.log('Messy data:', grouped3);
```

### ✅ Expected Output

```javascript
Empty array: {}
Single item: { electronics: [{ product: 'TV', ... }] }
Messy data: { uncategorized: [{...}, {...}, {...}] }
```

---

## 🚫 When NOT to Use This Approach

| Skenario | Kenapa Tidak | Alternatif |
|----------|--------------|------------|
| **Kategori sudah diketahui & sedikit** | Overkill, kurang readable | Multiple `filter()` |
| **Browser modern only (2024+)** | Ada cara lebih simple | `Object.groupBy()` |
| **Perlu nested grouping kompleks** | `reduce` jadi rumit | Library seperti Lodash `_.groupBy()` |
| **Data sangat besar (100k+ items)** | Perlu optimasi | Stream processing, Web Workers |
| **Tim tidak familiar functional JS** | Maintenance sulit | `forEach` + object external |

---

## 📚 Quick Reference Card

```javascript
// 🎯 TEMPLATE: Grouping dengan reduce()
const grouped = array.reduce((acc, item) => {
  const key = item.propertyName;      // 1. Ekstrak key
  if (!acc[key]) acc[key] = [];       // 2. Init jika belum ada
  acc[key].push(item);                // 3. Push item
  return acc;                         // 4. WAJIB return!
}, {});                               // 5. Initial value = {}
```

### 🧠 Ingat: A-I-P-R

| Letter | Step | Code |
|--------|------|------|
| **A** | Access key | `const key = item.category` |
| **I** | Initialize if needed | `if (!acc[key]) acc[key] = []` |
| **P** | Push item | `acc[key].push(item)` |
| **R** | Return accumulator | `return acc` |

---

## 🚀 Tips Optimasi & Next Steps

### Optimasi

```javascript
// 1. Short-circuit dengan nullish coalescing
const grouped = orders.reduce((acc, order) => {
  const cat = order.category;
  (acc[cat] ??= []).push(order);  // Lebih ringkas!
  return acc;
}, {});

// 2. Grouping + Agregasi sekaligus
const stats = orders.reduce((acc, order) => {
  const cat = order.category;
  if (!acc[cat]) acc[cat] = { items: [], totalPrice: 0 };
  acc[cat].items.push(order);
  acc[cat].totalPrice += order.price;
  return acc;
}, {});
```

### Next Steps untuk Dipelajari

1. **`Object.groupBy()`** - Native grouping (ES2024)
2. **`Map` vs Object** - Kapan pakai Map untuk grouping
3. **Lodash `_.groupBy()`** - Utility library approach
4. **Multiple key grouping** - Group by 2+ properties

---

## ✅ Summary Checklist

- [x] Memahami **WHY** reduce cocok untuk grouping
- [x] Memahami **flow accumulator** dari iterasi ke iterasi
- [x] Memahami **struktur data**: Object berisi Array of Objects
- [x] Memahami **acc[category].push(order)** secara detail
- [x] Bisa menulis dari nol tanpa copy-paste
- [x] Tahu **common mistakes** dan cara menghindarinya
- [x] Paham **kapan TIDAK menggunakan** pendekatan ini
- [x] Siap handle **edge cases** (empty array, missing category)

---

> 💡 **Pro Tip**: Jika bingung, selalu visualisasikan state `acc` di setiap iterasi dengan `console.log(JSON.stringify(acc))` di dalam callback!
