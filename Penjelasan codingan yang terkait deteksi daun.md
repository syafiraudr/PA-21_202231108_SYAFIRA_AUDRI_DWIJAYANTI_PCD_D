
### Penjelasan Codingan Deteksi Daun
import cv2
/import numpy as np/
import matplotlib.pyplot as plt// codingan ini digunakan untuk mengimport library//

//pnp = 'pnp.jpg'//membuat variabel dengan nama pnp//

#### Membaca gambar dan konversi ke RGB
img_ori1 = cv2.imread(pnp)/
img_ori2 = cv2.cvtColor(img_ori1, cv2.COLOR_BGR2RGB)//Kode tersebut digunakan untuk membaca sebuah gambar dan mengonversi ruang warnanya dari BGR (Biru, Hijau, Merah) menjadi RGB (Merah, Hijau, Biru).Alasan untuk mengonversi ruang warna ini adalah bahwa banyak algoritma komputer visi dan library, termasuk OpenCV, menggunakan RGB sebagai ruang warna default. Konversi ini menjamin bahwa gambar dalam ruang warna yang benar untuk proses selanjutnya.//
#### Menentukan rentang warna untuk daun dan buah dalam RGB
lower_green = np.array([25, 40, 10])
upper_green = np.array([90, 255, 90])
//Baris ini mendefinisikan batas bawah dan batas atas untuk rentang warna hijau. Nilai-nilai tersebut dalam ruang warna HSV (Warna, Kecerahan, Nilai). Batas bawah ditetapkan untuk warna hijau dengan kecerahan rendah dan nilai rendah, sementara batas atas ditetapkan untuk warna hijau dengan kecerahan tinggi dan nilai tinggi. Rentang ini akan menangkap sebagian besar warna hijau dalam sebuah gambar.//
lower_yellow = np.array([150, 120, 0])
upper_yellow = np.array([255, 255, 80])
//Baris ini mendefinisikan batas bawah dan batas atas untuk rentang warna kuning. Nilai-nilai tersebut juga dalam ruang warna HSV. Batas bawah ditetapkan untuk warna kuning dengan kecerahan rendah dan nilai rendah, sementara batas atas ditetapkan untuk warna kuning dengan kecerahan tinggi dan nilai tinggi. Rentang ini akan menangkap sebagian besar warna kuning dalam sebuah gambar.//
#### Buat mask untuk daun (hijau) dan buah (kuning)
mask_daun = cv2.inRange(img_ori1, lower_green, upper_green)// Baris ini membuat masker untuk objek hijau dalam citra img_ori1. Fungsi inRange dari OpenCV digunakan untuk menentukan nilai piksel dalam citra yang berada dalam rentang warna hijau yang telah ditentukan (dengan lower_green dan upper_green).//
mask_buah = cv2.inRange(img_ori2, lower_yellow, upper_yellow)//Baris ini membuat masker untuk objek kuning dalam citra img_ori2. Fungsi inRange dari OpenCV digunakan untuk menentukan nilai piksel dalam citra yang berada dalam rentang warna kuning yang telah ditentukan (dengan lower_yellow dan upper_yellow).//
mask_daun = cv2.morphologyEx(mask_daun, cv2.MORPH_OPEN, kernel)//baris ini melakukan operasi morfologi terhadap masker mask_daun dengan menggunakan fungsi morphologyEx dari OpenCV. Parameter MORPH_OPEN menunjukkan bahwa operasi yang dilakukan adalah penutupan (opening), yang menghilangkan bagian-bagian kecil dari masker yang tidak diinginkan. Parameter kernel menentukan ukuran dan bentuk dari jendela yang digunakan dalam operasi ini.//
mask_buah = cv2.morphologyEx(mask_buah, cv2.MORPH_OPEN, kernel)//Baris ini melakukan operasi morfologi terhadap masker mask_buah dengan menggunakan fungsi morphologyEx dari OpenCV. Parameter MORPH_OPEN dan kernel memiliki arti yang sama seperti pada baris sebelumnya.//
### 
contours_daun, _ = cv2.findContours(mask_daun, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)//Baris ini mendeteksi kontur pada citra masker mask_daun. Fungsi findContours dari OpenCV digunakan untuk tujuan ini. Parameter RETR_EXTERNAL menunjukkan bahwa hanya kontur eksternal yang akan dideteksi, sedangkan CHAIN_APPROX_SIMPLE menunjukkan bahwa kontur akan ditekan menjadi bentuk sederhana.//
contours_buah, _ = cv2.findContours(mask_buah, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)//Baris ini mendeteksi kontur pada citra masker mask_buah. Fungsi findContours dari OpenCV digunakan untuk tujuan ini. Parameter RETR_EXTERNAL dan CHAIN_APPROX_SIMPLE memiliki arti yang sama seperti pada baris sebelumnya.//

mask_segmented_daun = np.zeros_like(img_ori1)
mask_segmented_buah = np.zeros_like(img_ori2)//Baris ini mendefinisikan dua variabel, mask_segmented_daun dan mask_segmented_buah, sebagai array nol dengan ukuran yang sama dengan citra asli img_ori1 dan img_ori2. Array ini akan digunakan untuk menyimpan hasil segmentasi.//
cv2.drawContours(mask_segmented_daun, contours_daun, -1, (0, 255, 0), thickness=cv2.FILLED)// Baris ini menarik kontur-kontur yang telah dideteksi pada citra masker mask_segmented_daun dengan warna hitam (0, 0, 0). Parameter -1 menunjukkan bahwa semua kontur akan ditarik, sedangkan thickness=cv2.FILLED menunjukkan bahwa kontur akan ditarik dengan ketebalan penuh.//
cv2.drawContours(mask_segmented_buah, contours_buah, -1, (255, 255, 0), thickness=cv2.FILLED)//Baris ini menarik kontur-kontur yang telah dideteksi pada citra masker mask_segmented_buah dengan warna putih (255, 255, 0). Parameter -1 menunjukkan bahwa semua kontur akan ditarik, sedangkan thickness=cv2.FILLED menunjukkan bahwa kontur akan ditarik dengan ketebalan penuh.//
segmented_daun = cv2.bitwise_and(img_ori1, mask_segmented_daun)
segmented_buah = cv2.bitwise_and(img_ori2, mask_segmented_buah)
//Kode ini digunakan untuk menghasilkan citra yang tersegmentasi, di mana objek tertentu (misalnya daun dan buah) dapat dipisahkan dari latar belakang.//

#### Menampilkan Output
fig, axs = plt.subplots(2, 2, figsize=(10, 10))

plt.subplot(2, 2, 1)
axs[0, 0].imshow(img_ori2)
axs[0, 0].set_title('Gambar Asli')

plt.subplot(2, 2, 2)
axs[0, 1].imshow(cv2.cvtColor(mask_buah, cv2.COLOR_GRAY2RGB))
axs[0, 1].set_title('Mask nanas')

plt.subplot(2, 2, 3)
axs[1, 0].imshow(segmented_buah)
axs[1, 0].set_title('Segmentasi nanas')

plt.subplot(2, 2, 2)
axs[1, 1].imshow(segmented_daun)
axs[1, 1].set_title('Segmentasi Daun')

plt.tight_layout()
plt.show()
//Kode ini digunakan untuk menampilkan citra asli, masker, dan hasil segmentasi dalam satu plot.//
