# Requirements Document

## Introduction

Website "The Journey of X & XI MPLB" adalah sebuah jurnal digital satu halaman (single-page) yang merekam perjalanan kelas dari Kelas 10 hingga Kelas 11 untuk total 33 siswa. Website ini dibangun menggunakan HTML, Tailwind CSS (via CDN), dan JavaScript murni (vanilla), dan dikemas dalam satu file `index.html`. Website berfungsi sebagai kenangan kolektif kelas yang interaktif dan responsif, menampilkan profil siswa, profil wali kelas, galeri foto, jadwal kelas, dan struktur organisasi.

## Glossary

- **Website**: Aplikasi web berbasis satu file `index.html` yang berjalan di browser.
- **Hero_Section**: Bagian paling atas halaman yang menampilkan judul, kalimat pengantar, dan tombol navigasi utama.
- **Student_Card**: Kartu visual yang menampilkan informasi seorang siswa termasuk dua foto, nama lengkap, nama panggilan, nomor absen, dan kutipan favorit.
- **Student_Directory**: Kumpulan 33 Student_Card yang ditampilkan dalam tata letak grid responsif.
- **Search_Bar**: Elemen input teks yang memfilter Student_Directory berdasarkan nama siswa secara real-time.
- **Teacher_Profile_Card**: Kartu yang menampilkan foto, nama, dan pesan singkat wali kelas.
- **Tab_System**: Komponen UI berbasis JavaScript yang menampilkan konten berbeda berdasarkan tab yang dipilih (Kelas 10 atau Kelas 11).
- **Gallery**: Komponen galeri foto yang dikelompokkan dalam dua album (Kelas 10 dan Kelas 11) dengan efek hover dan modal pop-up.
- **Modal**: Jendela overlay pop-up yang menampilkan foto dalam ukuran penuh ketika foto di galeri diklik.
- **Renderer**: Mekanisme JavaScript yang membaca array data siswa dan merender Student_Card ke dalam DOM.
- **Kelas_10_Theme**: Palet warna kalem/tenang yang digunakan untuk konten bertema Kelas 10.
- **Kelas_11_Theme**: Palet warna cerah/modern yang digunakan untuk konten bertema Kelas 11.

---

## Requirements

### Requirement 1: Struktur File Tunggal

**User Story:** Sebagai guru atau siswa yang ingin berbagi website kelas, saya ingin seluruh website terdapat dalam satu file HTML, agar file dapat dibagikan dan dibuka tanpa instalasi server.

#### Acceptance Criteria

1. THE Website SHALL terdiri dari satu file `index.html` yang memuat semua HTML, CSS (via Tailwind CDN), dan JavaScript secara inline atau embedded, tanpa referensi ke file eksternal lokal.
2. WHEN file `index.html` dibuka di browser modern (Chrome, Firefox, Edge, Safari) versi terbaru pada saat pengembangan, THE Website SHALL menampilkan seluruh konten tanpa error konsol dan tanpa pesan error yang terlihat di halaman.
3. THE Website SHALL memuat Tailwind CSS melalui CDN link `<script src="https://cdn.tailwindcss.com"></script>` tanpa memerlukan build tool.
4. IF CDN Tailwind CSS tidak dapat dijangkau (kondisi offline), THEN THE Website SHALL tetap menampilkan seluruh konten HTML dengan tata letak yang dapat dibaca, meskipun tanpa styling Tailwind.

---

### Requirement 2: Hero Section

**User Story:** Sebagai pengunjung website, saya ingin melihat judul dan pengantar kelas di bagian paling atas, agar saya langsung memahami tujuan website ini.

#### Acceptance Criteria

1. THE Hero_Section SHALL menampilkan judul utama "The Journey of X & XI MPLB" menggunakan elemen heading terbesar di halaman, dengan ukuran font minimal 32px dan hanya ada satu elemen heading terbesar tersebut di seluruh halaman.
2. THE Hero_Section SHALL menampilkan kalimat pengantar: "Berawal dari orang asing yang malu-malu di bangku Kelas 10, kini kita tumbuh bersama menjadi sebuah keluarga di Kelas 11. Ini adalah ruang digital tempat setiap tawa, cerita, dan ambisi kita terekam abadi." dengan teks yang seluruhnya terlihat tanpa interaksi pengguna.
3. THE Hero_Section SHALL menampilkan dua tombol bernama "Lihat Kenangan Kelas 10" dan "Lihat Aktivitas Kelas 11" yang keduanya tampak sekaligus dalam satu tampilan layar tanpa perlu di-scroll, dengan label teks persis seperti yang tertulis.
4. WHEN tombol "Lihat Kenangan Kelas 10" diklik, THE Website SHALL melakukan smooth scroll sehingga seksi konten Kelas 10 mencapai batas atas viewport dalam waktu tidak lebih dari 1 detik.
5. WHEN tombol "Lihat Aktivitas Kelas 11" diklik, THE Website SHALL melakukan smooth scroll sehingga seksi konten Kelas 11 mencapai batas atas viewport dalam waktu tidak lebih dari 1 detik.
6. THE Hero_Section SHALL menampilkan Kelas_10_Theme dan Kelas_11_Theme secara bersamaan sebagai latar belakang visual, di mana kedua warna tema diterapkan dalam satu elemen gradient yang mencakup seluruh area Hero_Section.
7. IF seksi konten Kelas 10 atau Kelas 11 tidak ditemukan di halaman, THEN THE Website SHALL menampilkan pesan kesalahan di konsol yang menginformasikan bahwa konten tidak tersedia dan tombol yang memicu aksi tersebut tidak melakukan scroll.

---

### Requirement 3: Profil Wali Kelas

**User Story:** Sebagai siswa yang mengunjungi website, saya ingin melihat profil kedua wali kelas dalam satu tampilan berdampingan, agar saya dapat mengenang peran mereka selama dua tahun.

#### Acceptance Criteria

1. THE Website SHALL menampilkan dua Teacher_Profile_Card secara berdampingan (side-by-side) dalam satu baris pada layar lebar (≥ 768px).
2. WHILE layar berukuran lebih kecil dari 768px, THE Website SHALL menampilkan kedua Teacher_Profile_Card secara vertikal (satu per baris).
3. THE Teacher_Profile_Card pertama SHALL memiliki label "Wali Kelas 10" dan berisi placeholder untuk: foto wali kelas, nama lengkap (maksimal 60 karakter), dan pesan singkat (maksimal 150 karakter).
4. THE Teacher_Profile_Card kedua SHALL memiliki label "Wali Kelas 11" dan berisi placeholder untuk: foto wali kelas, nama lengkap (maksimal 60 karakter), dan pesan singkat (maksimal 150 karakter).
5. IF foto wali kelas tidak tersedia, THEN THE Teacher_Profile_Card SHALL menampilkan avatar placeholder berupa inisial nama; IF nama juga tidak tersedia, THEN SHALL menampilkan ikon default.
6. THE Teacher_Profile_Card "Wali Kelas 10" SHALL menggunakan warna aksen dan tipografi heading dari Kelas_10_Theme pada seluruh elemen visual kartunya.
7. THE Teacher_Profile_Card "Wali Kelas 11" SHALL menggunakan warna aksen dan tipografi heading dari Kelas_11_Theme pada seluruh elemen visual kartunya.

---

### Requirement 4: Direktori Identitas Murid — Data

**User Story:** Sebagai pengelola website, saya ingin data 33 siswa didefinisikan dalam satu array JavaScript yang mudah diedit, agar pengganti data dapat dilakukan tanpa memahami DOM secara mendalam.

#### Acceptance Criteria

1. THE Website SHALL mendefinisikan data seluruh siswa dalam satu array JavaScript bernama `studentsData` yang dideklarasikan dengan `const` di dalam tag `<script>` pada file HTML utama.
2. THE `studentsData` SHALL memiliki tepat 33 elemen, masing-masing mewakili satu siswa dengan nilai properti `absen` bernomor unik 1 hingga 33 secara berurutan.
3. SETIAP elemen dalam `studentsData` SHALL memiliki tepat enam properti berikut: `absen` (number, rentang 1–33), `namaLengkap` (string, maksimal 100 karakter), `namaPanggilan` (string, maksimal 30 karakter), `fotoKelas10` (string, berisi URL atau path relatif, maksimal 300 karakter), `fotoKelas11` (string, berisi URL atau path relatif, maksimal 300 karakter), dan `kutipan` (string, maksimal 300 karakter).
4. THE `studentsData` SHALL diisi dengan data placeholder yang terstruktur untuk setiap 33 entri, di mana nilai placeholder mengikuti pola yang menyertakan nomor absen siswa (contoh: `namaLengkap` untuk absen 1 mengandung angka "1") sehingga setiap entri dapat dibedakan satu sama lain.
5. THE Website SHALL menyertakan komentar kode pada blok `<script>` yang memuat `studentsData`, menjelaskan cara mengganti nilai placeholder pada properti `namaLengkap`, `namaPanggilan`, `fotoKelas10`, `fotoKelas11`, dan `kutipan`, di mana komentar tersebut muncul tepat satu kali per properti pada elemen pertama (`absen: 1`) sebagai panduan representatif.
6. IF jumlah elemen dalam `studentsData` tidak sama dengan 33 saat halaman dimuat, THEN THE Website SHALL menampilkan pesan peringatan di konsol browser yang mengindikasikan ketidaksesuaian jumlah data siswa.

---

### Requirement 5: Direktori Identitas Murid — Tampilan Grid

**User Story:** Sebagai pengunjung website, saya ingin melihat semua 33 siswa dalam tampilan kartu yang rapi dan responsif, agar saya dapat menelusuri daftar kelas dengan nyaman di perangkat apapun.

#### Acceptance Criteria

1. WHEN halaman dimuat, THE Renderer SHALL merender tepat 33 Student_Card ke dalam elemen kontainer Student_Directory, dengan urutan kartu sesuai urutan nomor absen dari terkecil hingga terbesar.
2. THE Student_Directory SHALL menggunakan CSS Grid dengan tata letak: 1 kolom pada lebar layar < 640px, 2 kolom pada lebar layar 640px–1023px, 3 kolom pada lebar layar 1024px–1439px, dan 4 kolom pada lebar layar ≥ 1440px.
3. SETIAP Student_Card SHALL menampilkan nomor absen, nama lengkap, nama panggilan, foto atau placeholder Kelas 10, foto atau placeholder Kelas 11, dan kutipan favorit yang dipotong maksimal 150 karakter dengan penanda elipsis jika melebihi batas tersebut.
4. THE Student_Card SHALL menggunakan elemen `<img>` dengan atribut `alt` yang berisi nama lengkap siswa untuk aksesibilitas.
5. WHEN URL foto tidak tersedia atau gagal dimuat, THE Student_Card SHALL menampilkan gambar placeholder berwarna netral yang memuat inisial nama siswa (maksimal 2 karakter) di bagian tengah dengan ukuran area foto yang sama.
6. IF jumlah data siswa yang berhasil dimuat kurang dari 33, THEN THE Renderer SHALL menampilkan pesan kesalahan yang mengindikasikan data tidak lengkap dan tidak merender Student_Directory.

---

### Requirement 6: Direktori Identitas Murid — Fitur Pencarian

**User Story:** Sebagai pengunjung website, saya ingin mengetik nama siswa di kolom pencarian untuk langsung menemukan kartu mereka, agar saya tidak perlu menggulir seluruh daftar 33 siswa.

#### Acceptance Criteria

1. THE Website SHALL menampilkan satu Search_Bar di atas Student_Directory dengan placeholder teks "Cari nama siswa...".
2. WHEN pengguna mengetik di Search_Bar, THE Renderer SHALL memfilter dan menampilkan hanya Student_Card yang nama lengkap atau nama panggilannya mengandung teks yang diketik (pencarian tidak peka huruf besar/kecil) dalam waktu ≤ 300ms setelah karakter terakhir diketik.
3. WHEN input di Search_Bar dikosongkan atau hanya berisi spasi, THE Renderer SHALL menampilkan kembali seluruh 33 Student_Card.
4. WHEN tidak ada Student_Card yang cocok dengan teks pencarian, THE Student_Directory SHALL menampilkan pesan "Siswa tidak ditemukan."
5. WHEN pengguna mengubah isi Search_Bar, THE Renderer SHALL memperbarui daftar Student_Card yang tampil secara otomatis tanpa memerlukan tombol submit.

---

### Requirement 7: Tab Informasi Kelas

**User Story:** Sebagai siswa, saya ingin beralih antara informasi Kelas 10 dan Kelas 11 dengan satu klik, agar saya dapat melihat jadwal dan organisasi dari kedua angkatan tanpa berpindah halaman.

#### Acceptance Criteria

1. THE Tab_System SHALL menampilkan dua tombol tab: "Kelas 10" dan "Kelas 11".
2. WHEN halaman pertama kali dimuat, THE Tab_System SHALL menampilkan konten "Kelas 10" sebagai tab aktif secara default.
3. WHEN tab "Kelas 10" dipilih, THE Tab_System SHALL menampilkan: jadwal pelajaran Kelas 10, jadwal piket Kelas 10, dan struktur organisasi kelas masa Kelas 10.
4. WHEN tab "Kelas 11" dipilih, THE Tab_System SHALL menampilkan: jadwal pelajaran Kelas 11, jadwal piket Kelas 11, dan struktur organisasi kelas masa Kelas 11.
5. WHILE satu tab aktif, THE Tab_System SHALL menampilkan perbedaan visual yang terukur antara tab aktif dan tab tidak aktif, seperti warna latar, ketebalan border, atau ukuran teks yang berbeda.
6. THE konten setiap tab SHALL berisi data placeholder di mana setiap placeholder diberi komentar kode yang menjelaskan konten apa yang harus diisi oleh pengguna.
7. THE Tab_System SHALL menggunakan gaya visual berbeda yang konsisten dengan Kelas_10_Theme untuk tab Kelas 10 dan Kelas_11_Theme untuk tab Kelas 11 sebagai indikator aktif.
8. WHEN tab yang sedang aktif diklik kembali, THE Tab_System SHALL tidak mengubah state apapun dan konten yang tampil SHALL tetap sama.

---

### Requirement 8: Galeri Kenangan — Album dan Tampilan

**User Story:** Sebagai siswa, saya ingin melihat koleksi foto kelas yang terbagi per angkatan dalam tampilan galeri yang menarik, agar saya dapat mengenang momen-momen penting bersama.

#### Acceptance Criteria

1. THE Gallery SHALL menampilkan dua album terpisah: "Album Cerita Kelas 10" dan "Album Cerita Kelas 11", masing-masing dapat diakses melalui tab atau tombol navigasi yang terpisah.
2. THE Gallery SHALL menampilkan thumbnail foto dalam tata letak grid responsif dengan minimum 2 kolom pada lebar layar kurang dari 768px dan maksimum 4 kolom pada lebar layar 1024px ke atas.
3. THE Gallery SHALL berisi minimal 6 placeholder foto per album (total minimum 12 placeholder foto), di mana setiap placeholder memiliki teks keterangan yang terdiri dari minimal 1 karakter dan maksimal 100 karakter.
4. WHEN kursor diarahkan ke thumbnail foto (hover), THE Gallery SHALL menampilkan perubahan visual yang terukur pada elemen tersebut berupa overlay dengan opacity lebih dari 0, atau perubahan skala (scale) yang berbeda dari nilai awal.
5. THE Gallery SHALL memastikan setiap elemen foto memiliki atribut `alt` yang tidak kosong (panjang minimal 1 karakter).
6. WHEN pengguna mengklik thumbnail foto, THE Gallery SHALL menampilkan foto tersebut dalam ukuran yang lebih besar di atas konten halaman (modal/lightbox).

---

### Requirement 9: Galeri Kenangan — Modal Pop-up

**User Story:** Sebagai pengunjung, saya ingin melihat foto dalam ukuran besar ketika diklik, agar detail foto terlihat jelas tanpa meninggalkan halaman.

#### Acceptance Criteria

1. WHEN thumbnail foto di Gallery diklik, THE Modal SHALL muncul dalam waktu ≤ 300ms dan menampilkan foto yang diklik dengan lebar maksimum 90% dari lebar viewport dan tinggi maksimum 90% dari tinggi viewport, diposisikan di tengah layar secara horizontal dan vertikal.
2. WHILE Modal terbuka, THE Website SHALL menampilkan overlay dengan opacity ≥ 0.5 di belakang Modal yang memblokir seluruh interaksi pointer (klik, tap, hover) dengan konten di bawahnya.
3. WHEN area overlay di luar batas Modal diklik, THE Modal SHALL tertutup dan overlay SHALL menghilang dalam waktu ≤ 300ms.
4. THE Modal SHALL menampilkan tombol "×" (tutup) berukuran minimum 44×44 piksel yang selalu terlihat di area Modal, dapat diklik untuk menutup Modal.
5. WHEN tombol "×" atau area overlay diklik untuk menutup Modal, THE Modal SHALL menghilang dengan transisi animasi durasi antara 150ms hingga 300ms dan overlay SHALL menghilang dalam rentang durasi yang sama.
6. WHEN Modal terbuka, THE Website SHALL mencegah scroll halaman di belakang Modal sehingga posisi scroll halaman tidak berubah selama Modal ditampilkan.
7. WHEN tombol keyboard Escape ditekan selama Modal terbuka, THE Modal SHALL tertutup dan overlay SHALL menghilang dalam waktu ≤ 300ms.
8. IF foto gagal dimuat di dalam Modal, THEN THE Modal SHALL menampilkan pesan error yang mengindikasikan foto tidak dapat ditampilkan dan tombol "×" tetap tersedia untuk menutup Modal.

---

### Requirement 10: Desain Responsif dan Tema Warna

**User Story:** Sebagai pengguna yang mengakses dari smartphone, tablet, atau laptop, saya ingin tampilan website selalu rapi dan terbaca dengan baik, agar pengalaman menengok kenangan kelas tetap menyenangkan di perangkat apapun.

#### Acceptance Criteria

1. THE Website SHALL menggunakan pendekatan mobile-first di mana layout default (tanpa breakpoint prefix) diterapkan untuk layar dengan lebar di bawah 640px, dan breakpoint Tailwind CSS `sm` (640px), `md` (768px), `lg` (1024px), `xl` (1280px) digunakan untuk penyesuaian bertahap pada layar yang lebih lebar.
2. THE Kelas_10_Theme SHALL mendefinisikan palet warna kalem/tenang yang diterapkan secara konsisten pada elemen background, primary text, secondary text, dan interactive accent dari semua komponen bertema Kelas 10.
3. THE Kelas_11_Theme SHALL mendefinisikan palet warna cerah/modern yang diterapkan secara konsisten pada elemen background, primary text, secondary text, dan interactive accent dari semua komponen bertema Kelas 11.
4. THE Website SHALL menggunakan Tailwind CSS utility classes sebagai mekanisme styling utama; CSS kustom diperbolehkan hanya untuk efek yang tidak tersedia melalui Tailwind utility classes yang ada.
5. WHEN pengguna memilih item navigasi, THE Website SHALL melakukan smooth scroll ke seksi yang dituju, dan header navigasi SHALL bersifat sticky (tetap terlihat di atas viewport) pada semua ukuran layar.
6. THE Website SHALL menampilkan footer yang memuat teks hak cipta dan identitas kelas, dan footer tersebut SHALL tampil lengkap tanpa elemen yang terpotong pada semua ukuran layar yang didukung.
7. WHEN lebar layar kurang dari 640px, THE Website SHALL menampilkan menu navigasi dalam format yang tidak menyebabkan horizontal scroll, seperti menu hamburger atau navigasi vertikal.

---

### Requirement 11: Keterbacaan Kode dan Komentar

**User Story:** Sebagai siswa yang ingin mempersonalisasi website ini, saya ingin kode HTML dan JavaScript diberi komentar yang jelas, agar saya dapat menemukan dan mengganti data seperti nama siswa dan foto tanpa bingung.

#### Acceptance Criteria

1. THE Website SHALL menyertakan komentar HTML (`<!-- -->`) di awal setiap seksi utama (Hero, Profil Wali Kelas, Direktori Siswa, Tab Informasi, Galeri, Footer) yang menjelaskan fungsi seksi tersebut dan menyebutkan elemen data apa yang dapat diganti oleh siswa.
2. THE Website SHALL menyertakan komentar JavaScript (`//` atau `/* */`) pada array `studentsData`, fungsi render, fungsi pencarian, Tab_System, dan Modal, di mana setiap komentar menjelaskan tujuan blok kode dan langkah-langkah yang harus dilakukan siswa untuk mengganti data terkait.
3. THE Website SHALL menggunakan nama variabel dan fungsi JavaScript yang terdiri dari minimal 3 karakter, bersifat deskriptif dalam Bahasa Indonesia atau bahasa Inggris, dan tidak menggunakan pola singkatan tidak bermakna seperti huruf tunggal, kombinasi huruf-angka acak (`fn1`, `x2`, `a1`), atau akronim yang tidak dijelaskan.
4. THE Website SHALL memiliki struktur kode yang konsisten dalam satu file HTML: elemen HTML terlebih dahulu, diikuti tepat satu tag `<style>` untuk CSS kustom (jika ada) yang ditempatkan di dalam `<head>`, diikuti tepat satu tag `<script>` yang ditempatkan sebagai elemen terakhir sebelum penutup tag `</body>`.

