# Design Document — The Journey of X & XI MPLB

## Overview

Website ini adalah jurnal digital satu halaman (*single-page*) untuk kelas MPLB yang merekam perjalanan dari Kelas 10 hingga Kelas 11, mencakup 33 siswa. Seluruh aplikasi dikemas dalam satu file `index.html` yang dapat dibuka langsung di browser tanpa server atau build tool.

**Tujuan teknis utama:**

- Zero-dependency deployment: satu file HTML yang dapat dibagikan via Google Drive, WhatsApp, atau email.
- Styling menggunakan Tailwind CSS via CDN (tidak ada node_modules, tidak ada `npm install`).
- Semua interaktivitas diimplementasikan dengan vanilla JavaScript yang embedded di dalam file HTML.
- Data siswa disimpan dalam satu array `studentsData` yang mudah diedit oleh non-developer.

**Fitur utama:**

| Fitur | Deskripsi |
|---|---|
| Hero Section | Judul, pengantar, tombol navigasi ke Kelas 10 & 11 |
| Profil Wali Kelas | Dua kartu berdampingan untuk wali kelas 10 & 11 |
| Direktori Siswa | Grid 33 kartu + search real-time |
| Tab Informasi Kelas | Jadwal & organisasi Kelas 10 / 11 dengan tab switcher |
| Galeri Kenangan | Album foto per angkatan + lightbox modal |

---

## Architecture

### Single-File Architecture

```
index.html
├── <head>
│   ├── <meta> tags (charset, viewport, title, description)
│   ├── <script src="https://cdn.tailwindcss.com"></script>  ← Tailwind CDN
│   └── <style>                                              ← Custom CSS (hanya untuk efek non-Tailwind)
│       ├── Scrollbar styling
│       ├── Modal transition keyframes
│       ├── Gallery hover overlay
│       └── Tema warna CSS custom properties
│
└── <body>
    ├── <nav>            ← Sticky navigation header
    ├── <section id="hero">
    ├── <section id="wali-kelas">
    ├── <section id="direktori">
    │   ├── Search bar
    │   └── #student-grid (populated by JS)
    ├── <section id="kelas-10">
    │   └── Tab panel: jadwal, piket, organisasi
    ├── <section id="kelas-11">
    │   └── Tab panel: jadwal, piket, organisasi
    ├── <section id="galeri">
    │   ├── Album tab navigation
    │   └── Photo grid + modal
    ├── <footer>
    └── <script>         ← Satu tag script, akhir body
        ├── const studentsData = [...]
        ├── renderStudents()
        ├── setupSearch()
        ├── setupTabs()
        ├── setupGallery()
        └── setupModal()
```

### Alur Data

```
studentsData (array)
       │
       ▼
renderStudents()          ← dipanggil saat DOMContentLoaded
       │
       ├──► #student-grid  (render 33 Student_Card ke DOM)
       │
setupSearch()
       │
       ├──► input#search-bar  (event listener 'input')
       │           │
       │           ▼
       │    filterStudents(query)  ← filter studentsData, re-render atau toggle visibility
       │
setupModal()
       │
       └──► event delegation pada #gallery-grid  (click → open modal)
                        │
                        ▼
                  openModal(src, alt)  ← inject foto ke #modal-img
```

### Dependency Map

```
Tailwind CSS CDN  ──►  semua utility class di HTML
        (tidak ada dependency lain — zero npm packages)
```

### Offline Fallback

Karena Tailwind dimuat via CDN, jika offline:
- Seluruh konten HTML tetap tersedia dan terbaca.
- Layout fallback dipertahankan via CSS custom properties dan beberapa inline style minimal pada elemen kritis (flex, block, padding).

---

## Components and Interfaces

### 1. Sticky Navigation (`<nav>`)

**Responsibility:** Navigasi antar seksi dengan smooth scroll; tetap terlihat di atas viewport.

```
Nav
 ├── Logo/Title text
 └── Nav links (ul > li > a)
      ├── href="#wali-kelas"   → "Wali Kelas"
      ├── href="#direktori"    → "Direktori"
      ├── href="#kelas-10"     → "Kelas 10"
      ├── href="#kelas-11"     → "Kelas 11"
      └── href="#galeri"       → "Galeri"
 └── [Mobile] Hamburger button (toggle menu visibility < 640px)
```

**Behavior:**
- `position: sticky; top: 0; z-index: 50` via Tailwind.
- Pada layar < 640px: tombol hamburger toggle menu vertikal.
- Klik link → `element.scrollIntoView({ behavior: 'smooth' })`.

---

### 2. Hero Section

**Responsibility:** First impression — judul, tagline, CTA buttons.

```
Hero
 ├── <h1> "The Journey of X & XI MPLB"
 ├── <p>  kalimat pengantar
 └── CTA Buttons
      ├── <button id="btn-kelas-10"> "Lihat Kenangan Kelas 10"
      └── <button id="btn-kelas-11"> "Lihat Aktivitas Kelas 11"
```

**Background:** Linear gradient yang menggabungkan Kelas_10_Theme (slate/teal) dan Kelas_11_Theme (indigo/purple) dalam satu `background: linear-gradient(...)`.

**Button behavior (JS):**
```javascript
function scrollToSection(sectionId) {
  const target = document.getElementById(sectionId);
  if (!target) {
    console.error(`Konten dengan id "${sectionId}" tidak ditemukan.`);
    return;
  }
  target.scrollIntoView({ behavior: 'smooth' });
}
```

---

### 3. Teacher Profile Cards (`Teacher_Profile_Card`)

**Responsibility:** Menampilkan profil dua wali kelas secara berdampingan.

```
#wali-kelas (section)
 └── .grid (2 kolom ≥768px, 1 kolom <768px)
      ├── Teacher_Profile_Card ("Wali Kelas 10")
      │    ├── foto / avatar inisial / ikon default
      │    ├── label "Wali Kelas 10"
      │    ├── <h2> nama lengkap
      │    └── <p> pesan singkat
      └── Teacher_Profile_Card ("Wali Kelas 11")
           ├── foto / avatar inisial / ikon default
           ├── label "Wali Kelas 11"
           ├── <h2> nama lengkap
           └── <p> pesan singkat
```

**Avatar fallback hierarchy:**
1. `<img src="...">` jika foto tersedia → onerror handler mengganti dengan inisial
2. `<div>` berisi inisial nama (2 karakter) jika foto gagal / tidak ada
3. SVG ikon default jika nama juga kosong

**Tema:** Kelas 10 → `text-teal-700 border-teal-300`; Kelas 11 → `text-indigo-700 border-indigo-300`.

---

### 4. Student Directory

#### 4a. `studentsData` Array (Data Layer)

```javascript
const studentsData = [
  {
    absen: 1,                        // number, 1–33
    namaLengkap: "Siswa 1",          // string, max 100 char
    namaPanggilan: "Panggilan 1",    // string, max 30 char
    fotoKelas10: "img/siswa1-10.jpg",// string URL/path, max 300 char
    fotoKelas11: "img/siswa1-11.jpg",// string URL/path, max 300 char
    kutipan: "Kutipan siswa 1..."    // string, max 300 char
  },
  // ... 32 entri berikutnya
];
```

#### 4b. `renderStudents(students)` (Render Function)

**Input:** Array of student objects (subset atau full `studentsData`).  
**Output:** Memodifikasi innerHTML dari `#student-grid`.

```
renderStudents(students)
 ├── Jika students.length < 33 dan bukan hasil filter → console.error(...)
 ├── Jika students.length === 0 → tampilkan "Siswa tidak ditemukan."
 └── Untuk setiap student → buat Student_Card HTML string → join → set innerHTML
```

#### 4c. `Student_Card` HTML Structure

```html
<article class="student-card ...">
  <div class="foto-wrapper">
    <img src="{fotoKelas10}" alt="{namaLengkap}" onerror="gantiPlaceholder(this, '{namaLengkap}')">
    <img src="{fotoKelas11}" alt="{namaLengkap}" onerror="gantiPlaceholder(this, '{namaLengkap}')">
  </div>
  <div class="info">
    <span class="absen">#{absen}</span>
    <h3 class="nama-lengkap">{namaLengkap}</h3>
    <p class="nama-panggilan">"{namaPanggilan}"</p>
    <blockquote class="kutipan">{kutipan dipotong 150 char}</blockquote>
  </div>
</article>
```

#### 4d. Search Bar (`setupSearch`)

```
#search-bar (input[type="text"])
       │
       ▼ event: 'input'
filterStudents(query)
  ├── trim + lowercase query
  ├── filter studentsData di mana namaLengkap.toLowerCase().includes(query)
  │                           ATAU namaPanggilan.toLowerCase().includes(query)
  └── renderStudents(filtered)
```

**Debounce:** Tidak diperlukan untuk 33 item — filter sinkron cukup cepat < 300ms.  
Namun filter dipanggil langsung pada setiap event `input` untuk memastikan responsivitas ≤ 300ms.

---

### 5. Tab System (`setupTabs`)

**Responsibility:** Switch antara konten Kelas 10 dan Kelas 11 tanpa page reload.

```
#tab-system
 ├── .tab-buttons
 │    ├── <button data-tab="kelas-10"> "Kelas 10"
 │    └── <button data-tab="kelas-11"> "Kelas 11"
 └── .tab-panels
      ├── <div id="panel-kelas-10" class="tab-panel">
      │    ├── Jadwal Pelajaran (tabel placeholder)
      │    ├── Jadwal Piket (tabel placeholder)
      │    └── Struktur Organisasi (list placeholder)
      └── <div id="panel-kelas-11" class="tab-panel hidden">
           ├── Jadwal Pelajaran (tabel placeholder)
           ├── Jadwal Piket (tabel placeholder)
           └── Struktur Organisasi (list placeholder)
```

**State logic:**
```javascript
function setupTabs() {
  const tombolTab = document.querySelectorAll('[data-tab]');
  tombolTab.forEach(tombol => {
    tombol.addEventListener('click', () => {
      const targetTab = tombol.dataset.tab;
      // Jika tab yang diklik sudah aktif → tidak melakukan apapun
      if (tombol.classList.contains('tab-aktif')) return;
      // Sembunyikan semua panel, hapus state aktif
      // Tampilkan panel target, set state aktif
    });
  });
}
```

**Default state:** Tab "Kelas 10" aktif saat halaman dimuat.

**Visual distinction:** Tab aktif mendapat `border-b-2` + warna tema; tab tidak aktif mendapat warna muted.

---

### 6. Gallery & Modal

#### 6a. Gallery Structure

```
#galeri (section)
 ├── .album-tabs
 │    ├── <button data-album="album-10"> "Album Cerita Kelas 10"
 │    └── <button data-album="album-11"> "Album Cerita Kelas 11"
 └── .album-panels
      ├── <div id="album-10">
      │    └── .foto-grid (CSS Grid, 2–4 col responsif)
      │         └── [6+ thumbnail items]
      │              ├── <img src="..." alt="keterangan foto">
      │              └── hover overlay dengan keterangan
      └── <div id="album-11" class="hidden">
           └── .foto-grid
                └── [6+ thumbnail items]
```

#### 6b. Gallery Hover Behavior (CSS)

```css
.foto-item {
  position: relative;
  overflow: hidden;
}
.foto-item .overlay {
  position: absolute;
  inset: 0;
  background: rgba(0,0,0,0.5);
  opacity: 0;
  transition: opacity 200ms ease;
  display: flex;
  align-items: flex-end;
}
.foto-item:hover .overlay {
  opacity: 1;
}
.foto-item:hover img {
  transform: scale(1.05);
  transition: transform 300ms ease;
}
```

#### 6c. Modal (`setupModal` + `openModal` + `tutupModal`)

```
#modal-overlay (div, fixed inset-0, bg-black/70, z-50, hidden)
 └── #modal-container (div, centered, max-w-[90vw] max-h-[90vh])
      ├── <button id="tombol-tutup"> × (min 44×44px)
      └── #modal-gambar (<img>, object-contain, max-w/h 90vw/90vh)
```

**Event handlers:**
```
Klik thumbnail      → openModal(src, alt)
Klik overlay        → tutupModal()
Klik tombol-tutup   → tutupModal()
Keydown 'Escape'    → tutupModal()
```

**openModal(src, alt):**
1. Set `#modal-gambar` src dan alt
2. Remove `hidden` dari `#modal-overlay`
3. `document.body.style.overflow = 'hidden'` (cegah scroll)
4. Focus ke `#tombol-tutup` (aksesibilitas)

**tutupModal():**
1. Add `hidden` ke `#modal-overlay`
2. `document.body.style.overflow = ''` (restore scroll)
3. Clear src dari `#modal-gambar`

**Foto gagal load di modal:**
- `#modal-gambar` onerror handler → tampilkan `<p id="modal-error">Foto tidak dapat ditampilkan.</p>` dan sembunyikan `#modal-gambar`.

---

## Data Models

### `StudentObject`

```typescript
interface StudentObject {
  absen: number;          // 1–33, unik
  namaLengkap: string;   // max 100 karakter
  namaPanggilan: string; // max 30 karakter
  fotoKelas10: string;   // URL atau path relatif, max 300 karakter
  fotoKelas11: string;   // URL atau path relatif, max 300 karakter
  kutipan: string;       // max 300 karakter
}
```

**Constraints:**
- `studentsData.length === 33`
- `absen` values: {1, 2, 3, ..., 33} — masing-masing tepat sekali (unik dan berurutan)
- Tidak ada properti tambahan yang diperlukan; array bersifat flat (bukan nested)

### `FotoGaleri` (implicit — embedded dalam HTML)

```typescript
interface FotoGaleri {
  src: string;        // URL/path foto
  alt: string;        // keterangan, 1–100 karakter
  album: "10" | "11"; // album kelas 10 atau kelas 11
}
```

Minimum 6 item per album → minimal 12 total placeholder.

### `WaliKelas` (implicit — hardcoded dalam HTML)

```typescript
interface WaliKelas {
  kelas: "10" | "11";
  foto?: string;       // opsional — fallback ke inisial jika kosong
  namaLengkap: string; // max 60 karakter
  pesan: string;       // max 150 karakter
}
```

### State Model (Runtime, tidak persisten)

```typescript
interface AppState {
  queryPencarian: string;        // isi Search_Bar saat ini
  tabAktif: "kelas-10" | "kelas-11"; // tab yang sedang aktif
  albumAktif: "album-10" | "album-11"; // album galeri yang aktif
  modalTerbuka: boolean;         // apakah modal sedang terbuka
  modalSrc: string;              // src foto yang sedang dibuka di modal
}
```

State ini **tidak disimpan** ke localStorage atau URL — cukup sebagai state in-memory yang dikelola langsung oleh DOM (class `hidden`, atribut `src`).

---

## Correctness Properties

*A property is a characteristic or behavior that should hold true across all valid executions of a system — essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.*

### Property 1: Render count matches data length

*For any* valid `studentsData` array dengan N elemen, memanggil `renderStudents(studentsData)` SHALL menghasilkan tepat N elemen `<article class="student-card">` di dalam `#student-grid`.

**Validates: Requirements 5.1**

---

### Property 2: Search filter inclusivity

*For any* query string `q` dan `studentsData`, setiap kartu yang ditampilkan setelah `filterStudents(q)` SHALL memiliki `namaLengkap` atau `namaPanggilan` yang mengandung `q` (case-insensitive); dan tidak ada kartu yang tersembunyi apabila data siswa tersebut memenuhi kriteria tersebut.

**Validates: Requirements 6.2**

---

### Property 3: Empty/whitespace search restores full list

*For any* query yang seluruhnya terdiri dari karakter whitespace (termasuk string kosong), memanggil `filterStudents(q)` SHALL menghasilkan semua 33 Student_Card ditampilkan di `#student-grid`.

**Validates: Requirements 6.3**

---

### Property 4: Student card data fidelity

*For any* `StudentObject` dalam `studentsData`, Student_Card yang dirender SHALL menampilkan nilai `absen`, `namaLengkap`, `namaPanggilan`, dan `kutipan` (dipotong ≤ 150 karakter) yang berkorespondensi tepat dengan data sumbernya, dan atribut `alt` pada elemen `<img>` SHALL sama dengan `namaLengkap`.

**Validates: Requirements 5.3, 5.4**

---

### Property 5: Tab state exclusivity

*For any* klik pada tombol tab, setelah event handler selesai, SHALL ada tepat satu tab panel yang tidak memiliki class `hidden` dan tepat satu tombol tab yang memiliki class `tab-aktif`, dan keduanya harus berkorespondensi pada tab yang sama.

**Validates: Requirements 7.2, 7.3, 7.4, 7.5**

---

### Property 6: Tab idempotence

*For any* tab yang sedang aktif, mengklik tombol tab tersebut kembali SHALL tidak mengubah state DOM (tidak ada perubahan class, tidak ada perubahan visibility panel).

**Validates: Requirements 7.8**

---

### Property 7: Modal scroll lock

*For any* aksi `openModal(src, alt)`, setelah modal terbuka, `document.body.style.overflow` SHALL bernilai `'hidden'`; dan setelah `tutupModal()` dipanggil, `document.body.style.overflow` SHALL kembali ke nilai string kosong `''`.

**Validates: Requirements 9.6**

---

### Property 8: Gallery alt attribute non-empty

*For any* elemen `<img>` di dalam `#galeri`, atribut `alt` SHALL memiliki panjang minimal 1 karakter.

**Validates: Requirements 8.5**

---

### Property 9: Kutipan truncation invariant

*For any* `StudentObject` dengan `kutipan` berisi string sepanjang N karakter, teks kutipan yang ditampilkan di Student_Card SHALL memiliki panjang ≤ 150 karakter; jika N > 150 maka teks SHALL diakhiri dengan karakter elipsis (`…` atau `...`).

**Validates: Requirements 5.3**

---

### Property 10: studentsData uniqueness and order

*For any* array `studentsData`, nilai properti `absen` pada setiap elemen SHALL unik di dalam array, dan elemen-elemen SHALL terurut secara ascending berdasarkan `absen` (elemen pertama memiliki `absen` terkecil).

**Validates: Requirements 4.2, 5.1**

---

### Property 11: Data validation warning for incorrect count

*For any* array siswa dengan panjang yang tidak sama dengan 33, fungsi validasi data SHALL memanggil `console.warn` tepat sekali; dan untuk array dengan panjang tepat 33, fungsi validasi SHALL tidak memanggil `console.warn`.

**Validates: Requirements 4.6, 5.6**

---

### Property 12: Photo placeholder initials correctness

*For any* string `namaLengkap` yang tidak kosong, fungsi `gantiPlaceholder` SHALL menghasilkan teks inisial yang terdiri dari huruf pertama setiap kata (uppercase), dipotong maksimal 2 karakter; dan untuk string kosong, SHALL menghasilkan karakter `"?"`.

**Validates: Requirements 5.5, 3.5**

---

### Property 13: No-match search message

*For any* query yang tidak cocok dengan satupun siswa dalam array, `renderStudents` SHA menampilkan pesan "Siswa tidak ditemukan." di dalam `#student-grid`, dan tidak merender satupun `Student_Card`.

**Validates: Requirements 6.4**

---

## Error Handling

### Strategi Umum

Error handling di website ini bersifat *defensive* namun tidak mengganggu user experience. Mengikuti prinsip: **fail visibly in dev, fail gracefully in prod**.

### Error Cases per Komponen

#### 1. Data Validation (`studentsData`)

| Kondisi | Handler | Output |
|---|---|---|
| `studentsData.length !== 33` | `console.warn(...)` saat `DOMContentLoaded` | Warning di konsol, grid tidak dirender → tampilkan pesan error di halaman |
| Properti elemen tidak lengkap | Nilai fallback (`""` / `"#"`) digunakan saat render | Kartu tetap dirender dengan placeholder |

```javascript
// Saat DOMContentLoaded
if (studentsData.length !== 33) {
  console.warn(`[Journey] Jumlah data siswa tidak sesuai: ditemukan ${studentsData.length}, diharapkan 33.`);
  document.getElementById('student-grid').innerHTML =
    '<p class="error-data">Data siswa tidak lengkap. Harap periksa array studentsData.</p>';
  return;
}
```

#### 2. Foto Gagal Dimuat (`<img> onerror`)

```javascript
function gantiPlaceholder(imgElement, namaLengkap) {
  const inisial = namaLengkap
    .split(' ')
    .map(kata => kata[0])
    .join('')
    .toUpperCase()
    .slice(0, 2);
  // Ganti img dengan div inisial
  const placeholder = document.createElement('div');
  placeholder.className = 'foto-placeholder';
  placeholder.textContent = inisial || '?';
  imgElement.replaceWith(placeholder);
}
```

#### 3. Target Scroll Tidak Ditemukan

```javascript
function scrollKeSection(sectionId) {
  const target = document.getElementById(sectionId);
  if (!target) {
    console.error(`[Journey] Section dengan id "${sectionId}" tidak ditemukan. Scroll dibatalkan.`);
    return;
  }
  target.scrollIntoView({ behavior: 'smooth' });
}
```

#### 4. Foto Gagal Dimuat di Modal

```javascript
modalGambar.onerror = function() {
  this.style.display = 'none';
  document.getElementById('modal-pesan-error').textContent = 'Foto tidak dapat ditampilkan.';
  document.getElementById('modal-pesan-error').classList.remove('hidden');
};
```

#### 5. Pencarian Tanpa Hasil

```javascript
if (hasil.length === 0) {
  studentGrid.innerHTML = '<p class="pesan-kosong">Siswa tidak ditemukan.</p>';
}
```

### Console Error Taxonomy

| Prefix | Level | Kondisi |
|---|---|---|
| `[Journey] Jumlah data...` | `warn` | studentsData.length !== 33 |
| `[Journey] Section "..." tidak ditemukan` | `error` | ID target scroll tidak ada di DOM |
| `[Journey] Foto gagal dimuat` | (via onerror, no console) | img src gagal → replace dengan placeholder |

---

## Testing Strategy

### Pendekatan Pengujian

Website ini menggunakan **dual testing approach**:

1. **Unit Tests** — menguji fungsi JavaScript murni secara terpisah (filter, render, truncation, tab state)
2. **Property-Based Tests** — menguji properti universal yang harus berlaku untuk semua input valid

### Property-Based Testing Framework

Untuk vanilla JavaScript, digunakan **[fast-check](https://fast-check.io/)** sebagai PBT library.

```html
<!-- Untuk keperluan testing (tidak ada di file produksi) -->
<script src="https://cdn.jsdelivr.net/npm/fast-check@3.x/lib/bundle/es.js"></script>
```

Setiap property test dikonfigurasi dengan minimum **100 iterasi**. Format tag:

```
Feature: journey-of-x-xi-website, Property {N}: {deskripsi singkat}
```

### Unit Tests (Vitest / Jest)

Karena output adalah file HTML tunggal, fungsi-fungsi JavaScript diekstrak ke dalam file terpisah untuk keperluan testing, lalu hasilnya di-inline kembali ke `index.html`.

**File test:** `__tests__/functions.test.js`

| Test Case | Fungsi | Skenario |
|---|---|---|
| Kutipan panjang dipotong | `potongKutipan(teks, 150)` | Input 200 char → output 150 char + `…` |
| Kutipan pendek tidak dipotong | `potongKutipan(teks, 150)` | Input 50 char → output identik |
| Inisial dari nama lengkap | `ambilInisial(nama)` | `"Budi Santoso"` → `"BS"` |
| Inisial nama kosong | `ambilInisial("")` | Output `"?"` |
| Render output berisi data siswa | `renderStudents([...])` | Output HTML contains nama siswa |
| Scroll ke section tidak ada | `scrollKeSection("tidak-ada")` | console.error dipanggil, tidak throw |

### Property-Based Tests

**File test:** `__tests__/properties.test.js`

#### PBT 1 — Render count matches data length
```javascript
// Feature: journey-of-x-xi-website, Property 1: render count matches data length
fc.assert(fc.property(
  fc.array(studentArbitrary, { minLength: 1, maxLength: 50 }),
  (students) => {
    const html = renderStudentsToHTML(students);
    const count = (html.match(/class="student-card/g) || []).length;
    return count === students.length;
  }
), { numRuns: 100 });
```

#### PBT 2 — Search filter inclusivity
```javascript
// Feature: journey-of-x-xi-website, Property 2: search filter inclusivity
fc.assert(fc.property(
  fc.array(studentArbitrary, { minLength: 1, maxLength: 33 }),
  fc.string({ minLength: 1, maxLength: 20 }),
  (students, query) => {
    const hasil = filterStudents(students, query);
    return hasil.every(s =>
      s.namaLengkap.toLowerCase().includes(query.toLowerCase()) ||
      s.namaPanggilan.toLowerCase().includes(query.toLowerCase())
    );
  }
), { numRuns: 100 });
```

#### PBT 3 — Empty/whitespace query restores full list
```javascript
// Feature: journey-of-x-xi-website, Property 3: empty/whitespace search restores full list
fc.assert(fc.property(
  fc.array(studentArbitrary, { minLength: 1, maxLength: 33 }),
  fc.stringOf(fc.constantFrom(' ', '\t', '\n'), { maxLength: 10 }),
  (students, querySpasi) => {
    const hasil = filterStudents(students, querySpasi);
    return hasil.length === students.length;
  }
), { numRuns: 100 });
```

#### PBT 4 — Student card data fidelity
```javascript
// Feature: journey-of-x-xi-website, Property 4: student card data fidelity
fc.assert(fc.property(
  studentArbitrary,
  (student) => {
    const html = renderSingleCard(student);
    return html.includes(student.namaLengkap) &&
           html.includes(`alt="${student.namaLengkap}"`) &&
           html.includes(String(student.absen));
  }
), { numRuns: 100 });
```

#### PBT 5 — Tab state exclusivity
```javascript
// Feature: journey-of-x-xi-website, Property 5: tab state exclusivity
fc.assert(fc.property(
  fc.constantFrom('kelas-10', 'kelas-11'),
  fc.constantFrom('kelas-10', 'kelas-11'),
  (tabAwal, tabKlik) => {
    const state = simulasiKlikTab(tabAwal, tabKlik);
    const panelTerlihat = state.panels.filter(p => !p.hidden).length;
    const tabAktifCount = state.tabs.filter(t => t.aktif).length;
    return panelTerlihat === 1 && tabAktifCount === 1;
  }
), { numRuns: 100 });
```

#### PBT 6 — Kutipan truncation invariant
```javascript
// Feature: journey-of-x-xi-website, Property 9: kutipan truncation invariant
fc.assert(fc.property(
  fc.string({ minLength: 0, maxLength: 500 }),
  (kutipan) => {
    const hasil = potongKutipan(kutipan, 150);
    // hasil.length ≤ 151 karena karakter elipsis '…' dihitung 1 karakter
    return hasil.length <= 151 &&
           (kutipan.length <= 150 ? hasil === kutipan : hasil.endsWith('…') || hasil.endsWith('...'));
  }
), { numRuns: 100 });
```

#### PBT 7 — Modal scroll lock round-trip
```javascript
// Feature: journey-of-x-xi-website, Property 7: modal scroll lock
fc.assert(fc.property(
  fc.string({ minLength: 1 }), // src foto
  fc.string({ minLength: 1 }), // alt teks
  (src, alt) => {
    openModal(src, alt);
    const overflowSaatTerbuka = document.body.style.overflow;
    tutupModal();
    const overflowSaatTertutup = document.body.style.overflow;
    return overflowSaatTerbuka === 'hidden' && overflowSaatTertutup === '';
  }
), { numRuns: 100 });
```

#### PBT 8 — Data validation warning for incorrect count
```javascript
// Feature: journey-of-x-xi-website, Property 11: data validation warning for incorrect count
fc.assert(fc.property(
  fc.array(studentArbitrary, { minLength: 0, maxLength: 50 }).filter(arr => arr.length !== 33),
  (students) => {
    const warnSpy = jest.spyOn(console, 'warn').mockImplementation(() => {});
    validasiDataSiswa(students);
    const dipanggil = warnSpy.mock.calls.length === 1;
    warnSpy.mockRestore();
    return dipanggil;
  }
), { numRuns: 100 });
```

#### PBT 9 — Photo placeholder initials correctness
```javascript
// Feature: journey-of-x-xi-website, Property 12: photo placeholder initials correctness
fc.assert(fc.property(
  fc.string({ minLength: 1, maxLength: 60 }).filter(s => s.trim().length > 0),
  (namaLengkap) => {
    const inisial = ambilInisial(namaLengkap);
    // Inisial adalah huruf kapital, panjang 1–2 karakter
    return inisial.length >= 1 && inisial.length <= 2 && inisial === inisial.toUpperCase();
  }
), { numRuns: 100 });
```

#### PBT 10 — No-match search message
```javascript
// Feature: journey-of-x-xi-website, Property 13: no-match search message
fc.assert(fc.property(
  fc.array(studentArbitrary, { minLength: 1, maxLength: 33 }),
  (students) => {
    // Query yang dipastikan tidak cocok dengan data apapun
    const queryTidakCocok = '@@@@TIDAKCOCOK@@@@';
    const hasilHTML = renderStudentsToHTML(filterStudents(students, queryTidakCocok));
    return hasilHTML.includes('Siswa tidak ditemukan.') &&
           !hasilHTML.includes('student-card');
  }
), { numRuns: 100 });
```

### Integration Tests

**File test:** `__tests__/integration.test.js`  
Menggunakan **Playwright** atau **jsdom** untuk menguji perilaku DOM end-to-end.

| Skenario | Ekspektasi |
|---|---|
| Halaman dimuat → 33 kartu dirender | `document.querySelectorAll('.student-card').length === 33` |
| Klik "Lihat Kenangan Kelas 10" | `#kelas-10` mencapai top viewport dalam 1 detik |
| Klik thumbnail galeri → modal muncul | `#modal-overlay` tidak memiliki class `hidden` dalam 300ms |
| Tekan Escape saat modal terbuka | `#modal-overlay` memiliki class `hidden` dalam 300ms |
| Navigasi sticky tetap visible saat scroll | `nav` `getBoundingClientRect().top >= 0` di semua posisi scroll |
| Search "tidak ada nama ini" → pesan kosong | DOM berisi teks "Siswa tidak ditemukan." |
| CDN offline (network blocked) → konten tetap tampil | HTML body berisi semua konten meski tanpa Tailwind styling |
| Foto gagal dimuat di modal → pesan error tampil | `#modal-pesan-error` visible, `#tombol-tutup` masih dapat diklik |

### Test Coverage Targets

| Area | Unit | Property | Integration |
|---|---|---|---|
| `studentsData` validation | ✓ | ✓ (P11) | ✓ |
| Render function | ✓ | ✓ (P1) | ✓ |
| Search/filter | ✓ | ✓ (P2, P3, P13) | ✓ |
| Card data fidelity | ✓ | ✓ (P4) | — |
| Kutipan truncation | ✓ | ✓ (P9) | — |
| Photo placeholder initials | ✓ | ✓ (P12) | — |
| Tab system | ✓ | ✓ (P5, P6) | ✓ |
| Modal scroll lock | ✓ | ✓ (P7) | ✓ |
| Gallery alt attrs | — | ✓ (P8) | — |
| studentsData order & uniqueness | ✓ | ✓ (P10) | — |
| Offline fallback | — | — | ✓ (manual/Playwright) |
