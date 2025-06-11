UAS_PCD_23E_KEL11

Anggota:
- Shafira Nabila Noer Poerwanto (23031554011)
- Ratu Arum Rahma Gati (23031554142)
- Chiara Dewi Chatlina (23031554170)


Proyek Identifikasi Iris: Analisis Perbandingan Metode dan Optimasi
Proyek ini merupakan implementasi dan analisis sistem identifikasi iris mata dari awal menggunakan dataset MMU-Iris-Database. Tujuan utamanya adalah untuk membangun sebuah pipeline yang andal, membandingkan beberapa pendekatan populer, dan mengidentifikasi batasan kinerja dari metode-metode tersebut.

Perjalanan riset ini melibatkan eksplorasi dari metode ekstraksi fitur klasik seperti Gabor/LBP hingga pendekatan yang lebih modern menggunakan Normalisasi Citra dan Pencocokan Template Biner. Kesimpulan akhir dari proyek ini menyoroti pentingnya tahap segmentasi sebagai faktor paling krusial dalam menentukan akurasi sistem secara keseluruhan.

Alur Kerja Pipeline
Sistem ini bekerja melalui serangkaian langkah yang terdefinisi dengan baik untuk memproses citra iris dan melakukan pencocokan.

Input Citra Mata: Mengambil gambar dari dataset.
Pra-pemrosesan: Citra diubah ke format grayscale, lalu MedianBlur dan CLAHE diterapkan untuk mengurangi noise dan meningkatkan kontras.
Segmentasi Iris: Circular Hough Transform digunakan untuk mendeteksi dan melokalisasi batas pupil (lingkaran dalam) dan batas iris (lingkaran luar).
Normalisasi Iris: Area cincin iris yang telah ditemukan kemudian "dibuka" menjadi sebuah citra persegi panjang berukuran tetap (64x360 piksel) menggunakan Daugman's Rubber Sheet Model. Langkah ini krusial untuk mengatasi variasi ukuran dan rotasi mata.
Pembuatan Template Biner (IrisCode): Citra persegi panjang yang sudah dinormalisasi diubah menjadi template biner (hanya berisi nilai 0 dan 1) menggunakan Adaptive Thresholding. Template inilah yang menjadi representasi unik dari setiap iris.
Pencocokan (Matching): Untuk membandingkan dua iris, Jarak Hamming dihitung antara dua IrisCode. Metode ini juga menangani rotasi dengan menggeser salah satu template beberapa piksel dan mencari skor kecocokan terendah.
Evaluasi: Akurasi sistem diukur dengan membandingkan hasil prediksi dengan label sebenarnya pada data uji.
Struktur Kode
Kode ini disusun dalam beberapa fungsi inti dan sebuah script utama untuk menjalankan keseluruhan proses.

get_iris_template_optimized(image_path): Ini adalah fungsi utama yang mengorkestrasi alur kerja untuk satu gambar, mulai dari pembacaan, segmentasi, normalisasi, hingga pembuatan template biner.
normalize_iris(...): Fungsi khusus yang bertugas mengimplementasikan Daugman's Rubber Sheet Model untuk mengubah ROI cincin iris menjadi persegi panjang.
calculate_hamming_distance(...): Fungsi untuk membandingkan dua IrisCode. Fungsi ini menghitung Jarak Hamming terbaik dengan toleransi rotasi.
Blok Utama (Script):
Ekstraksi: Melakukan loop pada seluruh dataset, memanggil get_iris_template_optimized untuk setiap gambar, dan menyimpan hasilnya.
Persiapan Data: Mengurutkan dan memfilter data untuk memastikan hasil yang konsisten, lalu membaginya menjadi set training (galeri) dan testing menggunakan train_test_split.
Pencarian Akurasi Terbaik: Kode ini secara unik berisi sebuah loop yang mencoba hingga 150 nilai random_state yang berbeda untuk menemukan pembagian data yang menghasilkan akurasi tertinggi, lalu melaporkan hasil terbaik tersebut.
Metodologi Kunci
Normalisasi Iris
Teknik ini adalah kunci untuk membuat sistem menjadi robust terhadap variasi geometris. Dengan mengubah area iris yang melingkar menjadi sebuah blok persegi panjang yang ukurannya selalu sama, perbandingan fitur menjadi jauh lebih adil dan konsisten, tidak terpengaruh oleh jarak subjek ke kamera.

Jarak Hamming dan Toleransi Rotasi
Setelah iris dinormalisasi, perbandingan tidak lagi menggunakan jarak geometris seperti Euclidean. Jarak Hamming adalah metode yang sangat efisien untuk mengukur perbedaan antara dua set data biner.

Jarak = Jumlah bit yang berbeda / Total bit
Rotasi mata pada dunia nyata akan muncul sebagai pergeseran horizontal pada citra yang ternormalisasi. Dengan menggeser (np.roll) salah satu template saat membandingkan, kita dapat menemukan kecocokan terbaik meskipun ada sedikit rotasi pada mata.

Tantangan dan Penemuan
Perjalanan proyek ini mengungkap beberapa temuan penting:

Baseline Awal: Pendekatan awal menggunakan fitur Gabor/LBP pada citra mentah menghasilkan akurasi yang menjanjikan (~40%), namun sulit untuk ditingkatkan lebih jauh.
Keunggulan Normalisasi: Pipeline yang menggunakan Normalisasi + Template Matching terbukti lebih unggul, berhasil mencapai akurasi puncak ~44% pada salah satu pembagian data.
Bottleneck Utama Teridentifikasi: Eksperimen berulang kali membuktikan bahwa Circular Hough Transform adalah titik terlemah dalam sistem. Algoritma ini sering gagal mendeteksi lingkaran iris secara akurat, terutama pada gambar dengan oklusi kelopak mata atau kualitas rendah. Kegagalan segmentasi ini membatasi potensi dari algoritma pencocokan yang lebih canggih.
Cara Menjalankan Kode
Prasyarat: Pastikan library Python berikut terpasang: opencv-python, numpy, pandas, scikit-learn, scikit-image.
Struktur Folder: Letakkan file dataset_PCD.zip di dalam direktori Google Drive Anda pada path: /content/drive/MyDrive/.
Eksekusi: Jalankan seluruh sel kode pada lingkungan seperti Google Colab. Script akan melakukan seluruh proses secara otomatis.
Output: Script akan melakukan pencarian untuk random_state terbaik dan pada akhirnya akan mencetak Akurasi Puncak yang berhasil ditemukan beserta nilai random_state yang menghasilkannya, lengkap dengan Classification Report.
Hasil Akhir
Akurasi puncak yang berhasil dicapai oleh pipeline tercanggih dalam proyek ini adalah ~44%. Hasil ini didapatkan menggunakan metode Normalisasi Iris dan Template Matching, dengan random_state spesifik yang ditemukan melalui proses pencarian.

Kesimpulan akhir dari riset ini adalah bahwa untuk melampaui batas akurasi ini, pengembangan di masa depan harus berfokus pada penggantian metode segmentasi HoughCircles dengan pendekatan yang lebih modern dan andal seperti model Deep Learning U-Net.
