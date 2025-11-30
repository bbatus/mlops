# Docker ve Docker Compose Kurulumu - Rocky Linux 10

## Docker Rocky Linux'ta Kurulu Gelir mi?
**HAYIR** - Rocky Linux minimal kurulum ile gelir, Docker manuel olarak kurulmalıdır.

---

## Docker Kurulumu

### 1. Sistem Hazırlığı
```bash
# Sistem güncellemesi
sudo dnf update -y

# Gerekli bağımlılıklar
sudo dnf install -y dnf-plugins-core
```

### 2. Docker Repository Ekleme
```bash
# Docker'ın resmi repository'sini ekle
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### 3. Docker Engine Kurulumu
```bash
# Docker ve bileşenlerini kur
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Docker servisini başlat
sudo systemctl start docker

# Sistem açılışında otomatik başlat
sudo systemctl enable docker
```

### 4. Kullanıcı İzinleri (Opsiyonel ama Önerilen)
```bash
# Mevcut kullanıcıyı docker grubuna ekle (sudo olmadan docker kullanmak için)
sudo usermod -aG docker $USER

# Değişikliklerin aktif olması için yeniden giriş yap veya:
newgrp docker
```

---

## Docker Compose Kurulumu

**İyi Haber**: Docker Compose artık Docker'ın bir parçası olarak geliyor (plugin olarak).

### Kurulum Doğrulama
```bash
# Docker Compose versiyonu (yeni yöntem)
docker compose version

# Eski yöntem de çalışabilir
docker-compose version
```

### Manuel Kurulum (Gerekirse)
```bash
# En son sürümü indir
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# Çalıştırma izni ver
sudo chmod +x /usr/local/bin/docker-compose

# Symlink oluştur
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

---

## Kurulum Doğrulama Komutları

### Docker Kontrolü
```bash
# Docker versiyonu
docker --version
# Beklenen çıktı: Docker version 24.x.x, build xxxxx

# Docker servisi durumu
sudo systemctl status docker
# Beklenen: active (running)

# Docker bilgisi
docker info
# Sistem bilgilerini gösterir

# Test container çalıştır
docker run hello-world
# Başarılı ise "Hello from Docker!" mesajı görünür
```

### Docker Compose Kontrolü
```bash
# Docker Compose versiyonu
docker compose version
# Beklenen çıktı: Docker Compose version v2.x.x

# Alternatif komut
docker-compose --version
```

### Container Listesi
```bash
# Çalışan container'ları listele
docker ps

# Tüm container'ları listele (durmuş olanlar dahil)
docker ps -a

# Image'ları listele
docker images
```

### Sistem Kaynakları
```bash
# Docker disk kullanımı
docker system df

# Detaylı bilgi
docker system df -v
```

---

## Hızlı Test Senaryosu

```bash
# 1. Nginx container çalıştır
docker run -d -p 8080:80 --name test-nginx nginx

# 2. Kontrol et
docker ps

# 3. Tarayıcıdan veya curl ile test et
curl http://localhost:8080

# 4. Temizlik
docker stop test-nginx
docker rm test-nginx
```

---

## Yaygın Sorunlar ve Çözümler

### Problem: "permission denied" hatası
```bash
# Çözüm: Kullanıcıyı docker grubuna ekle
sudo usermod -aG docker $USER
newgrp docker
```

### Problem: Docker servisi başlamıyor
```bash
# Servisi yeniden başlat
sudo systemctl restart docker

# Logları kontrol et
sudo journalctl -u docker.service
```

### Problem: Port zaten kullanımda
```bash
# Hangi process kullanıyor kontrol et
sudo netstat -tulpn | grep <port>

# Veya
sudo lsof -i :<port>
```

---

## MLOps için Önemli Docker Komutları

```bash
# Container loglarını izle
docker logs -f <container-name>

# Container içine gir (debugging)
docker exec -it <container-name> bash

# Tüm durmuş container'ları temizle
docker container prune

# Kullanılmayan image'ları temizle
docker image prune -a

# Tüm sistemi temizle (DİKKAT: Her şeyi siler!)
docker system prune -a --volumes
```

---

## Sonraki Adımlar
- ✅ Docker kuruldu
- ✅ Docker Compose hazır
- 🔜 MLflow kurulumu
- 🔜 Airflow deployment
- 🔜 Model serving container'ları

**Not**: MLOps pipeline'larında her tool genellikle kendi container'ında çalışır. Docker bu yüzden temel bir gereklilik.
