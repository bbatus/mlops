# Python Environment Yönetimi - Conda vs UV

## Neden Virtual Environment?

### Problem: Paket Çakışmaları
```
Proje A → pandas 1.5.0 gerekiyor
Proje B → pandas 2.2.0 gerekiyor
❌ Aynı sistemde ikisi birden çalışamaz!
```

### Çözüm: İzole Ortamlar
```
✅ Proje A → kendi env'i → pandas 1.5.0
✅ Proje B → kendi env'i → pandas 2.2.0
```

**MLOps'ta Kritik Çünkü:**
- Her model farklı kütüphane versiyonları kullanabilir
- Production ve development ortamları farklı olabilir
- Reproducibility (tekrar üretilebilirlik) için gerekli
- Docker container'larına taşımak için temiz başlangıç

---

## Conda vs UV Karşılaştırması

| Özellik | Conda | UV |
|---------|-------|-----|
| **Hız** | Yavaş (dakikalar) | ⚡ Çok hızlı (saniyeler) |
| **Disk Kullanımı** | Yüksek (~500MB+) | Düşük (~50MB) |
| **Python Versiyonu** | Kendi Python'unu kurar | Sistem Python'unu kullanır |
| **Paket Sayısı** | Çok geniş (conda-forge) | PyPI (pip ile aynı) |
| **Popülerlik** | Eski, yaygın | 🔥 Yeni, trend |
| **Data Science** | Optimize edilmiş | Genel amaçlı |
| **Öğrenme Eğrisi** | Kolay | Çok kolay |

### Conda Ne Zaman?
- Bilimsel hesaplama (NumPy, SciPy native dependencies)
- Eski projeler (legacy)
- R, Julia gibi diğer dilleri de kullanıyorsan

### UV Ne Zaman? (Önerilen)
- Modern Python projeleri
- Hız önemli
- Minimal disk kullanımı istiyorsan
- **MLOps production ortamları** (Docker'da daha verimli)

---

## UV Nedir?

**UV** = Ultra-fast Python package installer (Rust ile yazılmış)

### Avantajları:
1. **10-100x daha hızlı** pip ve conda'dan
2. **Tek tool** - hem environment hem package manager
3. **Modern** - 2024'te çıktı, best practices built-in
4. **Deterministic** - her zaman aynı versiyonları kurar
5. **Lock file** - tam reproducibility

### Somut Örnek:
```bash
# Conda ile
conda create -n myenv python=3.12
conda activate myenv
pip install pandas scikit-learn mlflow
# ⏱️ Süre: ~3-5 dakika

# UV ile
uv venv myenv
source myenv/bin/activate
uv pip install pandas scikit-learn mlflow
# ⏱️ Süre: ~10-20 saniye
```

---

## Requirements.txt Nedir?

**requirements.txt** = Projenin ihtiyaç duyduğu tüm Python paketlerinin listesi

### Neden Kullanılır?
1. **Reproducibility** - Aynı ortamı başka yerde tekrar oluştur
2. **Collaboration** - Takım arkadaşların aynı paketleri kullansın
3. **Deployment** - Production'a aynı versiyonları deploy et
4. **Documentation** - Proje hangi paketlere bağımlı?

### Örnek requirements.txt:
```txt
pandas==2.2.3          # Veri manipülasyonu
jupyterlab==4.5.0      # Interactive notebook
scikit-learn==1.5.2    # Machine learning
mlflow==3.5.1          # ML experiment tracking
boto3==1.35.54         # AWS SDK (S3, SageMaker)
langchain==0.3.27      # LLM framework
langchain-google-genai>=0.1.0  # Google AI entegrasyonu
```

### Versiyon Notasyonları:
```txt
pandas==2.2.3          # Tam versiyon (önerilen)
pandas>=2.2.0          # Minimum versiyon
pandas~=2.2.0          # 2.2.x (minor updates)
pandas                 # En son versiyon (riskli!)
```

---

## Paket Açıklamaları

### 1. pandas (2.2.3)
**Ne İşe Yarar:** Veri manipülasyonu ve analizi
```python
import pandas as pd
df = pd.read_csv('data.csv')
df.groupby('category').mean()
```
**MLOps'ta:** Feature engineering, data preprocessing

### 2. jupyterlab (4.5.0)
**Ne İşe Yarar:** Interactive Python notebook
```python
# Tarayıcıda kod yaz, sonuçları anında gör
# Veri görselleştirme, EDA için ideal
```
**MLOps'ta:** Experiment, prototyping, data exploration

### 3. scikit-learn (1.5.2)
**Ne İşe Yarar:** Machine learning kütüphanesi
```python
from sklearn.ensemble import RandomForestClassifier
model = RandomForestClassifier()
model.fit(X_train, y_train)
```
**MLOps'ta:** Model training, evaluation, preprocessing

### 4. mlflow (3.5.1)
**Ne İşe Yarar:** ML experiment tracking ve model registry
```python
import mlflow
mlflow.log_param("learning_rate", 0.01)
mlflow.log_metric("accuracy", 0.95)
mlflow.sklearn.log_model(model, "model")
```
**MLOps'ta:** Experiment tracking, model versioning, deployment

### 5. boto3 (1.35.54)
**Ne İşe Yarar:** AWS SDK - S3, SageMaker, Lambda
```python
import boto3
s3 = boto3.client('s3')
s3.upload_file('model.pkl', 'my-bucket', 'models/model.pkl')
```
**MLOps'ta:** Cloud storage, model deployment, data pipeline

### 6. langchain (0.3.27)
**Ne İşe Yarar:** LLM application framework
```python
from langchain.llms import OpenAI
llm = OpenAI(temperature=0.7)
response = llm("Explain MLOps")
```
**MLOps'ta:** LLM-based applications, RAG systems

### 7. langchain-google-genai
**Ne İşe Yarar:** Google Gemini entegrasyonu
```python
from langchain_google_genai import ChatGoogleGenerativeAI
llm = ChatGoogleGenerativeAI(model="gemini-pro")
```
**MLOps'ta:** Google AI modellerini kullanma

---

## Kurulum Adımları (UV ile - Önerilen)

### 1. UV Kurulumu
```bash
# UV'yi kur (tek seferlik)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Shell'i yenile
source ~/.bashrc  # veya ~/.zshrc

# Doğrulama
uv --version
```

### 2. Proje Klasör Yapısı Oluştur
```bash
# Ana klasör
mkdir -p ~/mlops

# Week 3 klasörü
mkdir -p ~/mlops/week3

# Environment klasörü
mkdir -p ~/mlops/env

# Week 3'e git
cd ~/mlops/week3
```

### 3. Virtual Environment Oluştur
```bash
# UV ile venv oluştur (Python 3.12)
cd ~/mlops/env
uv venv mlops-env --python 3.12
```

### 4. Environment'ı Aktifleştir
```bash
# Environment'ı aktifleştir
source ~/mlops/env/mlops-env/bin/activate

# Aktif olduğunu görmek için
which python
# Çıktı: /home/batuhansarihanvbo/mlops/env/mlops-env/bin/python

# Hangi environment aktif?
echo $VIRTUAL_ENV
# Çıktı: /home/batuhansarihanvbo/mlops/env/mlops-env
```

### 5. requirements.txt Oluştur
```bash
cd ~/mlops/week3

# requirements.txt dosyası oluştur
cat << EOF > requirements.txt
pandas==2.2.3
jupyterlab==4.5.0
scikit-learn==1.5.2
mlflow==3.5.1
boto3==1.35.54
langchain==0.3.27
langchain-google-genai>=0.1.0
EOF
```

### 6. Paketleri Kur
```bash
# UV ile hızlı kurulum
uv pip install -r requirements.txt

# Alternatif: Normal pip (daha yavaş)
pip install -r requirements.txt
```

### 7. Kurulum Doğrulama
```bash
# Python versiyonu
python --version
# Beklenen: Python 3.12.x

# Paket listesi
uv pip list
# veya
pip list

# Spesifik paket kontrolü
python -c "import pandas; print(pandas.__version__)"
python -c "import mlflow; print(mlflow.__version__)"
python -c "import sklearn; print(sklearn.__version__)"
```

---

## Jupyter Lab Başlatma

### 1. Jupyter Lab'i Başlat
```bash
# Environment aktif olmalı!
jupyter lab --ip 0.0.0.0 --port 8990

# Çıktıda link görünecek:
# http://127.0.0.1:8990/lab?token=xxxxx
```

### 2. Google Cloud'da Port Açma (Gerekirse)
```bash
# Firewall kuralı ekle
gcloud compute firewall-rules create jupyter-lab \
  --allow tcp:8990 \
  --source-ranges 0.0.0.0/0 \
  --description "Jupyter Lab access"
```

### 3. Tarayıcıdan Erişim
```
# Local'den (SSH tunnel ile)
http://localhost:8990/lab?token=xxxxx

# Direkt VM IP'den (güvenli değil, sadece test için)
http://34.79.253.58:8990/lab?token=xxxxx
```

### 4. Test Notebook
```python
# Yeni notebook oluştur ve test et
import pandas as pd
import sklearn
import mlflow

print(f"Pandas: {pd.__version__}")
print(f"Scikit-learn: {sklearn.__version__}")
print(f"MLflow: {mlflow.__version__}")

# Basit test
df = pd.DataFrame({'a': [1, 2, 3], 'b': [4, 5, 6]})
print(df)
```

---

## Yaygın Komutlar

### Environment Yönetimi
```bash
# Environment oluştur
uv venv myenv --python 3.12

# Aktifleştir
source myenv/bin/activate

# Deaktive et
deactivate

# Sil
rm -rf myenv
```

### Paket Yönetimi
```bash
# Tek paket kur
uv pip install pandas

# requirements.txt'den kur
uv pip install -r requirements.txt

# Paket güncelle
uv pip install --upgrade pandas

# Paket kaldır
uv pip uninstall pandas

# Tüm paketleri listele
uv pip list

# requirements.txt oluştur (mevcut paketlerden)
uv pip freeze > requirements.txt
```

### Jupyter Lab
```bash
# Arka planda başlat (önerilen - terminal serbest kalır)
cd ~/mlops/env/mlops-env
nohup jupyter lab --ip 0.0.0.0 --port 8990 > jupyter.log 2>&1 &

# Token'ı görmek için
sleep 3
cat jupyter.log | grep "http://127"

# Çalışan Jupyter'ları listele
jupyter lab list

# Log'u canlı izle
tail -f ~/mlops/env/mlops-env/jupyter.log

# Durdur
pkill -f jupyter-lab
```

---

## Klasör Yapısı Özeti

```
/home/batuhansarihanvbo/
├── mlops/
│   ├── env/
│   │   └── mlops-env/          # Virtual environment
│   │       ├── bin/            # Python, pip, jupyter
│   │       ├── lib/            # Kurulu paketler
│   │       ├── jupyter.log     # Jupyter log dosyası
│   │       └── pyvenv.cfg      # Environment config
│   └── week3/
│       ├── requirements.txt     # Paket listesi
│       ├── notebooks/           # Jupyter notebook'lar (oluşturulacak)
│       ├── data/                # Veri dosyaları (oluşturulacak)
│       └── models/              # Eğitilmiş modeller (oluşturulacak)
```

---

## Troubleshooting

### Problem: uv komutu bulunamıyor
```bash
# Çözüm: PATH'e ekle (Zsh kullanıyorsan)
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Bash kullanıyorsan
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

### Problem: Python 3.12 bulunamıyor
```bash
# Rocky Linux'ta Python 3.12 kur
sudo dnf install -y python3.12

# Doğrula
python3.12 --version
```

### Problem: Jupyter Lab açılmıyor
```bash
# Port kullanımda mı kontrol et
sudo netstat -tulpn | grep 8990

# Farklı port dene
jupyter lab --ip 0.0.0.0 --port 8991
```

### Problem: Import hatası
```bash
# Environment aktif mi kontrol et
which python
# /home/.../mlops-env/bin/python olmalı

# Paketi tekrar kur
uv pip install --force-reinstall pandas
```

---

## Hızlı Kısayollar (Alias)

Jupyter yönetimini kolaylaştırmak için alias'lar ekle:

```bash
# .zshrc'ye ekle
cat >> ~/.zshrc << 'EOF'

# Jupyter kısayolları
alias jstart='cd ~/mlops/env/mlops-env && nohup jupyter lab --ip 0.0.0.0 --port 8990 > jupyter.log 2>&1 & sleep 2 && cat jupyter.log | grep token'
alias jstop='pkill -f jupyter-lab && echo "Jupyter durduruldu!"'
alias jtoken='cat ~/mlops/env/mlops-env/jupyter.log | grep "http://127" | tail -1'
alias jlog='tail -f ~/mlops/env/mlops-env/jupyter.log'
alias jlist='jupyter lab list'

# Environment kısayolları
alias mlenv='source ~/mlops/env/mlops-env/bin/activate'
alias mlweek3='cd ~/mlops/week3'

EOF

# Yükle
source ~/.zshrc
```

**Kullanım:**
- `jstart` → Jupyter'ı başlat ve token'ı göster
- `jstop` → Jupyter'ı durdur
- `jtoken` → Token'ı tekrar göster
- `jlist` → Çalışan Jupyter'ları listele
- `jlog` → Log'u canlı izle
- `mlenv` → Environment'ı aktifleştir
- `mlweek3` → Week3 klasörüne git

---

## Sonraki Adımlar

- ✅ UV kuruldu
- ✅ Virtual environment oluşturuldu (`~/mlops/env/mlops-env`)
- ✅ Paketler kuruldu
- ✅ Jupyter Lab arka planda çalışıyor
- ✅ Jupyter log: `~/mlops/env/mlops-env/jupyter.log`
- 🔜 İlk ML experiment
- 🔜 MLflow tracking
- 🔜 Model deployment

**Not**: Her yeni proje için yeni bir virtual environment oluştur!
