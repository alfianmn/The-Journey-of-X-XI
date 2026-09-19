# Implementation Plan: The Journey of X & XI MPLB

**Project Planner & Developer:** Alfian Mahadika Nadhif

## Overview

Implementasi website jurnal digital satu halaman dalam satu file `index.html` menggunakan HTML, Tailwind CSS (CDN), dan vanilla JavaScript. Tidak ada build tool, tidak ada dependensi npm untuk produksi. Urutan task mengikuti alur top-down struktur file: scaffold → navigasi → seksi-seksi konten → JavaScript interaktif → validasi data → testing.

---

## Tasks

- [x] 1. Scaffold file `index.html` — boilerplate, Tailwind CDN, blok CSS kustom, blok script
  - [x] 1.1 Buat file `index.html` dengan struktur HTML5 lengkap: `<!DOCTYPE html>`, `<html lang="id">`, `<head>` (charset, viewport, title, description), dan `<body>`
    - Tambahkan `<meta name="description">` yang mendeskripsikan website kelas
    - Tambahkan `<script src="https://cdn.tailwindcss.com"></script>` sebagai satu-satunya CDN di `<head>`
    - Tambahkan satu `<style>` kosong di dalam `<head>` untuk CSS kustom (scroll-bar, modal transition, gallery hover)
    - Tambahkan satu `<script>` kosong sebagai elemen terakhir sebelum `</body>` untuk semua JavaScript
    - _Requirements: 1.1, 1.3, 11.4_

  - [x]* 1.2 Tulis unit test: verifikasi struktur scaffold
    - Pastikan file berisi tepat satu tag `<style>` di `<head>` dan tepat satu tag `<script>` sebelum `</body>`
    - Pastikan CDN Tailwind hadir di `<head>`
    - _Requirements: 1.3, 11.4_

- [x] 2. Sticky navigation dengan mobile hamburger menu
  - [x] 2.1 Implementasikan `<nav>` sticky dengan Tailwind (`sticky top-0 z-50`) beserta logo/title dan nav links
    - Nav links: `href="#wali-kelas"`, `href="#direktori"`, `href="#kelas-10"`, `href="#kelas-11"`, `href="#galeri"`
    - Tambahkan komentar HTML `<!-- NAVIGASI: ganti teks logo dan href jika perlu -->` di awal elemen `<nav>`
    - _Requirements: 10.5, 11.1_

  - [x] 2.2 Implementasikan hamburger button dan toggle menu vertikal untuk layar < 640px
    - Tambahkan `<button id="tombol-hamburger">` dengan aria-label dan ikon tiga garis
    - Tambahkan fungsi `setupNavMobile()` di blok `<script>`: toggle class `hidden` pada menu nav saat hamburger diklik
    - Pastikan menu tidak menyebabkan horizontal scroll pada lebar < 640px
    - Tambahkan komentar JavaScript `// setupNavMobile: toggle menu hamburger pada layar mobile`
    - _Requirements: 10.7, 11.2, 11.3_

- [x] 3. Hero section — judul, tagline, CTA buttons dengan smooth scroll
  - [x] 3.1 Buat `<section id="hero">` dengan `<h1>`, `<p>` tagline, dan dua CTA button
    - `<h1>` berisi persis teks "The Journey of X & XI MPLB" dengan ukuran font ≥ 32px (`text-4xl` atau lebih besar); hanya ada satu `<h1>` di seluruh halaman
    - `<p>` berisi kalimat pengantar persis seperti di requirements 2.2
    - Dua tombol: `id="btn-kelas-10"` ("Lihat Kenangan Kelas 10") dan `id="btn-kelas-11"` ("Lihat Aktivitas Kelas 11")
    - Background: `linear-gradient` yang menggabungkan Kelas_10_Theme (teal/slate) dan Kelas_11_Theme (indigo/purple) — implementasikan via CSS kustom di `<style>` atau Tailwind gradient utilities
    - Tambahkan komentar HTML `<!-- HERO: ubah teks h1, tagline, dan warna gradient jika perlu -->`
    - _Requirements: 2.1, 2.2, 2.3, 2.6, 11.1_

  - [x] 3.2 Implementasikan fungsi `scrollKeSection(sectionId)` di blok `<script>` dan hubungkan ke kedua CTA button
    - Fungsi: cek apakah target ada di DOM; jika tidak → `console.error('[Journey] Section "..." tidak ditemukan')` dan return; jika ada → `scrollIntoView({ behavior: 'smooth' })`
    - Pasang event listener pada `btn-kelas-10` → `scrollKeSection('kelas-10')` dan `btn-kelas-11` → `scrollKeSection('kelas-11')`
    - Tambahkan komentar JavaScript `// scrollKeSection: smooth scroll ke seksi target; menampilkan error konsol jika target tidak ditemukan`
    - _Requirements: 2.4, 2.5, 2.7, 11.2, 11.3_

- [x] 4. Teacher profile cards — dua kartu berdampingan dengan avatar fallback
  - [x] 4.1 Buat `<section id="wali-kelas">` dengan grid 2-kolom (≥768px) / 1-kolom (<768px) dan dua `Teacher_Profile_Card`
    - Kartu pertama: label "Wali Kelas 10", foto/avatar, `<h2>` nama, `<p>` pesan singkat — styling dengan warna teal (`text-teal-700 border-teal-300`)
    - Kartu kedua: label "Wali Kelas 11", foto/avatar, `<h2>` nama, `<p>` pesan singkat — styling dengan warna indigo (`text-indigo-700 border-indigo-300`)
    - Placeholder: nama ≤ 60 karakter, pesan ≤ 150 karakter, `src` foto berupa path placeholder
    - Tambahkan komentar HTML `<!-- WALI KELAS: ganti src foto, nama, dan pesan di masing-masing kartu -->`
    - _Requirements: 3.1, 3.2, 3.3, 3.4, 3.6, 3.7, 11.1_

  - [x] 4.2 Implementasikan avatar fallback hierarchy di HTML (onerror handler inline) dan fungsi `gantiPlaceholder`
    - `gantiPlaceholder(imgElement, namaLengkap)`: ambil dua huruf pertama tiap kata → uppercase → slice 2 karakter; jika nama kosong → `"?"`; ganti `<img>` dengan `<div class="foto-placeholder">` berisi inisial
    - Pasang `onerror="gantiPlaceholder(this, 'Nama Wali Kelas')"` pada setiap `<img>` wali kelas
    - Tambahkan kondisi: jika `src` foto bernilai string kosong atau tidak ada, langsung render `<div>` inisial tanpa mencoba load `<img>`
    - Tambahkan komentar JavaScript `// gantiPlaceholder: mengganti img yang gagal dimuat dengan div berisi inisial nama`
    - _Requirements: 3.5, 11.2, 11.3_

  - [x]* 4.3 Tulis property test untuk `gantiPlaceholder` / `ambilInisial`
    - **Property 12: Photo placeholder initials correctness**
    - **Validates: Requirements 5.5, 3.5**
    - Gunakan `fc.string({ minLength: 1, maxLength: 60 })` filtered non-empty; assert `inisial.length >= 1 && inisial.length <= 2 && inisial === inisial.toUpperCase()`
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 12: photo placeholder initials correctness`

- [x] 5. Checkpoint — pastikan scaffold, nav, hero, dan wali kelas ter-render dengan benar
  - Buka `index.html` di browser, verifikasi: nav sticky terlihat, hero section muncul dengan gradient, dua kartu wali kelas berdampingan (layar lebar), hamburger menu berfungsi di lebar < 640px.
  - Pastikan tidak ada error konsol yang tidak diharapkan.

- [x] 6. Student directory — `studentsData` array, `renderStudents`, dan `gantiPlaceholder` untuk siswa
  - [x] 6.1 Deklarasikan `const studentsData = [...]` dengan tepat 33 elemen placeholder di blok `<script>`
    - Setiap elemen memiliki 6 properti: `absen` (1–33), `namaLengkap` (mengandung nomor absen, misal `"Siswa 1"`), `namaPanggilan`, `fotoKelas10`, `fotoKelas11`, `kutipan`
    - Tambahkan komentar panduan tepat satu kali per properti pada elemen pertama (`absen: 1`): `// namaLengkap: ganti dengan nama lengkap siswa`, dst.
    - Tambahkan komentar blok `/* studentsData: Array 33 siswa. Ganti nilai placeholder di setiap elemen dengan data nyata. */`
    - _Requirements: 4.1, 4.2, 4.3, 4.4, 4.5, 11.2_

  - [x] 6.2 Implementasikan fungsi `validasiDataSiswa(data)` dan panggil saat `DOMContentLoaded`
    - Jika `data.length !== 33`: `console.warn('[Journey] Jumlah data siswa tidak sesuai: ditemukan X, diharapkan 33.')` dan tampilkan pesan error di `#student-grid`, lalu return (hentikan render)
    - Tambahkan komentar JavaScript `// validasiDataSiswa: memeriksa jumlah data sebelum render; tampilkan warning jika tidak 33`
    - _Requirements: 4.6, 5.6, 11.2, 11.3_

  - [x]* 6.3 Tulis property test untuk `validasiDataSiswa`
    - **Property 11: Data validation warning for incorrect count**
    - **Validates: Requirements 4.6, 5.6**
    - Gunakan `fc.array(studentArbitrary, { minLength: 0, maxLength: 50 }).filter(arr => arr.length !== 33)`; spy `console.warn`; assert dipanggil tepat sekali
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 11: data validation warning for incorrect count`

  - [x] 6.4 Implementasikan fungsi `potongKutipan(teks, maks)` yang memotong string menjadi ≤ `maks` karakter dan menambahkan `…` jika terpotong
    - Tambahkan komentar JavaScript `// potongKutipan: memotong string kutipan menjadi maksimal N karakter dengan elipsis`
    - _Requirements: 5.3, 11.3_

  - [x]* 6.5 Tulis property test untuk `potongKutipan`
    - **Property 9: Kutipan truncation invariant**
    - **Validates: Requirements 5.3**
    - Gunakan `fc.string({ minLength: 0, maxLength: 500 })`; assert `hasil.length <= 151` dan jika `kutipan.length > 150` maka diakhiri `…` atau `...`
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 9: kutipan truncation invariant`

  - [x] 6.6 Implementasikan fungsi `ambilInisial(namaLengkap)` yang mengekstrak hingga 2 inisial huruf kapital, fallback `"?"` untuk nama kosong
    - Tambahkan komentar JavaScript `// ambilInisial: mengambil inisial nama (maks 2 huruf kapital) untuk placeholder foto`
    - _Requirements: 5.5, 3.5, 11.3_

  - [x] 6.7 Implementasikan fungsi `renderStudents(students)` yang membuat HTML string untuk setiap Student_Card dan meng-inject ke `#student-grid`
    - Jika `students.length === 0` → tampilkan `<p class="pesan-kosong">Siswa tidak ditemukan.</p>` di `#student-grid`
    - Setiap `<article class="student-card">` berisi: `<span>` nomor absen, `<h3>` nama lengkap, `<p>` nama panggilan, dua `<img>` dengan `alt=namaLengkap` dan `onerror="gantiPlaceholder(this, '...')"`, `<blockquote>` kutipan dipotong 150 karakter
    - Urutan kartu sesuai urutan array (yang sudah ascending by absen)
    - Tambahkan komentar JavaScript `// renderStudents: membuat HTML Student_Card dari array dan meng-inject ke #student-grid`
    - _Requirements: 5.1, 5.3, 5.4, 5.5, 6.4, 11.2, 11.3_

  - [x]* 6.8 Tulis property test untuk `renderStudents`
    - **Property 1: Render count matches data length**
    - **Validates: Requirements 5.1**
    - Gunakan `fc.array(studentArbitrary, { minLength: 1, maxLength: 50 })`; assert jumlah `class="student-card"` di HTML output = `students.length`
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 1: render count matches data length`

  - [x]* 6.9 Tulis property test untuk fidelitas data Student_Card
    - **Property 4: Student card data fidelity**
    - **Validates: Requirements 5.3, 5.4**
    - Gunakan `studentArbitrary`; assert HTML output berisi `namaLengkap`, `alt="${namaLengkap}"`, dan `String(absen)`
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 4: student card data fidelity`

  - [x]* 6.10 Tulis property test untuk no-match search message
    - **Property 13: No-match search message**
    - **Validates: Requirements 6.4**
    - Gunakan query `'@@@@TIDAKCOCOK@@@@'`; assert HTML berisi `'Siswa tidak ditemukan.'` dan tidak berisi `'student-card'`
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 13: no-match search message`

  - [x]* 6.11 Tulis property test untuk uniqueness dan urutan `studentsData`
    - **Property 10: studentsData uniqueness and order**
    - **Validates: Requirements 4.2, 5.1**
    - Assert nilai `absen` unik di seluruh array dan terurut ascending
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 10: studentsData uniqueness and order`

- [x] 7. Buat `<section id="direktori">` dengan Search_Bar dan `#student-grid`, lalu hubungkan ke render
  - [x] 7.1 Tambahkan HTML `<section id="direktori">` berisi `<input id="search-bar" type="text" placeholder="Cari nama siswa...">` dan `<div id="student-grid">` dengan CSS Grid responsif (1/2/3/4 kolom sesuai breakpoint)
    - CSS Grid: `grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4`
    - Tambahkan komentar HTML `<!-- DIREKTORI SISWA: data kartu siswa dirender otomatis dari array studentsData di blok script -->`
    - _Requirements: 5.2, 6.1, 11.1_

  - [x] 7.2 Implementasikan fungsi `filterStudents(students, query)` yang mengembalikan subset array berdasarkan `namaLengkap` atau `namaPanggilan` (case-insensitive)
    - Jika query setelah `trim()` kosong → kembalikan seluruh array
    - Tambahkan komentar JavaScript `// filterStudents: memfilter array siswa berdasarkan nama (case-insensitive); query kosong mengembalikan semua data`
    - _Requirements: 6.2, 6.3, 11.3_

  - [x]* 7.3 Tulis property test untuk `filterStudents` — search filter inclusivity
    - **Property 2: Search filter inclusivity**
    - **Validates: Requirements 6.2**
    - Gunakan `fc.array(studentArbitrary, { minLength: 1, maxLength: 33 })` dan `fc.string({ minLength: 1, maxLength: 20 })`; assert setiap hasil memenuhi kriteria case-insensitive
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 2: search filter inclusivity`

  - [x]* 7.4 Tulis property test untuk `filterStudents` — empty/whitespace restores full list
    - **Property 3: Empty/whitespace search restores full list**
    - **Validates: Requirements 6.3**
    - Gunakan `fc.stringOf(fc.constantFrom(' ', '\t', '\n'), { maxLength: 10 })`; assert `hasil.length === students.length`
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 3: empty/whitespace search restores full list`

  - [x] 7.5 Implementasikan fungsi `setupSearch()` yang memasang event listener `'input'` pada `#search-bar`
    - Setiap event `input` → panggil `filterStudents(studentsData, searchBar.value)` lalu `renderStudents(hasil)`
    - Tidak perlu debounce (33 item, filter sinkron < 300ms)
    - Tambahkan komentar JavaScript `// setupSearch: event listener real-time pada search bar; memfilter dan me-render ulang kartu siswa`
    - _Requirements: 6.2, 6.3, 6.5, 11.2, 11.3_

- [x] 8. Tab system — `setupTabs` untuk Kelas 10 / Kelas 11
  - [x] 8.1 Buat `<section id="kelas-10">` dan `<section id="kelas-11">` masing-masing berisi `#tab-system` dengan dua tombol tab dan dua panel konten
    - Tombol: `<button data-tab="kelas-10">Kelas 10</button>` dan `<button data-tab="kelas-11">Kelas 11</button>`
    - Panel Kelas 10 (`id="panel-kelas-10"`): tabel jadwal pelajaran placeholder, tabel jadwal piket placeholder, list struktur organisasi placeholder — setiap placeholder diberi komentar kode yang menjelaskan apa yang harus diisi
    - Panel Kelas 11 (`id="panel-kelas-11"`, class `hidden`): struktur sama dengan panel Kelas 10 + komentar placeholder
    - Warna aktif: Kelas 10 → warna teal, Kelas 11 → warna indigo; visual: `border-b-2` + warna tema pada tab aktif
    - Tambahkan komentar HTML `<!-- TAB INFORMASI KELAS: ganti konten placeholder di dalam panel-kelas-10 dan panel-kelas-11 -->`
    - _Requirements: 7.1, 7.2, 7.3, 7.4, 7.5, 7.6, 7.7, 11.1_

  - [x] 8.2 Implementasikan fungsi `setupTabs()` di blok `<script>`
    - Query semua `[data-tab]`; untuk setiap klik: jika `tombol.classList.contains('tab-aktif')` → return (idempoten); sinon → hapus `tab-aktif` dari semua tombol + tambah `hidden` ke semua panel → set `tab-aktif` pada tombol klik + hapus `hidden` dari panel target
    - Panggil `setupTabs()` di `DOMContentLoaded`, set tab "Kelas 10" aktif secara default
    - Tambahkan komentar JavaScript `// setupTabs: mengelola state tab aktif; klik tab yang sudah aktif tidak mengubah state apapun`
    - _Requirements: 7.2, 7.3, 7.4, 7.5, 7.8, 11.2, 11.3_

  - [x]* 8.3 Tulis property test untuk tab state exclusivity
    - **Property 5: Tab state exclusivity**
    - **Validates: Requirements 7.2, 7.3, 7.4, 7.5**
    - Gunakan `fc.constantFrom('kelas-10', 'kelas-11')` untuk `tabAwal` dan `tabKlik`; assert tepat 1 panel terlihat dan tepat 1 tab aktif setelah klik
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 5: tab state exclusivity`

  - [x]* 8.4 Tulis property test untuk tab idempotence
    - **Property 6: Tab idempotence**
    - **Validates: Requirements 7.8**
    - Klik tab yang sedang aktif → assert tidak ada perubahan state DOM (class, visibility)
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 6: tab idempotence`

- [x] 9. Checkpoint — pastikan direktori siswa dan tab system berfungsi
  - Buka `index.html` di browser: verifikasi 33 kartu siswa muncul di grid, search bar memfilter secara real-time, tab Kelas 10/11 beralih dengan benar, panel default adalah Kelas 10.
  - Pastikan tidak ada error konsol.

- [x] 10. Gallery section — dua album, hover effects, placeholder foto
  - [x] 10.1 Buat `<section id="galeri">` dengan navigasi dua album (tab/button) dan dua panel foto grid
    - Album tabs: `<button data-album="album-10">Album Cerita Kelas 10</button>` dan `<button data-album="album-11">Album Cerita Kelas 11</button>`
    - Panel `id="album-10"`: grid responsif (`grid-cols-2 md:grid-cols-3 lg:grid-cols-4`) berisi ≥ 6 item `<div class="foto-item">` placeholder; setiap item: `<img src="..." alt="keterangan foto (1-100 karakter)">` + `<div class="overlay"><p>keterangan</p></div>`
    - Panel `id="album-11"` (class `hidden`): struktur sama, ≥ 6 item placeholder
    - Tambahkan komentar HTML `<!-- GALERI: ganti src dan alt pada setiap img placeholder di album-10 dan album-11 -->`
    - _Requirements: 8.1, 8.2, 8.3, 8.5, 11.1_

  - [x] 10.2 Tambahkan CSS hover effects di blok `<style>` untuk `.foto-item` dan `.overlay`
    - `.foto-item { position: relative; overflow: hidden; }` dan `.overlay { opacity: 0; transition: opacity 200ms ease; }` dan `.foto-item:hover .overlay { opacity: 1; }` dan `.foto-item:hover img { transform: scale(1.05); transition: transform 300ms ease; }`
    - _Requirements: 8.4, 10.4_

  - [x] 10.3 Implementasikan fungsi `setupGallery()` untuk toggle antar album
    - Query semua `[data-album]`; logika toggle yang sama dengan `setupTabs()` tetapi untuk album panel
    - Pasang event listener klik pada setiap thumbnail foto → `openModal(src, alt)`
    - Tambahkan komentar JavaScript `// setupGallery: mengelola toggle album dan mendelegasikan klik thumbnail ke openModal`
    - _Requirements: 8.1, 8.6, 11.2, 11.3_

  - [ ]* 10.4 Tulis property test untuk gallery alt attribute non-empty
    - **Property 8: Gallery alt attribute non-empty**
    - **Validates: Requirements 8.5**
    - Query semua `img` di dalam `#galeri`; assert setiap `alt.length >= 1`
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 8: gallery alt attribute non-empty`

- [ ] 11. Modal / lightbox — `setupModal`, `openModal`, `tutupModal`
  - [x] 11.1 Buat HTML elemen modal di bawah `<section id="galeri">` sebelum `<footer>`
    - `<div id="modal-overlay" class="hidden fixed inset-0 bg-black/70 z-50 flex items-center justify-center">`
    - Di dalamnya: `<div id="modal-container" class="relative max-w-[90vw] max-h-[90vh]">` berisi tombol `<button id="tombol-tutup" aria-label="Tutup modal">×</button>` (min 44×44px), `<img id="modal-gambar">`, dan `<p id="modal-pesan-error" class="hidden">`
    - Tambahkan komentar HTML `<!-- MODAL: dikelola sepenuhnya oleh JavaScript; tidak perlu mengubah HTML ini -->`
    - _Requirements: 9.1, 9.2, 9.4, 9.8, 11.1_

  - [x] 11.2 Implementasikan fungsi `openModal(src, alt)` di blok `<script>`
    - Set `#modal-gambar` src dan alt → hapus class `hidden` dari `#modal-overlay` → `document.body.style.overflow = 'hidden'` → focus ke `#tombol-tutup`
    - Sembunyikan `#modal-pesan-error` dan tampilkan `#modal-gambar` (reset error state setiap kali modal dibuka)
    - Tambahkan komentar JavaScript `// openModal: membuka modal lightbox, mengunci scroll halaman, dan fokus ke tombol tutup untuk aksesibilitas`
    - _Requirements: 9.1, 9.6, 11.2, 11.3_

  - [x] 11.3 Implementasikan fungsi `tutupModal()` di blok `<script>`
    - Tambahkan class `hidden` ke `#modal-overlay` → `document.body.style.overflow = ''` → kosongkan `src` dari `#modal-gambar`
    - Tambahkan komentar JavaScript `// tutupModal: menutup modal, merestorasi scroll halaman, dan membersihkan src gambar`
    - _Requirements: 9.3, 9.5, 9.6, 11.2, 11.3_

  - [x] 11.4 Implementasikan fungsi `setupModal()` yang memasang semua event listener modal
    - `#tombol-tutup` → `tutupModal()`
    - Klik `#modal-overlay` (kecuali klik di dalam `#modal-container`) → `tutupModal()`
    - `document.addEventListener('keydown', e => { if (e.key === 'Escape' && !modalOverlay.classList.contains('hidden')) tutupModal(); })`
    - `#modal-gambar` onerror → sembunyikan `#modal-gambar`, tampilkan `#modal-pesan-error` dengan teks "Foto tidak dapat ditampilkan."
    - Tambahkan komentar JavaScript `// setupModal: memasang event listener untuk tutup modal via tombol, overlay, dan tombol Escape`
    - _Requirements: 9.3, 9.4, 9.7, 9.8, 11.2, 11.3_

  - [ ]* 11.5 Tulis property test untuk modal scroll lock round-trip
    - **Property 7: Modal scroll lock**
    - **Validates: Requirements 9.6**
    - Gunakan `fc.string({ minLength: 1 })` untuk src dan alt; assert `body.style.overflow === 'hidden'` setelah `openModal` dan `''` setelah `tutupModal`
    - Tambahkan tag `// Feature: journey-of-x-xi-website, Property 7: modal scroll lock round-trip`

- [ ] 12. Footer
  - [x] 12.1 Buat `<footer>` berisi teks hak cipta dan identitas kelas
    - Contoh: `© 2025 The Journey of X & XI MPLB. Semua kenangan tersimpan di sini.`
    - Pastikan footer tampil lengkap tanpa elemen terpotong di semua ukuran layar (gunakan `py-6 text-center w-full`)
    - Tambahkan komentar HTML `<!-- FOOTER: ganti teks hak cipta dan identitas kelas sesuai kebutuhan -->`
    - _Requirements: 10.6, 11.1_

- [ ] 13. Wiring semua fungsi ke `DOMContentLoaded`
  - [x] 13.1 Tambahkan blok `document.addEventListener('DOMContentLoaded', () => { ... })` di akhir blok `<script>` yang memanggil semua fungsi setup secara berurutan
    - Urutan: `validasiDataSiswa(studentsData)` → jika valid: `renderStudents(studentsData)` → `setupSearch()` → `setupTabs()` → `setupGallery()` → `setupModal()` → `setupNavMobile()`
    - Tambahkan komentar JavaScript `// DOMContentLoaded: entry point — memanggil validasi data dan semua fungsi setup setelah DOM siap`
    - _Requirements: 4.6, 5.1, 11.2, 11.3_

- [x] 14. Checkpoint akhir — verifikasi lengkap semua fitur
  - Buka `index.html` di browser (Chrome/Firefox/Edge/Safari): verifikasi seluruh fitur dari requirement 1–11 berfungsi tanpa error konsol.
  - Uji responsivitas di viewport 320px, 640px, 768px, 1024px, 1440px.
  - Simulasikan offline (DevTools → Network → Offline): verifikasi konten tetap terbaca meskipun tanpa Tailwind styling.
  - Pastikan semua tests pass, tanyakan kepada user jika ada pertanyaan.

- [x] 15. Setup property-based testing dengan fast-check
  - [x] 15.1 Buat file `__tests__/helpers.js` yang mengekstrak semua fungsi pure JavaScript dari `index.html` ke modul yang dapat di-import oleh test runner
    - Fungsi yang diekstrak: `potongKutipan`, `ambilInisial`, `filterStudents`, `renderStudentsToHTML` (versi pure tanpa DOM), `validasiDataSiswa`, `simulasiKlikTab`
    - Tambahkan `module.exports` untuk semua fungsi
    - _Requirements: 11.3_

  - [x] 15.2 Buat file `__tests__/properties.test.js` dengan semua 10 property-based test menggunakan fast-check
    - Import fungsi dari `helpers.js`
    - Implementasikan PBT 1–10 sesuai desain testing (Properties 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13)
    - Setiap test dikonfigurasi `{ numRuns: 100 }`
    - Setiap test diberi tag `// Feature: journey-of-x-xi-website, Property N: ...`
    - _Requirements: 4.2, 4.6, 5.1, 5.3, 5.4, 5.5, 6.2, 6.3, 6.4, 7.2, 7.5, 7.8, 8.5, 9.6_

  - [x] 15.3 Buat file `__tests__/functions.test.js` dengan unit tests
    - Test cases: kutipan panjang dipotong, kutipan pendek tidak dipotong, inisial dari nama lengkap, inisial nama kosong, render output berisi data siswa, scroll ke section tidak ada
    - _Requirements: 5.3, 5.5, 11.3_

  - [x] 15.4 Buat file `package.json` di root dengan konfigurasi minimal untuk menjalankan test
    - Dependensi: `vitest` (atau `jest`), `fast-check`, `jsdom`
    - Script: `"test": "vitest run"` (atau `"jest"`)
    - Tambahkan `"type": "module"` jika menggunakan Vitest
    - _Requirements: (testing infrastructure)_

---

## Notes

- Tasks bertanda `*` bersifat opsional dan dapat dilewati untuk MVP yang lebih cepat
- Setiap task mereferensikan requirements spesifik untuk traceabilitas
- Checkpoint di task 5, 9, dan 14 memastikan validasi inkremental
- Property tests memvalidasi properti universal; unit tests memvalidasi contoh spesifik dan edge case
- `index.html` adalah satu-satunya file output untuk produksi; file `__tests__/` hanya untuk development dan tidak perlu disertakan saat berbagi website
- Seluruh CSS kustom (modal transition, gallery hover, foto-placeholder) ditulis di satu blok `<style>` di `<head>`
- Seluruh JavaScript ditulis di satu blok `<script>` sebelum `</body>` sesuai requirement 11.4

---

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1.1"] },
    { "id": 1, "tasks": ["1.2", "2.1"] },
    { "id": 2, "tasks": ["2.2", "3.1"] },
    { "id": 3, "tasks": ["3.2", "4.1"] },
    { "id": 4, "tasks": ["4.2", "6.1"] },
    { "id": 5, "tasks": ["4.3", "6.2", "6.4", "6.6"] },
    { "id": 6, "tasks": ["6.3", "6.5", "6.7", "7.1"] },
    { "id": 7, "tasks": ["6.8", "6.9", "6.10", "6.11", "7.2", "8.1"] },
    { "id": 8, "tasks": ["7.3", "7.4", "7.5", "8.2"] },
    { "id": 9, "tasks": ["8.3", "8.4", "10.1"] },
    { "id": 10, "tasks": ["10.2", "10.3", "11.1"] },
    { "id": 11, "tasks": ["10.4", "11.2", "11.3"] },
    { "id": 12, "tasks": ["11.4", "12.1"] },
    { "id": 13, "tasks": ["11.5", "13.1"] },
    { "id": 14, "tasks": ["15.1"] },
    { "id": 15, "tasks": ["15.2", "15.3", "15.4"] }
  ]
}
```
