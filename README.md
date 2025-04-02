# **📦 Pipeline Analisis Metagenomik (QIIME2 + R)**

## 🧬 Overview: Analisis Metagenomik 16S rRNA

Analisis metagenomik 16S rRNA adalah metode untuk mengungkap komposisi dan struktur komunitas mikroba dari suatu sampel lingkungan tanpa perlu mengkultur mikroorganisme secara langsung. Proses ini dimulai dari ekstraksi DNA total dari sampel (misalnya tanah, air, sedimen, atau isi usus), diikuti oleh amplifikasi gen 16S rRNA menggunakan primer universal. Produk PCR kemudian disekuensing menggunakan platform seperti [Illumina MiSeq](https://sapac.illumina.com/systems/sequencing-platforms/miseq.html), menghasilkan data sekuens pendek (short reads) dalam format **.fastq.gz**.

Pipeline ini menggunakan QIIME2 untuk mengolah data sekuens mentah—meliputi trimming, denoising, identifikasi **ASV** (*Amplicon Sequence Variant*), serta analisis filogenetik dan keanekaragaman mikroba (**alpha** dan **beta diversity**). Selanjutnya, data dianalisis dan divisualisasikan secara lebih mendalam di R menggunakan berbagai paket seperti `tidyverse`, `qiime2R`, dan `vegan`. Pendekatan ini memungkinkan eksplorasi komunitas mikroba berdasarkan kondisi lingkungan atau perlakuan eksperimental secara efisien dan reproducible.

## **🧰 Persiapan yang Dibutuhkan**

**Software & Tools:**

-   QIIME2 (versi yang diuji: 2023.5)

-   R (versi yang diuji: 4.3.2)

-   R studio atau Jupyter notebook

-   Anaconda

**Paket R yang diperlukan:**

1.  `tidyverse`

2.  `ggplot2`

3.  `ggraph`

4.  `qiime2R`

5.  `patchwork`

6.  `vegan`

7.  `scales`

8.  `RColorBrewer`

9.  `igraph`

**Struktur Folder yang Direkomendasikan:**

``` bash
project/
│
├── raw_data/        # Data fastq.gz mentah
├── qiime/           # Output dari QIIME2
├── cache/           # File hasil proses sementara
└── figures/         # Gambar dan visualisasi hasil analisis
```

## ⚙️ Instalasi (Windows & UNIX)

**🔧 1. Instalasi QIIME2**

Rekomendasi: Gunakan Conda (melalui Anaconda atau Miniconda) untuk mengelola lingkungan QIIME2 secara terpisah.

-   🔹 UNIX (Linux/macOS)

    ``` bash
    # !bash
    # Install Miniconda (jika belum ada)
    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
    bash Miniconda3-latest-Linux-x86_64.sh

    # Buat dan aktifkan environment QIIME2
    wget https://data.qiime2.org/distro/core/qiime2-2023.5-py38-linux-conda.yml
    conda env create -n qiime2-2023.5 --file qiime2-2023.5-py38-linux-conda.yml
    conda activate qiime2-2023.5
    ```

-   🔸 Windows (via WSL2 / Ubuntu Terminal)

    > **Catatan:** QIIME2 tidak mendukung Windows secara native. Disarankan menggunakan **WSL2 (Windows Subsystem for Linux)** dengan Ubuntu.

    -   Aktifkan WSL dan install Ubuntu melalui Microsoft Store.

    -   Jalankan langkah instalasi UNIX seperti di atas di dalam terminal Ubuntu

**🔧 2. Instalasi R & RStudio**

-   🔹 UNIX (Linux/macOS)

    Install R:

    ``` bash
    sudo apt update
    sudo apt install r-base
    ```

    Download dan install RStudio dari: <https://posit.co/download/rstudio-desktop/>

-   🔸 Windows (via WSL2 / Ubuntu Terminal)

    Install R dari: <https://cran.r-project.org/>

    Install RStudio dari: <https://posit.co/download/rstudio-desktop/>

**📦 3. Instalasi Paket R**

Setelah R terinstall, buka R atau RStudio dan jalankan:

``` r
install.packages(c("tidyverse", "ggplot2", "ggraph", "patchwork",
                   "vegan", "scales", "RColorBrewer", "igraph"))

# Install qiime2R dari GitHub
if (!requireNamespace("devtools", quietly = TRUE)) install.packages("devtools")
devtools::install_github("jbisanz/qiime2R")
```

## 🧪 Langkah-Langkah Pipeline

**1. 🔽 Pengunduhan Data**

Unduh data sekuensing mentah menggunakan `wget` atau tempatkan secara manual ke dalam folder `raw_data/`.

**2. 🧼 Penyaringan Kualitas & Demultipleksing**

-   Import file `.fastq.gz` ke dalam QIIME2.

-   Lakukan proses trimming dan denoising dengan `qiime demux` dan `qiime dada2 denoise-paired`.

**3. 🌳 Konstruksi Pohon Filogenetik**

Lakukan alignment dan bangun pohon filogenetik menggunakan:

`qiime phylogeny align-to-tree-mafft-fasttree`

**4. 📊 Analisis Keanekaragaman (Diversity)**

-   **Alpha diversity**: Shannon, Faith PD, jumlah fitur teramati

-   **Beta diversity**: Bray-Curtis, UniFrac

-   Visualisasi menggunakan `qiime diversity core-metrics-phylogenetic`

**5. 🧬 Klasifikasi Taksonomi**

Gunakan classifier yang sudah dilatih (`sklearn`) melalui:

-   `qiime feature-classifier classify-sklearn`

-   Buat barplot dan ringkasan komposisi taksonomi.

**6. 📥 Import Data QIIME2 ke R**

Gunakan paket `qiime2R` untuk membaca file `.qza` dan `.qzv`.

Data dikonversi ke format tidy untuk analisis dan visualisasi lebih lanjut.

**7. 📈 Visualisasi & Analisis Lanjutan di R**

-   Plot keanekaragaman alpha berdasarkan metadata sampel

-   Ordination dan analisis beta diversity menggunakan `vegan` dan `ggraph`

-   Visualisasi komposisi taksonomi berdasarkan kelompok

-   Plot abundance relatif dan jaringan komunitas mikroba
