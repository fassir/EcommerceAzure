<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:1F9BD4,50:2E75B6,100:16265F&height=200&section=header&text=EcommerceAzure&fontSize=48&fontColor=ffffff&fontAlignY=38&desc=E-commerce+completo+com+Streamlit+·+Azure+Blob+Storage+·+Azure+SQL+Database&descAlignY=58&descSize=16&animation=fadeIn" />

<br/>

[![Python](https://img.shields.io/badge/Python-3.10%2B-1F9BD4?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Azure](https://img.shields.io/badge/Azure-Cloud-2E75B6?style=for-the-badge&logo=microsoftazure&logoColor=white)](https://azure.microsoft.com)
[![Streamlit](https://img.shields.io/badge/Streamlit-App-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white)](https://streamlit.io)
[![SQL](https://img.shields.io/badge/Azure_SQL-Serverless-16265F?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)](https://azure.microsoft.com/services/azure-sql)
[![Status](https://img.shields.io/badge/Status-Ativo-1F9BD4?style=for-the-badge)](https://github.com/fassir/EcommerceAzure)

</div>

---

## 🛒 Sobre o Projeto

<div align="center">

> *"Construir um e-commerce na nuvem é exercitar o melhor da engenharia moderna: escalabilidade, persistência e experiência do usuário em harmonia."*

</div>

O **EcommerceAzure** é uma aplicação web completa de e-commerce desenvolvida com **Streamlit** e totalmente integrada ao ecossistema **Microsoft Azure**. O projeto demonstra como conectar uma interface Python moderna com serviços de armazenamento de arquivos (**Azure Blob Storage**) e banco de dados relacional (**Azure SQL Database serverless**).

É um case completo e funcional de desenvolvimento cloud-native com foco em boas práticas: separação de responsabilidades, variáveis de ambiente para segredos, e uma UX limpa para cadastro e listagem de produtos.

### 🌟 Destaques do Projeto

| Característica | Detalhe |
|---|---|
| 🖼️ **Upload de imagens** | Fotos de produtos armazenadas no Azure Blob Storage |
| 🗄️ **Banco serverless** | Azure SQL Database com escalonamento automático |
| 🎨 **Interface moderna** | Streamlit com componentes interativos |
| 🔐 **Segurança** | Credenciais via `.env`, nunca expostas no código |
| ☁️ **100% Cloud** | Infraestrutura totalmente provisionada no Azure |

---

## ☁️ Arquitetura Azure

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         ARQUITETURA ECOMMERCEAZURE                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  USUÁRIO                APLICAÇÃO               AZURE                   │
│                                                                         │
│  🌐 Browser    ──►   Streamlit UI    ──►  ┌─────────────────────┐     │
│                       main.py              │  Resource Group      │     │
│                                            │                     │     │
│  📸 Foto       ──►   upload_blob()  ──►  │  Azure Blob Storage  │     │
│  produto              (imagem)             │  Contêiner: produtos │     │
│                                            │                     │     │
│  📝 Dados      ──►  insert_product() ──►  │  Azure SQL Database  │     │
│  produto              (metadados)          │  Serverless          │     │
│                                            │                     │     │
│  🔍 Listagem   ──►   list_products() ──►  │  Consulta + CDN URL  │     │
│                       (catalogo)           └─────────────────────┘     │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### 🗂️ Serviços Azure Utilizados

| Serviço Azure | Tipo | Uso no projeto |
|---|---|---|
| **Resource Group** | Organização | Agrupa todos os recursos do projeto |
| **Azure Blob Storage** | Armazenamento | Upload e serving das imagens de produtos |
| **Azure SQL Database** | Banco de dados | Armazenamento dos dados dos produtos (serverless) |
| **Storage Account** | Infraestrutura | Conta de armazenamento que hospeda o Blob |

---

## 🛠️ Stack de Tecnologias

<div align="center">

[![My Skills](https://skillicons.dev/icons?i=python,azure,docker&theme=dark)](https://skillicons.dev)

</div>

<div align="center">

![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?style=flat-square&logo=streamlit&logoColor=white)
![Azure](https://img.shields.io/badge/Azure-0078D4?style=flat-square&logo=microsoftazure&logoColor=white)
![Azure SQL](https://img.shields.io/badge/Azure_SQL-CC2927?style=flat-square&logo=microsoftsqlserver&logoColor=white)
![Azure Blob](https://img.shields.io/badge/Blob_Storage-0078D4?style=flat-square&logo=microsoftazure&logoColor=white)
![pyodbc](https://img.shields.io/badge/pyodbc-2E75B6?style=flat-square&logo=python&logoColor=white)
![dotenv](https://img.shields.io/badge/python--dotenv-ECD53F?style=flat-square&logo=dotenv&logoColor=black)

</div>

| Biblioteca | Versão | Função |
|---|---|---|
| `streamlit` | ≥ 1.30 | Interface web interativa |
| `azure-storage-blob` | ≥ 12.0 | Upload/download de imagens no Blob Storage |
| `pyodbc` | ≥ 4.0 | Conexão com Azure SQL Database |
| `python-dotenv` | ≥ 1.0 | Carregamento seguro de variáveis de ambiente |
| `Pillow` | ≥ 9.0 | Pré-processamento de imagens |

---

## 🚀 Instalação e Configuração

<details>
<summary><b>☁️ 1. Provisione a infraestrutura Azure</b></summary>

```bash
# 1. Crie o Resource Group
az group create \
  --name rg-ecommerce \
  --location brazilsouth

# 2. Crie a conta de armazenamento
az storage account create \
  --name stgecommercefassir \
  --resource-group rg-ecommerce \
  --location brazilsouth \
  --sku Standard_LRS

# 3. Crie o contêiner de produtos
az storage container create \
  --name produtos \
  --account-name stgecommercefassir \
  --public-access blob

# 4. Crie o Azure SQL Database (serverless)
az sql server create \
  --name sql-ecommerce-fassir \
  --resource-group rg-ecommerce \
  --location brazilsouth \
  --admin-user sqladmin \
  --admin-password SuaSenhaForte@123

az sql db create \
  --resource-group rg-ecommerce \
  --server sql-ecommerce-fassir \
  --name db-ecommerce \
  --edition GeneralPurpose \
  --compute-model Serverless \
  --family Gen5 \
  --capacity 1
```

</details>

<details>
<summary><b>📦 2. Clone e instale dependências</b></summary>

```bash
# Clone o repositório
git clone https://github.com/fassir/EcommerceAzure.git
cd EcommerceAzure

# Crie e ative ambiente virtual
python -m venv venv
source venv/bin/activate       # Linux/macOS
# venv\Scripts\activate        # Windows

# Instale dependências
pip install -r requirements.txt
```

</details>

<details>
<summary><b>🔑 3. Configure as variáveis de ambiente</b></summary>

Crie um arquivo `.env` na raiz do projeto com base no `.env.example`:

```bash
# .env — Configurações Azure

# Azure Blob Storage
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;AccountName=...
AZURE_BLOB_CONTAINER=produtos

# Azure SQL Database
SQL_SERVER=sql-ecommerce-fassir.database.windows.net
SQL_DATABASE=db-ecommerce
SQL_USERNAME=sqladmin
SQL_PASSWORD=SuaSenhaForte@123
```

</details>

<details>
<summary><b>▶️ 4. Execute a aplicação</b></summary>

```bash
# Inicie o Streamlit
streamlit run main.py

# A aplicação abrirá em: http://localhost:8501
```

</details>

---

## ⚙️ Funções Principais

### `upload_blob(arquivo, nome_arquivo)`

```python
def upload_blob(arquivo: bytes, nome_arquivo: str) -> str:
    """
    Faz upload de uma imagem para o Azure Blob Storage.
    
    Args:
        arquivo: conteúdo binário da imagem
        nome_arquivo: nome do arquivo no contêiner
    
    Returns:
        str: URL pública da imagem no Blob Storage
    """
    blob_service = BlobServiceClient.from_connection_string(
        os.getenv("AZURE_STORAGE_CONNECTION_STRING")
    )
    container = blob_service.get_container_client(
        os.getenv("AZURE_BLOB_CONTAINER")
    )
    container.upload_blob(name=nome_arquivo, data=arquivo, overwrite=True)
    return f"https://{ACCOUNT_NAME}.blob.core.windows.net/produtos/{nome_arquivo}"
```

### `insert_product(nome, preco, descricao, imagem_url)`

```python
def insert_product(nome: str, preco: float, descricao: str, imagem_url: str):
    """Insere um novo produto no Azure SQL Database."""
    conn = pyodbc.connect(get_connection_string())
    cursor = conn.cursor()
    cursor.execute(
        "INSERT INTO Produtos (nome, preco, descricao, imagem_url) VALUES (?, ?, ?, ?)",
        nome, preco, descricao, imagem_url
    )
    conn.commit()
```

### `list_products()`

```python
def list_products() -> list[dict]:
    """Retorna todos os produtos cadastrados no banco de dados."""
    conn = pyodbc.connect(get_connection_string())
    cursor = conn.cursor()
    cursor.execute("SELECT id, nome, preco, descricao, imagem_url FROM Produtos")
    return [
        {"id": row[0], "nome": row[1], "preco": row[2],
         "descricao": row[3], "imagem_url": row[4]}
        for row in cursor.fetchall()
    ]
```

---

## ✅ Funcionalidades

| # | Funcionalidade | Descrição | Status |
|---|---|---|---|
| 1 | ➕ **Cadastro de produto** | Formulário com nome, preço, descrição e imagem | ✅ Implementado |
| 2 | 🖼️ **Upload de imagem** | Salva foto do produto no Azure Blob Storage | ✅ Implementado |
| 3 | 💾 **Persistência SQL** | Dados salvos no Azure SQL Database serverless | ✅ Implementado |
| 4 | 📋 **Listagem de produtos** | Exibe catálogo com imagens e preços | ✅ Implementado |
| 5 | 🔗 **URL pública de imagem** | CDN do Azure para serving de imagens | ✅ Implementado |
| 6 | 🔐 **Segurança** | Credenciais via `.env`, sem secrets no código | ✅ Implementado |
| 7 | ☁️ **Serverless SQL** | Auto-pause/resume no Azure SQL serverless | ✅ Implementado |
| 8 | 🎨 **UI Streamlit** | Interface responsiva sem necessidade de HTML/CSS | ✅ Implementado |

---

## 📁 Estrutura de Arquivos

```
EcommerceAzure/
│
├── 📄 main.py                   # Aplicação principal (Streamlit UI)
├── 📄 blob_service.py           # Funções: upload_blob()
├── 📄 db_service.py             # Funções: insert_product(), list_products()
├── 📄 config.py                 # Carregamento de variáveis de ambiente
│
├── 📄 requirements.txt          # Dependências do projeto
├── 📄 .env.example              # Modelo de variáveis de ambiente (sem segredos)
├── 📄 .gitignore                # Ignora .env e dados sensíveis
└── 📄 README.md                 # Documentação
```

---

## 👨‍💻 Autor

<div align="center">

| | |
|---|---|
| **Nome** | Fabio Piassi |
| **Formação** | Física · Ciência de Dados · Cloud · DevSecOps |
| **Especialidade** | Azure · Python · Aplicações Cloud-Native |
| **Localização** | Volta Redonda — RJ 🇧🇷 |
| **GitHub** | [@fassir](https://github.com/fassir) |

[![GitHub](https://img.shields.io/badge/GitHub-fassir-1F9BD4?style=for-the-badge&logo=github&logoColor=white)](https://github.com/fassir)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Fabio_Piassi-2E75B6?style=for-the-badge&logo=linkedin&logoColor=white)](https://linkedin.com/in/fassir)

</div>

---

<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=0:16265F,50:2E75B6,100:1F9BD4&height=120&section=footer&fontSize=14&fontColor=ffffff&text=EcommerceAzure+·+by+Fabio+Piassi&fontAlignY=65" />

*"A nuvem não é o futuro — é o presente. E esse projeto está nela."*

</div>
