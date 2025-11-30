# MLOps Docker Stack Kurulumu

## Genel Bakış

Bu dokümanda, MLOps pipeline'ı için gerekli tüm servisleri Docker Compose ile kuracağız.

### Kurulacak Servisler:
1. **MLflow** (5001) - ML experiment tracking ve model registry
2. **MinIO** (9000, 9001) - S3-compatible object storage (artifact store)
3. **MySQL** (3306) - MLflow ve Gitea için database
4. **Gitea** (3000, 222) - Git server (GitHub alternatifi)
5. **Gitea Runner** - CI/CD için runner
6. **Jenkins** (8080, 50000) - CI/CD automation server
7. **Prod Server** (8000, 2222) - Production deployment server
8. **Test Server** (8001, 2223) - Test deployment server

### Network:
- Tüm servisler `mlops-net` (172.18.0.0/16) network'ünde çalışır
- Her servisin sabit IP adresi var

---

## Ön Hazırlık: Mevcut Docker Temizliği

### 1. Çalışan Container'ları Durdur
```bash
# Tüm çalışan container'ları listele
docker ps

# Tüm container'ları durdur
docker stop $(docker ps -aq)

# Tüm container'ları sil
docker rm $(docker ps -aq)
```

### 2. Docker Image'larını Temizle (Opsiyonel)
```bash
# Kullanılmayan image'ları sil
docker image prune -a

# Tüm image'ları görmek için
docker images
```

### 3. Docker Volume'leri Temizle (DİKKAT: Veri kaybı!)
```bash
# Kullanılmayan volume'leri sil
docker volume prune

# Tüm volume'leri görmek için
docker volume ls
```

### 4. Docker Network'leri Temizle
```bash
# Kullanılmayan network'leri sil
docker network prune

# Tüm network'leri görmek için
docker network ls
```

### 5. Tam Temizlik (Tüm Sistem)
```bash
# DİKKAT: Bu komut her şeyi siler!
docker system prune -a --volumes

# Onay: y
```

---

## Kurulum Adımları

### 1. Repository'yi Clone Et (Eğer Yapmadıysan)
```bash
cd ~
git clone https://github.com/bbatus/mlops.git
cd mlops
```

### 2. Docker Klasörüne Git
```bash
cd "mlops docs/02_mlops_docker"
pwd
# Çıktı: /home/batuhansarihanvbo/mlops/mlops docs/02_mlops_docker
```

### 3. Dosya İzinlerini Ayarla
```bash
# Shell script'leri çalıştırılabilir yap
chmod +x wait-for-it.sh
chmod +x prod/init_script.sh
chmod +x test/init_script.sh

# Doğrulama
ls -la *.sh
ls -la prod/*.sh
ls -la test/*.sh
```

### 4. Windows Dosya Formatını Düzelt (Gerekirse)
```bash
# Eğer dosyalar Windows'tan geldiyse, satır sonlarını düzelt
sed -i 's/\r$//' wait-for-it.sh
sed -i 's/\r$//' prod/init_script.sh
sed -i 's/\r$//' test/init_script.sh
```

### 5. .env Dosyasını Kontrol Et
```bash
# .env dosyası var mı kontrol et
ls -la .env

# İçeriğini görüntüle
cat .env
```

**Beklenen .env içeriği:**
```env
# AWS/MinIO Credentials
AWS_ACCESS_KEY_ID=trainkey
AWS_SECRET_ACCESS_KEY=trainsecret

# S3 Bucket
S3_MLFLOW_BUCKET=mlflow

# MySQL
MYSQL_DATABASE=mlflow
MYSQL_USER=mlflow
MYSQL_PASSWORD=mlflow
MYSQL_ROOT_PASSWORD=root

# Gitea Runner Token (sonra güncellenecek)
GITEA_RUNNER_TOKEN=your_token_here
```

### 6. Docker Compose Versiyonunu Kontrol Et
```bash
docker compose version
# Beklenen: Docker Compose version v2.x.x
```

### 7. Docker Compose ile Servisleri Başlat
```bash
# Tüm servisleri build et ve başlat
docker compose up --build -d

# Bu işlem ~10 dakika sürebilir
```

**Açıklama:**
- `--build`: Image'ları yeniden build eder
- `-d`: Detached mode (arka planda çalışır)

### 8. Container'ların Durumunu Kontrol Et
```bash
# Çalışan container'ları listele
docker compose ps

# Tüm container'lar "Up" durumunda olmalı
```

### 9. Log'ları İzle
```bash
# Tüm servislerin loglarını izle
docker compose logs -f

# Spesifik servis logu
docker compose logs -f mlflow
docker compose logs -f mysql
docker compose logs -f minio

# Çıkmak için: Ctrl+C
```

### 10. Volume Sahipliğini Ayarla
```bash
# Prod ve test server volume'lerinin sahipliğini değiştir
sudo chown -R $USER:$USER prod/home/
sudo chown -R $USER:$USER test/home/

# Doğrulama
ls -la prod/home/
ls -la test/home/
```

---

## Google Cloud Firewall Kuralları

### Gerekli Portları Aç
```bash
# MLflow (5001)
gcloud compute firewall-rules create allow-mlflow \
  --allow tcp:5001 \
  --source-ranges 0.0.0.0/0 \
  --description "MLflow UI"

# MinIO Console (9001)
gcloud compute firewall-rules create allow-minio-console \
  --allow tcp:9001 \
  --source-ranges 0.0.0.0/0 \
  --description "MinIO Console"

# MinIO API (9000)
gcloud compute firewall-rules create allow-minio-api \
  --allow tcp:9000 \
  --source-ranges 0.0.0.0/0 \
  --description "MinIO API"

# Gitea (3000)
gcloud compute firewall-rules create allow-gitea \
  --allow tcp:3000 \
  --source-ranges 0.0.0.0/0 \
  --description "Gitea UI"

# Jenkins (8080)
gcloud compute firewall-rules create allow-jenkins \
  --allow tcp:8080 \
  --source-ranges 0.0.0.0/0 \
  --description "Jenkins UI"

# MySQL (3306) - Sadece gerekirse
gcloud compute firewall-rules create allow-mysql \
  --allow tcp:3306 \
  --source-ranges 0.0.0.0/0 \
  --description "MySQL"

# Prod Server (8000)
gcloud compute firewall-rules create allow-prod \
  --allow tcp:8000 \
  --source-ranges 0.0.0.0/0 \
  --description "Production Server"

# Test Server (8001)
gcloud compute firewall-rules create allow-test \
  --allow tcp:8001 \
  --source-ranges 0.0.0.0/0 \
  --description "Test Server"
```

---

## Web UI'lara Erişim

### 1. MLflow UI
```
URL: http://34.79.253.58:5001
```
- Experiment tracking
- Model registry
- Artifact store

### 2. MinIO Console
```
URL: http://34.79.253.58:9001
Username: trainkey
Password: trainsecret
```
- S3-compatible object storage
- Bucket yönetimi
- File upload/download

### 3. Gitea UI
```
URL: http://34.79.253.58:3000
```

#### İlk Kurulum:
1. Tarayıcıda aç
2. **"Install Gitea"** butonuna tıkla
3. Eğer "Page not found" görürsen, sayfayı yenile
4. Login ekranı görünecek

#### Kullanıcı Oluştur:
```
Username: jenkins
Email: jenkins@vbo.local
Password: Ankara_06
```

#### Dil Değiştirme:
1. Sağ üst köşe → Profil
2. Settings → Account
3. Language → Türkçe (veya English)

### 4. Jenkins UI
```
URL: http://34.79.253.58:8080
```
- İlk şifre için: `docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword`

### 5. MySQL (Opsiyonel)
```
Host: 34.79.253.58
Port: 3306
Username: mlflow
Password: mlflow
Database: mlflow
```

---

## Gitea Runner Kurulumu

### 1. Gitea'da Runner Token Oluştur

#### Adım 1: Gitea'ya Giriş Yap
```
http://34.79.253.58:3000
```

#### Adım 2: Runner Token Oluştur
1. Sağ üst → **Site Administration**
2. Sol menü → **Actions** → **Runners**
3. **"Create new Runner"** tıkla
4. Token'ı kopyala (örn: `abc123def456...`)

### 2. .env Dosyasını Güncelle
```bash
# .env dosyasını düzenle
cd ~/mlops/"mlops docs"/02_mlops_docker
nano .env

# GITEA_RUNNER_TOKEN satırını güncelle:
GITEA_RUNNER_TOKEN=abc123def456...

# Kaydet: Ctrl+O, Enter, Ctrl+X
```

### 3. Runner'ı Yeniden Başlat
```bash
# Runner'ı durdur
docker compose down gitea-runner

# Runner'ı başlat
docker compose up -d gitea-runner

# Log'u kontrol et
docker compose logs -f gitea-runner
```

### 4. Gitea UI'da Doğrula
1. Gitea → Site Administration → Actions → Runners
2. Yeni runner görünmeli (yeşil ✓)

---

## Servis Detayları

### MLflow
- **Port**: 5001
- **Backend Store**: MySQL
- **Artifact Store**: MinIO (S3)
- **Endpoint**: http://mlflow_server:5000 (container içinden)

### MinIO
- **Console Port**: 9001
- **API Port**: 9000
- **Access Key**: trainkey
- **Secret Key**: trainsecret
- **Bucket**: mlflow

### MySQL
- **Port**: 3306
- **Databases**: mlflow, gitea
- **Root Password**: root
- **MLflow User**: mlflow/mlflow

### Gitea
- **Web Port**: 3000
- **SSH Port**: 222
- **Database**: MySQL (gitea)
- **Actions**: Enabled

### Jenkins
- **Web Port**: 8080
- **Agent Port**: 50000
- **Image**: veribilimiokulu/jenkins:ltsjdk17-ansible9.5.1

### Prod Server
- **HTTP Port**: 8000
- **SSH Port**: 2222
- **User**: prod_user
- **Image**: veribilimiokulu/mlops-prod-server:rocky8.6-python3.12

### Test Server
- **HTTP Port**: 8001
- **SSH Port**: 2223
- **User**: test_user
- **Image**: veribilimiokulu/mlops-test-server:rocky8.6-python3.12

---

## Yaygın Komutlar

### Container Yönetimi
```bash
# Tüm servisleri başlat
docker compose up -d

# Tüm servisleri durdur
docker compose down

# Spesifik servisleri başlat
docker compose up -d mlflow mysql minio

# Spesifik servisi yeniden başlat
docker compose restart mlflow

# Container'a gir
docker exec -it mlflow bash
docker exec -it mysql bash
```

### Log İzleme
```bash
# Tüm loglar
docker compose logs -f

# Spesifik servis
docker compose logs -f mlflow

# Son 100 satır
docker compose logs --tail=100 mlflow
```

### Durum Kontrolü
```bash
# Çalışan container'lar
docker compose ps

# Detaylı bilgi
docker compose ps -a

# Resource kullanımı
docker stats
```

### Temizlik
```bash
# Container'ları durdur ve sil
docker compose down

# Volume'leri de sil (DİKKAT: Veri kaybı!)
docker compose down -v

# Image'ları da sil
docker compose down --rmi all
```

---

## Troubleshooting

### Problem: Container başlamıyor
```bash
# Log'u kontrol et
docker compose logs <service-name>

# Container'ı yeniden başlat
docker compose restart <service-name>

# Container'ı sil ve yeniden oluştur
docker compose up -d --force-recreate <service-name>
```

### Problem: Port zaten kullanımda
```bash
# Hangi process kullanıyor?
sudo netstat -tulpn | grep <port>

# Process'i durdur
sudo kill <PID>
```

### Problem: Volume permission hatası
```bash
# Sahipliği değiştir
sudo chown -R $USER:$USER prod/home/
sudo chown -R $USER:$USER test/home/
```

### Problem: MySQL bağlantı hatası
```bash
# MySQL container'ına gir
docker exec -it mysql bash

# MySQL'e bağlan
mysql -u root -p
# Password: root

# Database'leri listele
SHOW DATABASES;

# Çık
exit
```

### Problem: MinIO bucket yok
```bash
# mc container'ını kontrol et
docker compose logs mc

# Manuel bucket oluştur
docker exec -it minio bash
mc alias set minio http://localhost:9000 trainkey trainsecret
mc mb minio/mlflow
exit
```

### Problem: Gitea runner kayıtlı değil
```bash
# Runner token'ı kontrol et
cat .env | grep GITEA_RUNNER_TOKEN

# Runner'ı yeniden başlat
docker compose down gitea-runner
docker compose up -d gitea-runner

# Log'u izle
docker compose logs -f gitea-runner
```

---

## Network Yapısı

```
mlops-net (172.18.0.0/16)
├── 172.18.0.2  → mc (MinIO Client)
├── 172.18.0.3  → mysql
├── 172.18.0.4  → mlflow
├── 172.18.0.5  → gitea
├── 172.18.0.6  → jenkins
├── 172.18.0.7  → prod_server
├── 172.18.0.8  → test_server
├── 172.18.0.9  → minio
└── 172.18.0.10 → gitea-runner
```

---

## Volume'ler

```bash
# Volume'leri listele
docker volume ls

# Volume detayları
docker volume inspect 02_mlops_docker_dbdata
docker volume inspect 02_mlops_docker_minio_data
docker volume inspect 02_mlops_docker_jenkins_home
```

**Volume'ler:**
- `dbdata` → MySQL verileri
- `minio_data` → MinIO object storage
- `jenkins_home` → Jenkins konfigürasyonu ve joblar

---

## Güvenlik Notları

### Production Ortamı İçin:
1. **.env dosyasını güvenli tut** - Git'e commit etme
2. **Güçlü şifreler kullan** - Varsayılan şifreleri değiştir
3. **Firewall kurallarını sınırla** - 0.0.0.0/0 yerine spesifik IP'ler
4. **HTTPS kullan** - Nginx reverse proxy ekle
5. **Volume backup** - Düzenli yedekleme yap

### .env Dosyası Güvenliği:
```bash
# .env'i .gitignore'a ekle
echo ".env" >> .gitignore

# Örnek .env.example oluştur
cp .env .env.example
# .env.example'daki gerçek değerleri sil
```

---

## Sonraki Adımlar

1. ✅ Docker Compose stack kuruldu
2. ✅ Tüm servisler çalışıyor
3. ✅ Web UI'lara erişim sağlandı
4. ✅ Gitea runner kayıtlı
5. 🔜 MLflow ile ilk experiment
6. 🔜 Gitea'da repository oluştur
7. 🔜 Jenkins pipeline kur
8. 🔜 Model deployment (prod/test)

---

## Hızlı Referans

```bash
# Başlat
docker compose up -d

# Durdur
docker compose down

# Loglar
docker compose logs -f

# Durum
docker compose ps

# Yeniden başlat
docker compose restart

# Temizlik (veri kaybı!)
docker compose down -v
```

**Web UI'lar:**
- MLflow: http://34.79.253.58:5001
- MinIO: http://34.79.253.58:9001 (trainkey/trainsecret)
- Gitea: http://34.79.253.58:3000
- Jenkins: http://34.79.253.58:8080

**Not**: IP adresini kendi VM IP'nle değiştir!
