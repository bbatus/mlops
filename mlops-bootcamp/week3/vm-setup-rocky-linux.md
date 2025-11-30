# Google Cloud VM Setup - Rocky Linux

## VM Özellikleri
- **Instance Adı**: vbo-vm-rl
- **Bölge**: europe-west1-c
- **Makine Tipi**: e2-medium (2 vCPUs, 4 GB RAM)
- **İşletim Sistemi**: Rocky Linux 10
- **Disk**: 50 GB Balanced persistent disk
- **IP**: 34.79.253.58

## Neden Rocky Linux?

### 1. **Enterprise Kararlılığı**
- RHEL (Red Hat Enterprise Linux) klonu - üretim ortamları için tasarlandı
- MLOps production deployment'ları için kritik kararlılık
- Ubuntu'ya göre daha az sık güncelleme = daha az breaking change riski

### 2. **Uzun Dönem Destek (LTS)**
- 10 yıl destek süresi
- Production ML sistemleri yıllarca çalışır - kararlı base gerekir
- Ubuntu LTS 5 yıl (Rocky 2x daha uzun)

### 3. **Enterprise Tooling Uyumluluğu**
- Çoğu enterprise MLOps tool (Kubeflow, MLflow, Airflow) RHEL/CentOS için optimize
- Container orchestration (Kubernetes, OpenShift) için tercih edilen base
- SELinux güvenlik katmanı built-in

### 4. **Production-Ready Defaults**
- Minimal kurulum = daha az attack surface
- Security-first yaklaşım
- MLOps pipeline'ları için ideal starting point

### 5. **Kurumsal Ortamlarda Yaygınlık**
- Çoğu büyük şirket RHEL/Rocky kullanır
- Gerçek dünya MLOps deneyimi için uygun
- CV'de değerli bir skill

## Local vs Cloud VM Farkı

### Local Ortam
- Geliştirme ve test için
- Hızlı iterasyon
- Sınırlı kaynak

### Cloud VM (Production-like)
- Gerçek deployment senaryoları
- Scalability testleri
- 7/24 erişilebilirlik
- Team collaboration için hazır

## Güvenlik Notları
- HTTP/HTTPS trafiği açık
- Firewall kuralları aktif
- SSH key authentication önerilir
- Service account: compute default

## Rocky Linux vs Ubuntu: Komut Farkları

### Package Manager Farkı
| İşlem | Ubuntu (Debian) | Rocky Linux (RHEL) |
|-------|----------------|-------------------|
| Güncelleme | `sudo apt update` | `sudo dnf update` veya `sudo yum update` |
| Paket kurma | `sudo apt install <paket>` | `sudo dnf install <paket>` |
| Paket silme | `sudo apt remove <paket>` | `sudo dnf remove <paket>` |
| Paket arama | `apt search <paket>` | `dnf search <paket>` |
| Sistem upgrade | `sudo apt upgrade` | `sudo dnf upgrade` |

### İlk Kurulum Komutları (Rocky Linux)
```bash
# Sistem güncellemesi
sudo dnf update -y

# Gerekli temel paketler
sudo dnf install -y wget curl git vim nano

# Sistem bilgisi kontrolü
cat /etc/os-release
uname -a
```

## SSH Bağlantısı: MobaXterm vs Google Cloud Console

### MobaXterm (Eğitmenin Yöntemi)
- **Avantajları**:
  - Grafik arayüz ile dosya transferi (SFTP)
  - Split screen terminal
  - X11 forwarding (GUI uygulamalar için)
  - Session kaydetme
  
- **Bağlantı**:
  ```
  Host: 34.79.253.58
  Port: 22
  Username: <kullanıcı-adın>
  ```

### Google Cloud Console SSH (Senin Yöntemin)
- **Avantajları**:
  - Tarayıcıdan direkt erişim
  - SSH key yönetimi otomatik
  - Firewall kuralları otomatik ayarlanır
  - Ekstra tool kurulumu gereksiz
  
- **Kullanım**: VM instance sayfasında "SSH" butonuna tıkla

**Not**: İkisi de aynı makineye bağlanır, tercih meselesi. Cloud Console daha pratik, MobaXterm daha feature-rich.

## Terminal'i Renkli ve Kullanışlı Hale Getirme

### 1. Oh My Zsh (En Popüler - Önerilen)

**Zsh ve Oh My Zsh Kurulumu:**
```bash
# Zsh kurulumu
sudo dnf install -y zsh

# Oh My Zsh kurulumu
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Zsh'i default shell yap
chsh -s $(which zsh)

# Yeniden giriş yap veya:
zsh
```

**Tema Değiştirme:**
```bash
# .zshrc dosyasını düzenle
nano ~/.zshrc

# ZSH_THEME satırını bul ve değiştir:
# ZSH_THEME="robbyrussell"  # default
# ZSH_THEME="agnoster"      # popüler, renkli
# ZSH_THEME="powerlevel10k/powerlevel10k"  # en gelişmiş
```

**Powerlevel10k Teması (Çok Popüler):**
```bash
# Powerlevel10k'yı kur
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

# .zshrc'de temayı değiştir
nano ~/.zshrc
# ZSH_THEME="powerlevel10k/powerlevel10k"

# Kaynağı yenile
source ~/.zshrc

# Konfigürasyon wizard'ı otomatik açılır
p10k configure
```

### 2. Bash için Renkli Prompt (Daha Basit)

**Basit Renkli Bash:**
```bash
# .bashrc dosyasını düzenle
nano ~/.bashrc

# En alta ekle:
export PS1="\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ "

# Kaynağı yenile
source ~/.bashrc
```

**Starship Prompt (Modern, Hızlı):**
```bash
# Starship kurulumu
curl -sS https://starship.rs/install.sh | sh

# Bash için aktifleştir
echo 'eval "$(starship init bash)"' >> ~/.bashrc
source ~/.bashrc

# Zsh için aktifleştir
echo 'eval "$(starship init zsh)"' >> ~/.zshrc
source ~/.zshrc
```

### 3. Syntax Highlighting ve Auto-suggestions

**Zsh için:**
```bash
# Syntax highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# Auto-suggestions
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# .zshrc'de plugins satırını güncelle
nano ~/.zshrc
# plugins=(git zsh-syntax-highlighting zsh-autosuggestions docker)

source ~/.zshrc
```

### 4. Kullanışlı Terminal Araçları

**lsd (Renkli ls komutu):**
```bash
# lsd kurulumu
sudo dnf install -y lsd

# Kullanım
lsd
lsd -la
lsd --tree

# Alias ekle (opsiyonel)
echo "alias ls='lsd'" >> ~/.bashrc
echo "alias ll='lsd -la'" >> ~/.bashrc
source ~/.bashrc
```

**bat (Renkli cat komutu):**
```bash
# bat kurulumu
sudo dnf install -y bat

# Kullanım
bat dosya.txt
bat --style=numbers dosya.py

# Alias ekle
echo "alias cat='bat'" >> ~/.bashrc
source ~/.bashrc
```

**htop (Renkli sistem monitörü):**
```bash
# htop kurulumu
sudo dnf install -y htop

# Kullanım
htop
```

**ncdu (Disk kullanımı görselleştirme):**
```bash
# ncdu kurulumu
sudo dnf install -y ncdu

# Kullanım
ncdu /home
```

### 5. Hızlı Kurulum Paketi (Hepsi Bir Arada)

```bash
# Tüm araçları tek seferde kur
sudo dnf install -y zsh git curl wget htop ncdu vim nano

# Oh My Zsh kur
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Powerlevel10k kur
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k

# Plugins kur
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# .zshrc'yi düzenle
nano ~/.zshrc
# ZSH_THEME="powerlevel10k/powerlevel10k"
# plugins=(git docker zsh-syntax-highlighting zsh-autosuggestions)

# Kaynağı yenile
source ~/.zshrc
```

### Doğrulama Komutları

```bash
# Zsh versiyonu
zsh --version

# Mevcut shell
echo $SHELL

# Oh My Zsh kurulu mu?
ls -la ~/.oh-my-zsh

# Tema kontrolü
echo $ZSH_THEME

# Plugin listesi
echo $plugins
```

### Önerilen Kombinasyon (MLOps için)

```bash
# 1. Oh My Zsh + Powerlevel10k (görsel)
# 2. zsh-syntax-highlighting (komut renklendirme)
# 3. zsh-autosuggestions (otomatik tamamlama)
# 4. htop (sistem izleme)
# 5. Docker plugin (docker komutları için autocomplete)
```

**Sonuç**: Terminal artık renkli, komutlar highlight edilir, otomatik tamamlama çalışır ve git branch'leri görünür!

---
**Not**: Bu setup, local'de öğrenilen MLOps pratiklerini production-like ortamda uygulamak için tasarlandı.
