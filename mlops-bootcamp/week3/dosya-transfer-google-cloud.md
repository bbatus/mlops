# Local'den Google Cloud VM'e Dosya Transfer

## Yöntem 1: Google Cloud Console (En Kolay - Önerilen)

### Upload (Local → VM)

#### Adım 1: Google Cloud Console SSH'i Aç
- VM instance sayfasında **SSH** butonuna tıkla

#### Adım 2: Upload Butonunu Kullan
- SSH penceresinin **sağ üst köşesinde** ⚙️ (ayarlar) ikonu var
- **Upload file** seçeneğine tıkla
- Dosyayı seç ve yükle

#### Adım 3: Dosyayı Taşı
```bash
# Yüklenen dosya genellikle home dizinine gelir
ls -la ~

# Klasör oluştur ve taşı
mkdir -p ~/mlops/week3
mv ~/dosya-adi ~/mlops/week3/
```

### Download (VM → Local)

```bash
# SSH terminalinde
# Dosyayı sağ üst köşedeki ⚙️ → Download file ile indir
```

**Not**: Bu yöntem **tek dosya** için çalışır. Klasör için zip yapmalısın.

---

## Yöntem 2: gcloud CLI (Klasörler İçin - Önerilen)

### Kurulum (Local Bilgisayarında)

```bash
# Mac
brew install google-cloud-sdk

# Windows
# https://cloud.google.com/sdk/docs/install adresinden indir

# Linux
curl https://sdk.cloud.google.com | bash
exec -l $SHELL
```

### Kullanım

#### Upload (Local → VM)
```bash
# Tek dosya
gcloud compute scp /local/path/dosya.txt vbo-vm-rl:~/mlops/week3/ --zone=europe-west1-c

# Klasör (recursive)
gcloud compute scp --recurse /local/path/mlops-docker vbo-vm-rl:~/mlops/week3/ --zone=europe-west1-c

# Örnek: mlops docs klasörünü yükle
gcloud compute scp --recurse "./mlops docs" vbo-vm-rl:~/mlops/ --zone=europe-west1-c
```

#### Download (VM → Local)
```bash
# Tek dosya
gcloud compute scp vbo-vm-rl:~/mlops/week3/dosya.txt /local/path/ --zone=europe-west1-c

# Klasör
gcloud compute scp --recurse vbo-vm-rl:~/mlops/week3 /local/path/ --zone=europe-west1-c
```

---

## Yöntem 3: Git (En İyi Pratik - Önerilen)

### Neden Git?
- ✅ Version control
- ✅ Takım çalışması
- ✅ Backup
- ✅ Her yerden erişim

### Adımlar

#### 1. Local'de Git Repo Oluştur
```bash
# Local bilgisayarında
cd "mlops docs"
git init
git add .
git commit -m "Initial commit"

# GitHub'a push et (önce GitHub'da repo oluştur)
git remote add origin https://github.com/kullanici-adin/mlops-docs.git
git push -u origin main
```

#### 2. VM'de Clone Et
```bash
# VM'de
cd ~/mlops
git clone https://github.com/kullanici-adin/mlops-docs.git

# Veya SSH ile
git clone git@github.com:kullanici-adin/mlops-docs.git
```

#### 3. Güncellemeleri Çek
```bash
# VM'de
cd ~/mlops/mlops-docs
git pull
```

---

## Yöntem 4: SCP (Terminal Üzerinden)

### Mac/Linux'tan

```bash
# Tek dosya
scp /local/path/dosya.txt batuhansarihanvbo@34.79.253.58:~/mlops/week3/

# Klasör
scp -r "/local/path/mlops docs" batuhansarihanvbo@34.79.253.58:~/mlops/

# Port belirtmek gerekirse
scp -P 22 -r "/local/path/mlops docs" batuhansarihanvbo@34.79.253.58:~/mlops/
```

### Windows'tan (PowerShell)

```powershell
# Tek dosya
scp C:\local\path\dosya.txt batuhansarihanvbo@34.79.253.58:~/mlops/week3/

# Klasör
scp -r "C:\local\path\mlops docs" batuhansarihanvbo@34.79.253.58:~/mlops/
```

**Not**: SSH key authentication gerekebilir.

---

## Yöntem 5: Drag & Drop (VSCode ile)

### VSCode Remote SSH Kurulumu

#### 1. VSCode Extension Kur
- **Remote - SSH** extension'ını kur

#### 2. SSH Config Ekle
```bash
# Local'de ~/.ssh/config dosyasını düzenle
Host gcp-vm
    HostName 34.79.253.58
    User batuhansarihanvbo
    IdentityFile ~/.ssh/google_compute_engine
```

#### 3. Bağlan
- VSCode'da `Cmd/Ctrl + Shift + P`
- "Remote-SSH: Connect to Host" seç
- `gcp-vm` seç

#### 4. Dosya Transfer
- VSCode'un sol tarafındaki Explorer'dan
- Drag & drop ile dosya taşı

---

## Yöntem 6: Cloud Storage (Büyük Dosyalar İçin)

### Google Cloud Storage Kullan

#### 1. Bucket Oluştur (Bir Kez)
```bash
# Local'de
gsutil mb gs://mlops-transfer-bucket
```

#### 2. Upload (Local → Bucket)
```bash
# Local'de
gsutil -m cp -r "./mlops docs" gs://mlops-transfer-bucket/
```

#### 3. Download (Bucket → VM)
```bash
# VM'de
gsutil -m cp -r gs://mlops-transfer-bucket/"mlops docs" ~/mlops/
```

---

## Senin Durumun İçin En İyi Yöntem

### Senaryo 1: Tek Seferlik Transfer (Hızlı)

**Google Cloud Console Upload:**
```bash
# 1. Klasörü zip'le (local'de)
zip -r mlops-docker.zip "mlops docs/mlops-docker"

# 2. Google Cloud Console SSH → Upload file
# mlops-docker.zip'i yükle

# 3. VM'de unzip et
cd ~/mlops
unzip ~/mlops-docker.zip
mv "mlops docs/mlops-docker" ./mlops-docker
rm ~/mlops-docker.zip
```

### Senaryo 2: Sık Güncelleme (Önerilen)

**Git Kullan:**
```bash
# Local'de (bir kez)
cd "mlops docs"
git init
git add .
git commit -m "Initial commit"
# GitHub'a push et

# VM'de (bir kez)
cd ~/mlops
git clone https://github.com/kullanici-adin/mlops-docs.git

# Güncellemeler için (VM'de)
cd ~/mlops/mlops-docs
git pull
```

### Senaryo 3: gcloud CLI Var (En Hızlı)

```bash
# Local'de
gcloud compute scp --recurse "./mlops docs/mlops-docker" vbo-vm-rl:~/mlops/ --zone=europe-west1-c
```

---

## Hızlı Başlangıç (Şu An İçin)

### Adım 1: Klasörü Zip'le (Local'de)
```bash
# Mac/Linux
cd "mlops docs"
zip -r mlops-docker.zip mlops-docker

# Windows (PowerShell)
Compress-Archive -Path "mlops docs\mlops-docker" -DestinationPath mlops-docker.zip
```

### Adım 2: Google Cloud Console'dan Yükle
1. VM instance → SSH
2. Sağ üst → ⚙️ → Upload file
3. `mlops-docker.zip` seç

### Adım 3: VM'de Unzip Et
```bash
# VM'de
cd ~/mlops
unzip ~/mlops-docker.zip
ls -la mlops-docker
rm ~/mlops-docker.zip
```

---

## Karşılaştırma Tablosu

| Yöntem | Hız | Kolay | Klasör | Büyük Dosya | Önerilen |
|--------|-----|-------|--------|-------------|----------|
| Console Upload | ⭐⭐ | ⭐⭐⭐ | ❌ (zip gerek) | ❌ | Tek dosya için |
| gcloud CLI | ⭐⭐⭐ | ⭐⭐ | ✅ | ✅ | Klasörler için |
| Git | ⭐⭐ | ⭐⭐⭐ | ✅ | ❌ | Version control |
| SCP | ⭐⭐⭐ | ⭐ | ✅ | ✅ | Terminal kullanıcıları |
| VSCode | ⭐⭐ | ⭐⭐⭐ | ✅ | ⭐⭐ | GUI severler |
| Cloud Storage | ⭐⭐ | ⭐ | ✅ | ⭐⭐⭐ | Çok büyük dosyalar |

---

## Troubleshooting

### Problem: Permission denied
```bash
# VM'de klasör izinlerini kontrol et
ls -la ~/mlops
chmod 755 ~/mlops
```

### Problem: gcloud komutu bulunamıyor
```bash
# gcloud CLI kur
# https://cloud.google.com/sdk/docs/install
```

### Problem: SSH key hatası
```bash
# SSH key oluştur
ssh-keygen -t rsa -b 4096

# Public key'i VM'e ekle
cat ~/.ssh/id_rsa.pub
# Google Cloud Console → VM → Edit → SSH Keys → Add
```

---

## Sonraki Adım

Hangi yöntemi kullanmak istersin?

1. **Hızlı (Console Upload)** - Zip yap, yükle, unzip et
2. **Profesyonel (Git)** - GitHub'a push, VM'de clone
3. **Direkt (gcloud CLI)** - Tek komutla transfer

Söyle, yardımcı olayım! 🚀
