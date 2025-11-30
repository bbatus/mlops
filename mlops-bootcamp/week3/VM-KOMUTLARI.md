# VM'de GitHub'dan Çekme Komutları

## ✅ GitHub'a Push Edildi!

Repository: https://github.com/bbatus/mlops.git

---

## VM'de Çalıştırılacak Komutlar

### 1. Git Kurulu mu Kontrol Et
```bash
git --version
```

Eğer kurulu değilse:
```bash
sudo dnf install -y git
```

### 2. Home Dizinine Git
```bash
cd ~
```

### 3. Repository'yi Clone Et
```bash
git clone https://github.com/bbatus/mlops.git
```

### 4. Klasöre Gir ve İçeriği Kontrol Et
```bash
cd mlops
ls -la
```

### 5. mlops docs Klasörünü Kontrol Et
```bash
ls -la "mlops docs/"
ls -la "mlops docs/01_conda_jupyter_lab_vscode/02_mlops_docker/"
```

---

## Klasör Yapısı (VM'de)

```
/home/batuhansarihanvbo/
├── mlops/                          # Git repo (YENİ)
│   ├── mlops docs/
│   │   └── 01_conda_jupyter_lab_vscode/
│   │       ├── 02_mlops_docker/    # Docker compose dosyaları
│   │       ├── 03_finalize_jenkins_docker_installation/
│   │       ├── 04_aws/
│   │       └── 05_mlflow/
│   ├── mlops-bootcamp/
│   │   └── week3/
│   │       ├── vm-setup-rocky-linux.md
│   │       ├── docker-kurulum.md
│   │       ├── python-env-yonetimi.md
│   │       └── dosya-transfer-google-cloud.md
│   └── vbo_week1/
└── mlops/                          # Eski klasör (varsa)
    ├── env/
    │   └── mlops-env/
    └── week3/
```

---

## Güncellemeleri Çekmek İçin (Gelecekte)

```bash
# Repo klasörüne git
cd ~/mlops

# En son değişiklikleri çek
git pull origin main
```

---

## Docker Compose Dosyasını Kullanmak İçin

```bash
# Docker compose klasörüne git
cd ~/mlops/"mlops docs"/01_conda_jupyter_lab_vscode/02_mlops_docker/

# İçeriği kontrol et
ls -la

# Docker compose'u çalıştır
docker compose up -d

# Logları izle
docker compose logs -f
```

---

## Hızlı Kısayollar (Opsiyonel)

```bash
# .zshrc'ye ekle
cat >> ~/.zshrc << 'EOF'

# MLOps repo kısayolları
alias mlrepo='cd ~/mlops'
alias mldocker='cd ~/mlops/"mlops docs"/01_conda_jupyter_lab_vscode/02_mlops_docker/'
alias mlupdate='cd ~/mlops && git pull origin main'

EOF

source ~/.zshrc
```

Artık kullanabilirsin:
- `mlrepo` → Repo ana klasörüne git
- `mldocker` → Docker compose klasörüne git
- `mlupdate` → Git'ten güncellemeleri çek

---

## Troubleshooting

### Problem: Git kurulu değil
```bash
sudo dnf install -y git
```

### Problem: Permission denied
```bash
# SSH key ile GitHub'a bağlan (opsiyonel)
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
cat ~/.ssh/id_rsa.pub
# Bu key'i GitHub → Settings → SSH Keys'e ekle
```

### Problem: Klasör zaten var
```bash
# Eski klasörü yedekle
mv ~/mlops ~/mlops_backup

# Tekrar clone et
git clone https://github.com/bbatus/mlops.git
```

---

## Sonraki Adımlar

1. ✅ VM'de `git clone` çalıştır
2. ✅ Docker compose dosyalarını kontrol et
3. ✅ Docker compose'u başlat
4. 🔜 MLflow, Gitea, Jenkins kurulumlarına devam et

**Hazırsın!** 🚀
