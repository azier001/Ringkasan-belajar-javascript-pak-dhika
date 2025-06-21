# 🎨 Bermain dengan Warna Part 2

> 📚 **Materi:** Membuat slider RGB dan efek mouse movement untuk mengubah warna background  
> 🎯 **Level:** Pemula  
> ⏱️ **Durasi Belajar:** ~30 menit

---

## 🎲 Random Color Generator

Membuat tombol yang menghasilkan warna acak saat diklik.

### 📝 HTML

```html
<button id="randomBtn">🎲 Random Color</button>
<div id="colorInfo">
  <p>RGB: <span id="rgbValue">rgb(255, 255, 255)</span></p>
  <p>HEX: <span id="hexValue">#ffffff</span></p>
</div>
```

### ⚡ JavaScript - Versi Klasik

```javascript
const randomBtn = document.getElementById('randomBtn');
const rgbValue = document.getElementById('rgbValue');
const hexValue = document.getElementById('hexValue');

randomBtn.addEventListener('click', function() {
  // Generate random RGB values (0-255)
  const r = Math.floor(Math.random() * 256);
  const g = Math.floor(Math.random() * 256);
  const b = Math.floor(Math.random() * 256);
  
  // Apply to background
  document.body.style.backgroundColor = `rgb(${r}, ${g}, ${b})`;
  
  // Update display
  rgbValue.textContent = `rgb(${r}, ${g}, ${b})`;
  hexValue.textContent = rgbToHex(r, g, b);
});

// Function to convert RGB to HEX
function rgbToHex(r, g, b) {
  return "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);
}
```

### 🚀 JavaScript - Versi Modern

<details>
<summary>💡 <strong>Klik untuk lihat versi dengan template literals</strong></summary>

```javascript
const elements = {
  randomBtn: document.querySelector('#randomBtn'),
  rgbValue: document.querySelector('#rgbValue'),
  hexValue: document.querySelector('#hexValue')
};

// Generate random color
const generateRandomColor = () => {
  const randomValue = () => Math.floor(Math.random() * 256);
  return [randomValue(), randomValue(), randomValue()];
};

// Convert RGB to HEX
const rgbToHex = (r, g, b) => 
  `#${[r, g, b].map(x => x.toString(16).padStart(2, '0')).join('')}`;

// Update color display
const updateColorDisplay = ([r, g, b]) => {
  const rgbString = `rgb(${r}, ${g}, ${b})`;
  const hexString = rgbToHex(r, g, b);
  
  document.body.style.backgroundColor = rgbString;
  elements.rgbValue.textContent = rgbString;
  elements.hexValue.textContent = hexString;
};

elements.randomBtn.addEventListener('click', () => {
  const randomColor = generateRandomColor();
  updateColorDisplay(randomColor);
});
```

</details>

---

## 🌈 Color Picker

Menggunakan HTML5 color picker untuk memilih warna.

### 📝 HTML

```html
<input type="color" id="colorPicker" value="#ff0000">
<button id="copyBtn">📋 Copy Color Code</button>
<p id="selectedColor">Selected: #ff0000</p>
```

### ⚡ JavaScript - Versi Klasik

```javascript
const colorPicker = document.getElementById('colorPicker');
const copyBtn = document.getElementById('copyBtn');
const selectedColor = document.getElementById('selectedColor');

colorPicker.addEventListener('input', function() {
  const color = colorPicker.value;
  document.body.style.backgroundColor = color;
  selectedColor.textContent = `Selected: ${color}`;
});

copyBtn.addEventListener('click', function() {
  const color = colorPicker.value;
  navigator.clipboard.writeText(color);
  alert(`Color ${color} copied to clipboard!`);
});
```

### 🚀 JavaScript - Versi Modern

<details>
<summary>💡 <strong>Klik untuk lihat versi dengan async/await</strong></summary>

```javascript
const colorPicker = document.querySelector('#colorPicker');
const copyBtn = document.querySelector('#copyBtn');
const selectedColor = document.querySelector('#selectedColor');

// Update color function
const updateColor = (color) => {
  document.body.style.backgroundColor = color;
  selectedColor.textContent = `Selected: ${color}`;
};

// Copy to clipboard with modern API
const copyToClipboard = async (text) => {
  try {
    await navigator.clipboard.writeText(text);
    // Modern notification (replace alert)
    showNotification(`Color ${text} copied!`);
  } catch (err) {
    console.error('Failed to copy: ', err);
  }
};

// Simple notification function
const showNotification = (message) => {
  const notification = document.createElement('div');
  notification.textContent = message;
  notification.style.cssText = `
    position: fixed; top: 20px; right: 20px;
    background: #333; color: white; padding: 10px;
    border-radius: 5px; z-index: 1000;
  `;
  document.body.appendChild(notification);
  setTimeout(() => notification.remove(), 2000);
};

// Event listeners
colorPicker.addEventListener('input', (e) => updateColor(e.target.value));
copyBtn.addEventListener('click', () => copyToClipboard(colorPicker.value));
```

</details>

---

## 📋 Daftar Isi

- [🎚️ RGB Slider](#-rgb-slider)
- [🖱️ Mouse Movement Effect](#️-mouse-movement-effect)
- [🎲 Random Color Generator](#-random-color-generator)
- [🌈 Color Picker](#-color-picker)
- [💡 Tips & Catatan](#-tips--catatan)

---

## 🎚️ RGB Slider

Membuat 3 slider untuk mengontrol nilai Red, Green, dan Blue (0-255) yang akan mengubah warna background secara real-time.

### 📝 HTML Structure

```html
<div class="kotak merah"></div>
<input type="range" name="sMerah" min="0" max="255" />

<div class="kotak hijau"></div>
<input type="range" name="sHijau" min="0" max="255" />

<div class="kotak biru"></div>
<input type="range" name="sBiru" min="0" max="255" />
```

**Penjelasan:**
- `<div class="kotak merah">` → Kotak indikator warna merah
- `<input type="range">` → Slider dengan nilai 0-255 (range RGB)
- `name="sMerah"` → Identifier untuk JavaScript

### 🎨 CSS Styling

```css
body {
  text-align: center;
}

.kotak {
  width: 25px;
  height: 25px;
  margin: 10px auto;
}

.merah { background-color: red; }
.hijau { background-color: green; }
.biru { background-color: blue; }
```

### ⚡ JavaScript - Versi Klasik

```javascript
// Mengambil elemen slider
const sMerah = document.querySelector('input[name=sMerah]');
const sHijau = document.querySelector('input[name=sHijau]');
const sBiru = document.querySelector('input[name=sBiru]');

// Event listener untuk slider merah
sMerah.addEventListener('input', function () {
  const r = sMerah.value;
  const g = sHijau.value;
  const b = sBiru.value;
  document.body.style.backgroundColor = `rgb(${r},${g},${b})`;
});

// Event listener untuk slider hijau
sHijau.addEventListener('input', function () {
  const r = sMerah.value;
  const g = sHijau.value;
  const b = sBiru.value;
  document.body.style.backgroundColor = `rgb(${r},${g},${b})`;
});

// Event listener untuk slider biru
sBiru.addEventListener('input', function () {
  const r = sMerah.value;
  const g = sHijau.value;
  const b = sBiru.value;
  document.body.style.backgroundColor = `rgb(${r},${g},${b})`;
});
```

### 🚀 JavaScript - Versi Modern

<details>
<summary>💡 <strong>Klik untuk lihat versi yang lebih efisien</strong></summary>

```javascript
// Mengambil semua slider sekaligus
const sliders = {
  red: document.querySelector('input[name=sMerah]'),
  green: document.querySelector('input[name=sHijau]'),
  blue: document.querySelector('input[name=sBiru]')
};

// Fungsi untuk update background color
const updateBackgroundColor = () => {
  const r = sliders.red.value;
  const g = sliders.green.value;
  const b = sliders.blue.value;
  document.body.style.backgroundColor = `rgb(${r}, ${g}, ${b})`;
};

// Menambahkan event listener ke semua slider
Object.values(sliders).forEach(slider => {
  slider.addEventListener('input', updateBackgroundColor);
});
```

**Keunggulan versi modern:**
- ✅ Tidak ada kode berulang (DRY principle)
- ✅ Lebih mudah maintenance
- ✅ Fungsi terpisah, lebih terorganisir

</details>

---

## 🖱️ Mouse Movement Effect

Membuat background berubah warna mengikuti posisi mouse di layar.

### ⚡ JavaScript - Versi Klasik

```javascript
document.body.addEventListener('mousemove', function (event) {
  // Mendapatkan posisi mouse
  const xPos = Math.round((event.clientX / window.innerWidth) * 255);
  const yPos = Math.round((event.clientY / window.innerHeight) * 255);
  
  // Mengubah background berdasarkan posisi mouse
  document.body.style.backgroundColor = `rgb(${xPos}, ${yPos}, 100)`;
});
```

**Penjelasan:**
- `event.clientX` → Posisi mouse horizontal (px)
- `window.innerWidth` → Lebar browser (px)
- `(clientX / innerWidth) * 255` → Konversi ke nilai RGB (0-255)
- `Math.round()` → Membulatkan ke bilangan bulat

### 🚀 JavaScript - Versi Modern

<details>
<summary>💡 <strong>Klik untuk lihat versi dengan throttling</strong></summary>

```javascript
// Throttle function untuk performa lebih baik
const throttle = (func, delay) => {
  let timeoutId;
  let lastExecTime = 0;
  return function (...args) {
    const currentTime = Date.now();
    
    if (currentTime - lastExecTime > delay) {
      func.apply(this, args);
      lastExecTime = currentTime;
    } else {
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => {
        func.apply(this, args);
        lastExecTime = Date.now();
      }, delay - (currentTime - lastExecTime));
    }
  };
};

// Mouse movement handler
const handleMouseMove = (event) => {
  const { clientX, clientY } = event;
  const { innerWidth, innerHeight } = window;
  
  const xPos = Math.round((clientX / innerWidth) * 255);
  const yPos = Math.round((clientY / innerHeight) * 255);
  
  document.body.style.backgroundColor = `rgb(${xPos}, ${yPos}, 100)`;
};

// Menggunakan throttle untuk performa lebih baik
document.body.addEventListener('mousemove', throttle(handleMouseMove, 16));
```

**Keunggulan versi modern:**
- ✅ Throttling untuk performa lebih baik
- ✅ Destructuring assignment
- ✅ Arrow function yang lebih clean

</details>

---

## 💡 Tips & Catatan

### 📌 Debugging Tips

```javascript
// Untuk mengecek nilai slider
console.log(sMerah.value);

// Untuk mengecek posisi mouse
console.log(event.clientX, event.clientY);

// Untuk mengecek ukuran browser
console.log(window.innerWidth, window.innerHeight);
```

### ⚠️ Yang Perlu Diperhatikan

- **Range Input:** Selalu gunakan `min="0" max="255"` untuk RGB
- **Event Type:** Gunakan `'input'` bukan `'change'` untuk real-time update
- **Performance:** Mouse movement bisa intensif, pertimbangkan throttling untuk aplikasi kompleks

### 🎯 Variasi yang Bisa Dicoba

1. **Fixed Blue Value:** Ganti nilai blue dengan konstanta (misal: 100)
2. **Alpha Channel:** Gunakan `rgba()` untuk transparansi
3. **HSL Color:** Coba gunakan `hsl()` sebagai alternatif RGB
4. **Gradient Effect:** Kombinasikan beberapa warna untuk gradient
5. **Color Palette:** Simpan warna favorit dalam array

### 🧪 Mini Project Ideas

**🎮 Beginner Projects:**
- Color guessing game (tebak warna dari RGB)
- Traffic light simulator dengan 3 warna
- Mood ring (warna berubah berdasarkan waktu)

**🔥 Intermediate Projects:**
- Color palette generator
- Accessibility color contrast checker
- RGB to other color format converter

---

**🎉 Selamat! Anda sudah belajar cara manipulasi warna dengan JavaScript.**

> 📝 **Next Steps:** Coba kombinasikan kedua teknik ini untuk membuat efek yang lebih menarik!
