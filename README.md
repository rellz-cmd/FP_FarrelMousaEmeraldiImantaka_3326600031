# Final Project Oprec PENSHIP - Farrel Mousa Emeraldi Imantaka (3326600031)

## Identitas
- Nama: Farrel Mousa Emeraldi Imantaka
- NRP: 3326600031
- Program studi: Sains Data Terapan

## Project Roboflow
- Project ID Roboflow: rellz1207s-workspace/penship-object-detetction, version 2
- Link Roboflow Universe: https://universe.roboflow.com/rellz1207s-workspace/penship-object-detetction

## Dataset
- Jumlah foto asli: bottle 1, laptop 1, pencil 1 (Total: 3 foto)
- Metode pengambilan foto: Menggunakan kamera HP dengan berbagai variasi jarak (dekat dan jauh), sudut pandang, serta pencahayaan dan latar belakang yang berbeda.
- Fitur AI labeling Roboflow yang digunakan (apabila ada) dan cara perapiannya: Manual Bounding box

## Training
- Lingkungan training: laptop lokal (GPU RTX 4060)
- Model: yolov8n
- Grafik: ![Grafik Hasil Training](runs/detect/train-4/results.png)

## Pengembangan Kode
Daftar perubahan terhadap kode dasar, beserta alasan dan pengaruhnya terhadap hasil:
1. Melengkapi konfigurasi dataset API: Menambahkan parameter `ROBOFLOW_WORKSPACE`, `ROBOFLOW_PROJECT`, dan mengubah `ROBOFLOW_VERSION = 2` pada file `train.py`. Tujuannya agar program bisa langsung mengunduh dataset secara otomatis dari Roboflow tanpa perlu pindah-pindah file manual.
2. Autentikasi keamanan: Mengatur environment variable `$env:ROBOFLOW_API_KEY` secara manual di terminal PowerShell sebelum menjalankan kode, supaya program mendapat izin (otorisasi) untuk mengunduh dataset.
3. Penyelesaian Error Validasi: Sempat memodifikasi rute validasi pada `data.yaml` menjadi `val: ../train/images` karena dataset yang terlalu kecil membuat persentase validasi menjadi 0% di Roboflow. Namun, kode telah dikembalikan ke `val: ../valid/images` setelah melakukan rebalance split manual di Roboflow (67% Train, 33% Valid) dan menghapus folder lokal lama agar sistem mengunduh struktur dataset yang utuh.

## Hasil Eksperimen
| No | Skenario | Epochs | Batch | Optimizer | mAP50 | mAP50-95 | Keterangan |
|:--:|:---|:---:|:---:|:---:|:---:|:---:|:---|
| 0 | Baseline (kode dasar) | 50 | 16 | auto | 0.995 | 0.995 | Training final menggunakan 3 foto dataset |

## Analisis dan Kendala
- Analisis penyebab skenario terbaik menghasilkan nilai lebih tinggi: Karena keterbatasan waktu dan jumlah dataset akhir yang digunakan sangat kecil (3 foto), model diprediksi rentan mengalami overfitting. Model mungkin dapat mengenali data training dengan sangat baik namun akurasinya bisa menurun signifikan jika dihadapkan pada data pengujian yang belum pernah dilihat sebelumnya.
- Kendala yang ditemui dan cara penyelesaiannya: Kendala utamanya adalah muncul `FileNotFoundError` saat proses training baru dimulai. Penyebabnya, sistem Roboflow otomatis membuat porsi data validasi jadi 0% karena jumlah foto terlalu sedikit, sehingga folder `valid/images` tidak ikut terunduh. Solusinya, saya memaksa *rebalance split* di Roboflow menjadi 2 foto Train (67%) dan 1 foto Valid (33%), membuat *Version* baru, lalu menghapus folder dataset lama di laptop agar program mengunduh ulang struktur folder secara lengkap.
