# JEN LMS-Quiz

JEN LMS-Quiz adalah platform pembelajaran dan kuis berbasis web dengan 12 mata pelajaran, kuis mandiri, room multiplayer, leaderboard per mata pelajaran, serta modul materi PDF.

## Teknologi
- HTML, CSS, dan JavaScript
- Node.js + Express
- Socket.IO untuk room multiplayer
- Penyimpanan JSON lokal di server untuk profil pengguna, definisi room, dan seluruh riwayat percobaan kuis

## Fitur utama
1. **Kuis mandiri** — Player maupun Host dapat memulai kuis dari setiap mata pelajaran tanpa menunggu Host membuka sesi. Bank soal tetap 10 soal per mata pelajaran dan timer tetap 15 detik per soal.
2. **Room multiplayer** — Host dapat membuka room bawaan atau membuat room tambahan. Room tambahan diberi kode unik dan tersimpan di database server. Room baru memilih salah satu dari 12 bank soal yang tersedia.
3. **Penyimpanan pemain** — Data login nama, peran, avatar, dan ID pengguna disimpan di server.
4. **Leaderboard per mata pelajaran** — Setiap percobaan kuis mandiri maupun multiplayer disimpan. Leaderboard menampilkan nilai terbaik setiap pemain pada mata pelajaran tersebut, sedangkan seluruh riwayat percobaan tetap tersimpan.
5. **Materi PDF** — Tersedia 12 modul PDF yang tetap dapat dibuka melalui menu Materi Pembelajaran.

## Menjalankan proyek secara lokal
1. Pastikan Node.js versi LTS sudah terpasang.
2. Buka terminal di folder proyek.
3. Jalankan `npm install`.
4. Jalankan `npm test` untuk validasi bank soal, pemeriksaan sintaks, dan smoke test integrasi API/Socket.IO.
5. Jalankan `npm start`.
6. Buka `http://localhost:3000`.

Jangan membuka proyek melalui `file://` atau Live Server port 5500. Login, room, Socket.IO, dan penyimpanan data menggunakan server Node.js.

## Cara menggunakan kuis mandiri
1. Masuk sebagai Mahasiswa/Player atau Pengajar/Host.
2. Pada daftar room, pilih mata pelajaran.
3. Klik **Mulai Mandiri**.
4. Jawab 10 soal dengan timer 15 detik per soal.
5. Setelah selesai, nilai dikirim ke server dan ditampilkan pada leaderboard mata pelajaran.

## Cara Host membuat room tambahan
1. Masuk sebagai Pengajar/Host.
2. Pada halaman Daftar Room, isi nama room.
3. Pilih salah satu mata pelajaran yang tersedia.
4. Klik **Simpan Room**. Server membuat kode room unik.
5. Room baru muncul pada daftar dan tetap tersedia setelah server dijalankan ulang.
6. Klik **Buka Room Host** untuk memulai sesi multiplayer. Aturan multiplayer tetap minimal 5 peserta termasuk Host dan maksimal 10 peserta termasuk Host.

Room tambahan menggunakan bank soal dari mata pelajaran yang dipilih. Fitur ini belum membuat bank soal baru secara otomatis.

## Penyimpanan data
Data aplikasi disimpan di file JSON server, dengan lokasi default:

`data/jen-data.json`

Isi data meliputi:
- Profil pengguna yang pernah login (`users`).
- 12 room bawaan dan room tambahan yang dibuat Host (`rooms`).
- Seluruh percobaan kuis beserta pemain, mata pelajaran, nilai, mode, kode room, dan waktu pengerjaan (`attempts`).

Leaderboard menampilkan **nilai terbaik setiap pemain per mata pelajaran**. Riwayat percobaan yang lebih rendah tidak dihapus; tetap ada dalam `attempts`.

### Penting untuk deployment Render
Filesystem Render secara default bersifat sementara. File JSON baru bertahan setelah restart/redeploy jika ditulis di bawah mount path **Persistent Disk**. Menurut dokumentasi Render saat ini, Persistent Disk untuk Web Service hanya tersedia pada paket berbayar; paket Free tidak mendukung disk tersebut. Disk juga membatasi layanan ke satu instance dan meniadakan zero-downtime deploy. Lihat [dokumentasi Persistent Disks Render](https://render.com/docs/disks) dan [dokumentasi Web Services](https://render.com/docs/web-services).

Untuk layanan yang mendukung Persistent Disk, buat disk dari Dashboard Render dengan mount path `/var/data`, lalu tambahkan environment variable:

`JEN_DATA_FILE=/var/data/jen-data.json`

Build command: `npm install`  
Start command: `npm start`  
Health check path (opsional): `/health`

Jika menggunakan Render Free tanpa Persistent Disk, file JSON dapat hilang ketika instance restart/redeploy. Untuk deployment Free yang tetap membutuhkan penyimpanan permanen, ganti penyimpanan JSON lokal dengan database terkelola seperti Firebase/Firestore atau PostgreSQL. Jangan menganggap file lokal di luar mount path sebagai penyimpanan permanen. Simpan backup database secara berkala.

## Catatan akun dan keamanan
Login saat ini menggunakan nama, peran, avatar, dan ID acak berbasis browser; belum menggunakan password atau autentikasi email. Pada browser yang sama, ID dipisahkan menurut nama dan peran. Karena belum ada autentikasi, dua orang yang menggunakan nama dan peran sama pada browser yang sama dapat memakai identitas yang sama. Untuk deployment publik dengan banyak pengguna, integrasikan autentikasi yang sesungguhnya.

Skor tetap dihitung di browser, lalu server memvalidasi identitas terdaftar, mata pelajaran, dan rentang skor 0–1.500 sebelum menyimpan percobaan. Ini belum merupakan sistem penilaian anti-kecurangan. Untuk penggunaan publik/kompetitif, pindahkan kunci jawaban dan perhitungan skor ke backend tepercaya.

## Pengujian
Jalankan:

`npm test`

Pengujian mencakup:
- Validasi 12 mata pelajaran, 120 soal, file PDF, serta aset.
- Validasi fitur kuis mandiri, room dinamis, dan endpoint penyimpanan.
- Smoke test login pengguna, pembuatan room Host, join Socket.IO, start multiplayer, penyimpanan nilai, validasi skor, leaderboard per mata pelajaran, dan persistensi setelah server restart.

Pengujian otomatis tidak menggantikan pengujian manual seluruh tampilan di browser dan UAT.
# jen-lms-quiz
