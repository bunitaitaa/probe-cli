
[![OONI Probe Android](docs/logo.png)](https://ooni.org)

<div align="center">

[![GoDoc](https://pkg.go.dev/badge/github.com/ooni/probe-cli/)](https://pkg.go.dev/github.com/ooni/probe-cli/v3) [![Coverage Status](https://coveralls.io/repos/github/ooni/probe-cli/badge.svg?branch=master)](https://coveralls.io/github/ooni/probe-cli?branch=master) [![Slack](https://slack.openobservatory.org/badge.svg)](https://slack.openobservatory.org/)

## OONI Probe Measurement Engine and CLI

**[Instruksi instalasi »](https://ooni.org/install/cli)**

[Panduan pengguna](https://ooni.org/support/ooni-probe-cli) · [API docs](https://godoc.org/github.com/ooni/probe-cli) · [Laporkan bug](https://github.com/ooni/probe/issues/new?labels=ooni/probe-cli&assignee=bassosimone) · [Ajukan fitur](https://github.com/ooni/probe/issues/new?labels=ooni/probe-cli&assignee=bassosimone) · [Tutorial](https://github.com/ooni/probe-cli/tree/master/internal/tutorial)

</div>

<details>
  <summary>Daftar Isi</summary>
  <ol>
    <li><a href="#tentang-proyek-ini">Tentang proyek ini</a></li>
    <li><a href="#instruksi-instalasi">Instruksi instalasi</a></li>
    <li><a href="#instalasi-di-debianubuntu-dengan-troubleshooting">Instalasi di Debian/Ubuntu (dengan troubleshooting)</a></li>
    <li><a href="#nightly-builds">Nightly Builds</a></li>
    <li><a href="#instruksi-pengembangan">Instruksi pengembangan<a></li>
    <li><a href="#kontribusi">Kontribusi</a></li>
    <li><a href="#lisensi">Lisensi</a></li>
  </ol>
</details>

<hr>

## Tentang proyek ini

[Open Observatory of Network Interference](https://ooni.org) (OONI)  
adalah proyek perangkat lunak bebas nirlaba yang bertujuan mendukung upaya desentralisasi dalam mendokumentasikan sensor internet di seluruh dunia.

Repositori ini berisi paket Go berikut:

1. klien command line `ooniprobe` ([cmd/ooniprobe](cmd/ooniprobe));  
2. server test helper ([internal/cmd/oohelperd](internal/cmd/oohelperd));  
3. library mobile ([pkg/oonimkall](pkg/oonimkall));  
4. library measurement-engine ([internal](internal));  
5. klien command line eksperimental `miniooni` ([internal/cmd/miniooni](internal/cmd/miniooni)).

---

## Instruksi instalasi

Ikuti instruksi di [ooni.org/install/cli](https://ooni.org/install/cli)  
untuk memasang `ooniprobe` (tersedia untuk Windows, macOS, dan Debian/Ubuntu).  
Setelah terpasang, baca [panduan pengguna](https://ooni.org/support/ooni-probe-cli).

---

## Instalasi di Debian/Ubuntu (dengan troubleshooting)

Bagian ini mendokumentasikan langkah instalasi yang diuji di **Ubuntu 24.04 (Noble)**,  
termasuk kondisi kegagalan dan solusi yang ditemukan.

### ❌ Kondisi kegagalan umum

| Pesan error | Penyebab | Solusi |
|-------------|----------|--------|
| `gpg: can't connect to the dirmngr` / `No such file or directory` | Paket `dirmngr` belum terpasang dan `/root/.gnupg` belum ada | Pasang `gnupg dirmngr`, buat direktori `/root/.gnupg` dengan izin benar |
| `NO_PUBKEY 372D1FF271F2DD50` saat `apt update` | Repo ditambahkan sebelum kunci OONI diimpor | Impor kunci GPG OONI terlebih dahulu, lalu tambah repo |
| `unexpected web_connectivity` | Versi CLI baru tidak lagi menerima subcommand `web_connectivity` | Gunakan `ooniprobe run websites --input URL` |
| `failed to sufficiently increase receive buffer size ...` | Buffer UDP kernel terlalu kecil (hanya peringatan) | Opsional: naikkan dengan sysctl |

### ✅ Langkah instalasi yang berhasil

```bash
# 1. Pasang dependensi
sudo apt-get update
sudo apt-get install -y gnupg dirmngr ca-certificates

# 2. Pastikan direktori GnuPG root ada
sudo install -d -m 0700 -o root -g root /root/.gnupg

# 3. Impor kunci OONI
sudo install -d -m 0755 /etc/apt/keyrings
sudo gpg --no-default-keyring \
  --keyring /etc/apt/keyrings/ooni-apt-keyring.gpg \
  --keyserver hkps://keyserver.ubuntu.com:443 \
  --recv-keys B5A08F01796E7F521861B449372D1FF271F2DD50

# 4. Tambahkan repository OONI
echo "deb [signed-by=/etc/apt/keyrings/ooni-apt-keyring.gpg] https://deb.ooni.org/ unstable main" | sudo tee /etc/apt/sources.list.d/ooniprobe.list

# 5. Update & install OONI Probe CLI
sudo apt-get update
sudo apt-get install -y ooniprobe-cli
````

### 🚀 Cara menjalankan

* Mode interaktif (semua tes):

  ```bash
  ooniprobe run
  ```

* Tes satu situs:

  ```bash
  ooniprobe run websites --input https://youtube.com
  ```

* Tes daftar situs (dari file teks):

  ```bash
  ooniprobe run websites --input-file daftar_url.txt
  ```

### ℹ️ Opsional: memperbaiki peringatan buffer UDP

```bash
echo 'net.core.rmem_max=2500000
net.core.wmem_max=2500000' | sudo tee /etc/sysctl.d/99-quic.conf
sudo sysctl --system
```

---

## Nightly Builds

Kami merilis build harian menggunakan [rolling release tag](https://github.com/ooni/probe-cli/releases/tag/rolling).
Build ini berisi commit terbaru dari branch `master`.

---

## Instruksi pengembangan

Untuk setup pengembangan dibutuhkan Go >= 1.15 serta kompiler C (gcc/clang).
Lihat dokumen asli untuk detail instruksi pada Debian, Fedora, dan macOS.

---
