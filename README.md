# 🚀 Panduan Lengkap Autonomys Node

## 📋 Persiapan

Sebelum memulai instalasi, pastikan kamu telah memiliki:
- Alamat substrate (bisa menggunakan Subwallet)
- Aktifkan network Gemini 3h di wallet kamu

## 🛠️ Opsi Instalasi

Ada dua opsi instalasi yang tersedia:
1. **CLI (Command Line Interface)** - Membutuhkan VPS (direkomendasikan)
2. **GUI (Graphical User Interface)** - Dapat digunakan tanpa VPS

### 🖥️ Opsi 1: Instalasi CLI (dengan VPS)

#### Langkah 1: Instal Docker dan Docker Compose
```bash
sudo apt update
sudo apt install docker.io docker-compose -y
```

#### Langkah 2: Buat Screen Baru
```bash
screen -Rd autonomys
```

#### Langkah 3: Buat Folder Baru
```bash
mkdir autonomys && cd autonomys
```

#### Langkah 4: Buat File Docker Compose
```bash
nano docker-compose.yml
```

#### Langkah 5: Masukkan Kode Docker Compose
Salin dan tempel kode berikut ke dalam file `docker-compose.yml`. Pastikan untuk mengganti bagian `"--reward-address"` dengan alamat Subwallet kamu yang dimulai dengan "st".

```yaml
services:
  node:
    image: ghcr.io/autonomys/node:gemini-3h-2024-sep-03
    volumes:
      - node-data:/var/subspace:rw
    ports:
      - "0.0.0.0:30333:30333/tcp"
      - "0.0.0.0:30433:30433/tcp"
    restart: unless-stopped
    command:
      [
        "run",
        "--chain", "gemini-3h",
        "--base-path", "/var/subspace",
        "--listen-on", "/ip4/0.0.0.0/tcp/30333",
        "--dsn-listen-on", "/ip4/0.0.0.0/tcp/30433",
        "--rpc-cors", "all",
        "--rpc-methods", "unsafe",
        "--rpc-listen-on", "0.0.0.0:9944",
        "--farmer",
        "--name", "subspace"
      ]
    healthcheck:
      timeout: 5s
      interval: 30s
      retries: 60
  farmer:
    depends_on:
      node:
        condition: service_healthy
    image: ghcr.io/autonomys/farmer:gemini-3h-2024-sep-03
    volumes:
      - farmer-data:/var/subspace:rw
    ports:
      - "0.0.0.0:30533:30533/tcp"
    restart: unless-stopped
    command:
      [
        "farm",
        "--node-rpc-url", "ws://node:9944",
        "--listen-on", "/ip4/0.0.0.0/tcp/30533",
        "--reward-address", "st7tzEFXg1TWbkXwufTysZogs4iXESbyUykFY3qJ1iojxZQJn",
        "path=/var/subspace,size=100G"
      ]
volumes:
  node-data:
  farmer-data:
```

Simpan file dengan menekan `Ctrl + X`, lalu `Y`, dan `Enter`.

#### Langkah 6: Jalankan Node
```bash
docker-compose up -d
```

#### Langkah 7: Lihat Logs (Opsional)
```bash
docker compose logs --tail=1000 -f
```

#### Langkah 8: Isi Formulir Feedback
Kunjungi: https://autonomys.typeform.com/to/tRe4Z0uI

Jika kamu mengalami kesulitan membuat feedback, kamu dapat menggunakan tool generator feedback: [sultanfeedback.vercel.app](https://sultanfeedback.vercel.app)

### 🖱️ Opsi 2: Instalasi GUI (tanpa VPS)

1. Download installer dari [link ini](https://github.com/autonomys/space-acres/releases/download/0.1.35/space-acres-0.1.35-x86_64.msi).
2. Buka file yang telah diunduh dan ikuti proses instalasi.
3. Untuk panduan bergambar yang lebih detail, silakan kunjungi [dokumentasi resmi Autonomys](https://docs.autonomys.xyz/docs/farming-&-staking/farming/space-acres/space-acres-install/).

## 🎉 Selamat!

Kamu telah berhasil menginstal dan menjalankan Autonomys Node. Jika kamu mengalami kesulitan atau memiliki pertanyaan, jangan ragu untuk bertanya di komunitas resmi Autonomys.
