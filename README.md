# Empty Glass vs Filled Glass Image Dataset

## Dataset Summary

Dataset ini dibuat untuk tugas klasifikasi kondisi gelas berdasarkan citra, yaitu membedakan gelas kosong (**Empty Glass**) dan gelas berisi cairan (**Filled Glass**). Seluruh citra diambil sendiri melalui proses data acquisition menggunakan smartphone, bukan diambil dari dataset publik atau sumber eksternal. Setiap citra dilengkapi metadata tabular yang mencatat kondisi objek dan proses pengambilan gambar, sehingga sumber bias bisa ditelusuri dengan jelas.

Dataset ini merupakan kelanjutan dari proyek `liquid-detection-mobilenetv2` pada tugas Machine Learning 1.

## Supported Tasks

**Binary Image Classification**
Model diharapkan mampu menjawab pertanyaan: apakah gelas pada sebuah citra dalam kondisi kosong atau berisi cairan sebagai isi utamanya.

| Label | Definisi |
|---|---|
| Empty Glass | Gelas tidak memiliki volume cairan yang dianggap sebagai isi utama |
| Filled Glass | Gelas memiliki cairan yang dapat diamati secara visual sebagai isi utama |

## Dataset Structure

```
empty-glass-vs-filled-glass/
├── raw/
│   ├── empty_glass/
│   └── filled_glass/
├── processed/
├── augmented/
├── metadata/
│   └── dataset_metadata.csv
└── documentation/
    └── dataset_card.md
```

Folder `raw/` menyimpan citra original hasil acquisition dan menjadi sumber utama sebelum preprocessing. Folder `processed/` dan `augmented/` diisi setelah tahap masing-masing dijalankan dalam workflow eksperimen.

### Data Instances

Satu data point adalah satu file citra yang menampilkan satu gelas, dengan satu baris metadata yang menyertainya.

```json
{
  "image_id": "IMG002",
  "filename": "IMG002.jpg",
  "label": "Filled Glass",
  "glass_type": "Mug",
  "liquid_type": "Water",
  "fill_level": "70%",
  "camera_angle": "45°",
  "lighting_condition": "Bright",
  "background": "White Table"
}
```

### Data Fields

| Field | Deskripsi |
|---|---|
| image_id | ID unik citra |
| filename | Nama file citra |
| label | Label klasifikasi (Empty Glass / Filled Glass) |
| glass_type, glass_material | Jenis dan material gelas |
| liquid_type, fill_level | Jenis cairan dan tingkat pengisian |
| camera_angle, camera_distance, orientation, object_position | Parameter pengambilan gambar |
| lighting_condition, background, location_type | Kondisi lingkungan acquisition |
| device, capture_date | Perangkat dan tanggal pengambilan |
| quality_status, rejection_reason | Hasil quality control |

### Data Splits

| Kelas | Target Acquisition | Minimum Dataset Final |
|---|---|---|
| Empty Glass | 35 | 25 |
| Filled Glass | 35 | 25 |
| Total | 70 | 50 |

Selisih antara target acquisition dan minimum final berfungsi sebagai buffer quality control, agar citra yang gagal QC bisa dibuang tanpa membuat dataset jatuh di bawah batas minimum.

## Dataset Creation

### Curation Rationale

Sistem komputer memerlukan data visual yang terstruktur untuk mempelajari perbedaan gelas kosong dan berisi, sesuatu yang mudah dikenali manusia tapi tidak otomatis bagi model. Data dikumpulkan secara mandiri agar proses acquisition, variasi objek, dan metadata bisa dikendalikan sesuai kebutuhan riset, sekaligus menekan ketergantungan model pada faktor tidak relevan seperti background atau pencahayaan tertentu.

### Source Data

Citra diambil langsung dari gelas nyata menggunakan kamera smartphone. Variasi yang diperhatikan selama pengambilan meliputi jenis dan ukuran gelas, jenis dan tingkat isi cairan, sudut serta jarak kamera, dan kondisi lingkungan (pencahayaan, background, indoor/outdoor). Setiap variasi spesifik dibatasi maksimal sekitar 10 citra agar tidak mendominasi dataset.

Alur acquisition: menentukan objek dan kondisi gelas → menyiapkan lingkungan → mengambil citra → memberi image_id → mencatat metadata → pemeriksaan awal → quality control.

### Annotations

Pelabelan dilakukan manual oleh satu annotator berdasarkan kondisi aktual gelas saat citra diambil, bukan dari bentuk atau warna gelas. Citra yang kondisinya tidak bisa dipastikan (karena refleksi, transparansi, objek terhalang, dan sejenisnya) diberi status *ambiguous* dan ditinjau ulang sebelum masuk dataset final.

Citra ditolak (rejected) apabila mengalami blur berat, objek tidak terlihat jelas, kondisi ambigu, file corrupt, duplikat, melanggar protokol acquisition, atau metadata tidak lengkap. Data yang ditolak tetap dicatat statusnya, tidak langsung dihapus, demi menjaga traceability.

## Considerations for Using the Data

### Bias dan Risiko

Karena ukuran sampel relatif kecil dan dikumpulkan secara mandiri, dataset berpotensi memiliki bias berikut:

- **Object bias**: jenis atau bentuk gelas tertentu bisa lebih dominan
- **Environmental bias**: kondisi background dan pencahayaan tertentu lebih sering muncul
- **Device bias**: karakteristik kamera memengaruhi warna dan ketajaman citra
- **Liquid bias**: warna atau jenis cairan tertentu bisa jadi shortcut feature untuk kelas Filled Glass
- **Acquisition bias**: sudut dan jarak pengambilan tertentu tidak mewakili semua kondisi dunia nyata

Risiko utamanya adalah model belajar shortcut feature, misalnya mengaitkan background tertentu dengan label Filled Glass, alih-alih benar-benar mempelajari keberadaan cairan.

### Limitasi

Dataset berukuran kecil (target 70 citra, minimum final 50 citra) sehingga variasi gelas, cairan, dan kondisi acquisition yang bisa direpresentasikan terbatas. Dataset hanya mencakup dua kelas dan belum diuji generalisasinya pada perangkat, lingkungan, atau sumber data lain.

### Out-of-Scope Use

Dataset tidak dirancang untuk mengukur volume cairan secara presisi, mengidentifikasi jenis cairan, menentukan merek gelas, atau mendukung sistem otomatis yang bersifat kritis.

## Additional Information

### Licensing

Dataset ditujukan untuk keperluan akademik dan penelitian Machine Learning/Deep Learning. Ketentuan distribusi mengikuti tujuan publikasi dataset.

### Dataset Curator

**Adrian Bintang Saputera**
Universitas Lambung Mangkurat, Machine Learning 2
Kontak: 2310817110006@mhs.ulm.ac.id

### Versioning

| Version | Status | Deskripsi |
|---|---|---|
| v0.1 | Draft | Penyusunan awal Dataset Card |
| v0.2 | Draft | Revisi struktur dan protokol acquisition |
| v1.0 | Planned | Rilis dataset setelah acquisition, labeling, dan QC selesai |
