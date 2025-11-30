# Create conda virtual environment

## 1. Create an environment
`  conda create --name ds-dev python=3.12 ` 

` Proceed ([y]/n)? y ` 

### 1.1. Mac Users use th instructions below this page

### 1.2. Alternatively use uv
```
# install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

uv init uv_env
Initialized project `uv-env` at `/home/train/vbo-mlops/uv_env`
```
## 2. Activate env
` conda activate ds-dev `  

### 2.1. List conda environments
```commandline
conda env list
```
- Output
```
#
base                  *  /home/train/miniconda3
ds-dev                   /home/train/miniconda3/envs/ds-dev
```
## 3. Install basic datascience packages

```commandline
cat<<EOF | tee requirements.txt
pandas==2.2.3
jupyterlab==4.5.0
scikit-learn==1.5.2
mlflow==3.5.1
boto3==1.35.54
langchain==0.3.27
langchain-google-genai>=0.1.0
EOF

python -m pip install -r requirements.txt
```

### 3.1. uv install requirements and activate venv
```bash
uv --project uv_env/ add -r 01_conda_jupyter_lab_vscode/requirements.txt

source uv_env/.venv/bin/activate
```

## 4. Start Jupyter Lab
- Start
`  jupyter lab --ip 0.0.0.0 --port 8990 ` 

- Copy link
http://127.0.0.1:8990/lab?token=d137758b0014e1745f1946a91083e1256be7ff9051a4a95c


## 5. Port Forwarding
- Virtualbox
![Port Forwarding](images/01_jupyterlab_virtualbox_port_forward.png 'Port Forwarding')

## 6. Open Jupyter lab and create notebook
- Paste the notebook link to browser
- Create a notebook  
![ Create a notebook](images/02_create_notebook.png ' Create a notebook')

### 6.1. Import test

![](images/03_jupyter_imports.png)

---

### 6.2. Close jupyter lab
- On jupyter lab terminal press `Ctrl+C`

## 7. Removing environments
```commandline
conda env remove --name <env-name>
```

# macOS
## Update & upgrade homebrew
```commandline
brew update

brew upgrade
```

## Install python 3.12
```commandline
brew install python@3.12
```

## Alias for python3
```commandline
vi ~/.zshrc


alias python='/opt/homebrew/bin/python3.12'
alias python3='/opt/homebrew/bin/python3.12'


source ~/.zshrc
```
### Open PyCharm
- Select interpreter
- Open terminal
- venv will appear on the left like this `(venv) erkansirin@192 vbo-mlops %`
- Create requirements.txt like step 3
  - `pip install -r requirements.txt`
- Step 4 jupyter lab --ip 0.0.0.0 --port 8990
- Remove venv: deactivate then rm -rf venv