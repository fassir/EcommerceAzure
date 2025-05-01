# EcommerceAzure
Neste projeto é criado um e-commerce com site e associado a um banco de dados blob da Microsoft Azure. Para a construção do projeto é necessário os seguintes requisitos em seu computador:
* Visual Studio Code
* Azure Data Studio
* Python 3.x.x
  * Streamlit
  * azure-storage-blob
  * pymssql
  * dotenv

Os arquivos serão disponibilizados para melhor compreensão dos procedimentos realizados, mas seus conteúdos também são mostrados de acordo com a necessidade de uso.
## Criando um Resource Group e SQL Database
Conecte ao portal Azure e pesquise por "grupo de recursos"
![alt text](image.png)
Selecione a opção "Criar"
![alt text](image-1.png)
Escreva o nome do grupo de recursos e selecione "revisar e criar" e após isto a opção criar.
![alt text](image-2.png)
Na lista de grupo de recursos, selecione o grupo recém criado
![alt text](image-4.png)
e após isto selecione a opção "criar"
![alt text](image-3.png)
Pesquise por sql e selecione o "Azure SQL"
![alt text](image-5.png)
Aperte a opção "criar"
![alt text](image-6.png)
e escolha o plano Banco de dados individual
![alt text](image-7.png)
Selecione o grupo de recurso, digite um nome de database conforme as regras e após isto, selecione a opção "criar novo" para criar um novo servidor
![alt text](image-8.png)
Ao criar um servidor de banco de dados, digite o nome do servidor, a localização disponível para a criação em sua conta, selecione o método de autentificação "usar autentificação SQL e Microsoft Entra, defina o administrador com sua conta azure, digite o usuário e senha de acordo com os requisitos de segurança e aperte ok
![alt text](image-9.png)
É possível alterar o banco de dados para a opção serverless, diminuindo os custo da aplicação. Para isto selecione "configurar banco de dados"
![alt text](image-10.png)
e selecione a opção "sem servidor". Nesta mesma janela habilite o atraso de pausa automático e configure para uma hora afim de reduzir os custos desnecessários do serviço executando em tempos ociosos. Clique em aplicar
![alt text](image-11.png)
Após isto, clique em "revisar + criar". Após isto selecione a opção criar.

## Criando uma conta de armazenamento
Volte ao menu inicial e crie um novo recurso. Selecione a opção conta de armazenamento. Caso não esteja aparecendo, utilize a pesquisa
![alt text](image-12.png)
Digite o nome de usuário da conta de armazenamento e Selecione o grupo de recursos, digite o nome da conta de armazenamento, selecione a região disponível para conta, selecione o armazenamento de blobs do azure ou azure data lake storage gen 2 e selecione a redundancia local e clique examinar + criar
![alt text](image-13.png)
Selecione as opções avançadas e marque a opção "Permitir a habilitação de acesso anônimo em contêineres individuais" e selecione "examinar + criar"
![alt text](image-14.png)
Após o serviço validar as opções, clique em criar. Precisamos criar agora um container que nos habilite a acessar o banco de dados.

Acesse a conta de armazenamento e navegue pelo menu a esquerda nas opções "armazenamento de dados" > "contêineres"
 ![alt text](image-15.png)
 Selecione a opção "criar", digite o nome do contêiner, selecione a opção "Blob (acesso de leitura anônimosomente para blobs)" e aperte criar na opção inferior
 ![alt text](image-16.png)
è necessário agora localizar a chave de acesso deste novo contêiner. Para isso, no menu à esquerda selecione a opção "Segurança + rede" > "Chaves de acesso" 
![alt text](image-17.png)
Selecione "mostrar" na cadeia de conexão, copie e cole em um documento novo para criar o acesso ao banco de dados do contêiner.

## Configurando o banco de dados e criando a tabela de produtos
Após criar o contêiner podemos configurá-lo para inserir o banco de dados acessá-lo. Selecione a opção "visão geral" e selecione acesse o grupo de recursos
![alt text](image-18.png)
Na tabela de recursos, acesse o Banco de dados SQL
![alt text](image-19.png)
Vá na opção "Configurações" > Cadeias de conexão e copie para o arquivo o servidor de acesso ao banco de dados (caso seja a porta padrão do banco de dados - 1433 - não é necessário copiá-la no arquivo). Após isto, copie o database localizado no segmento "Catalog"
![alt text](image-20.png)
Precisamos agora configurar as configurações de rede para liberar o acesso do firewall do conteiner para nosso acesso. No menu lateral selecione a opção "Segurança" > "Rede". Em seguida, selecione a opção "Acesso privado" e depois selecione a opção "Adicionar o endereço IPv4 do cliente e salve as novas configurações.
![alt text](image-21.png)
Vamos verificar a conexão com o banco de dados usando o azure data studio. Selecione a opção "new" > new connection"
![alt text](image-22.png)
Insira seus dados e conecte-se ao banco de dados
![alt text](image-23.png)
* Caso não tenha conectado verifique se o serviço de banco de dados está online e se as regras de firewall do passo anterior está correta.

Uma vez conectado, aperte com o batão direito no banco de dados e em seguida em "new query"
![alt text](image-24.png)
Crie a tabela Produtos com o seguinte comando 
```
CREATE TABLE dbo.Produtos (
    id INT IDENTITY(1,1) PRIMARY KEY,
    nome NVARCHAR(255),
    descricao NVARCHAR(MAX),
    preco DECIMAL(18,2),
    imagem_url NVARCHAR(2083)
)
```
e aperte run.

Vamos agora necessitar criar arquivos auxiliares para a inserção utilizando python. O primeiro arquivo será chamado `requirements.txt` que armazenará os nomes dos pacotes a serem instalados com o pip. Caso tenha instalado os pacotes pode desconsiderar este passo.

Crie um arquivo chamado `requirements.txt` e escreva em seu conteúdo
```
streamlit
azure-storage-blob
pymssql
dotenv
```
uma vez salvo em seu computador, escreva o comando pip no terminal
```pip install -r requirements.txt```
e pressione enter

O proximo passo é escrever o arquivo `.env` com as conexões ao banco de dados
```
BLOB_CONNECTION_STRING = "<Conexão_ao_servidor_blob>"
BLOB_CONTAINER_NAME = "<nome_do_contêiner>"
BLOB_ACCOUNT_NAME = "<nome_da_conta_de_usuario>"

SQL_SERVER = "Conexão_com_o_banco_de_dados"
SQL_DATABASE = "Nome_do_banco_de_dados"
SQL_USER = "<Nome_do_usuário>"
SQL_PASSWORD = "<Senha_do_usuário>"
```

Com ambos arquivos realizados, será usado um arquivo em python `main.py` que criará uma pagina usando o streamlit que conterá todos os campos a serem preenchidos. Copie o seguinte código:
```
import streamlit as st
from azure.storage.blob import BlobServiceClient
import os
import pymssql
import uuid
import json
from dotenv import load_dotenv

load_dotenv()
BlobConnectionString = os.getenv("BLOB_CONNECTION_STRING")
blobContainerName = os.getenv("BLOB_CONTAINER_NAME")
blobaccountName = os.getenv("BLOB_ACCOUNT_NAME")

SQL_SERVER = os.getenv("SQL_SERVER")
SQL_DATABASE = os.getenv("SQL_DATABASE")
SQL_USER = os.getenv("SQL_USER")
SQL_PASSWORD = os.getenv("SQL_PASSWORD")

st.title('Cadastro de Produtos')

product_name = st.text_input("Nome do Produto")
product_price = st.number_input("Preço do Produto", min_value = 0.0, format='%.2f') 
product_description = st.text_area("Descricao do Produto")
product_image = st.file_uploader('Imagem do produto',type = ['jpg', 'png', 'jpeg'])

if st.button('Salvar Produto'):
    return_message = 'Produto salvo com sucesso'

st.header('Produtos Cadastrados')

if st.button('Listar Produtos'):
    return_message = 'Produtos listados com sucesso'
```
e após isto execute o arquivo com o comando 
```
python main.py
```
Abrirá uma página em seu navegador padrão da seguinte forma:
![alt text](image-25.png)

Mais adiante será incorporado ao código a parte em que o programa exportará os dados ao banco de dados.
## Implementando o salvamento de imagens no Blob Storage
Para realizar este processo, é necessário adicionar mais uma função ao código anterior:
```
def upload_blob(file):
    blob_service_client = BlobServiceClient.from_connection_string(BlobConnectionString)
    container_client = blob_service_client.get_container_client(blobContainerName)
    blob_name = str(uuid.uuid4())+file.name
    blob_client = container_client.get_blob_client(blob_name)
    blob_client.upload_blob(file.read(), overwrite=True)
    image_url= f"https://{blobaccountName}.blob.core.windows.net/{blobContainerName}/{blob_name}"
    return image_url
```
Esta seção do código permite que a imagem possua um nome distinto e caso já exista no banco de dados, sobreescreva o arquivo.

## Finalizando o projeto
Finalizando o código temos a parte em que o programa exportará os dados para o banco de dados
```
def insert_product(product_name, product_price, product_description, product_image):
    try:
        image_url = upload_blob(product_image)
        conn = pymssql.connect(server=SQL_SERVER,user=SQL_USER,password=SQL_PASSWORD,database=SQL_DATABASE)
        cursor = conn.cursor()
        cursor.execute(f"INSERT INTO dbo.Produtos (nome, preco, descricao, imagem_url) VALUES ('{product_name}','{product_price}','{product_description}','{image_url}')")
        conn.commit()
        conn.close()
        return True
    except Exception as e:
        st.error(f'Erro ao inserir o produto: {e}')
        return False
```
Com este código será aberto uma conexão ao banco usando o pymssql, adicionará os dados e será fechada a mesma, caso não consiga inserir enviará uma mensagem de erro.

Para listar os itens na tela, é necessário criar seguintes funções:
```
def list_products():
    try:
        conn = pymssql.connect(server=SQL_SERVER,user=SQL_USER,password=SQL_PASSWORD,database=SQL_DATABASE)
        cursor = conn.cursor()
        cursor.execute("SELECT * FROM Produtos")
        rows= cursor.fetchall()
        conn.close()
        return rows
    except Exception as e:
        st.error(f'Erro ao listar produtos:{e}')
        return []
    
def list_products_screen():
    products = list_products()
    if products:
        cards_por_linha = 3
        cols = st.columns(cards_por_linha)
        for i, product in enumerate(products):
            col = cols[i % cards_por_linha]
            with col:
                st.write(f"###{product[1]}")
                st.write(f"**Descrição:**{product[2]}")
                st.write(f"**Preço:** R$ {product[3]:.2f}")
                if product[4]:
                    html_img = f'<img src={product[4]}" width="200" height="200" alt="Imagem do produto">'
                    st.markdown(html_img,unsafe_allow_html=True)
                st.markdown("---")
            if (i+1) % cards_por_linha == 0 and (i+1)<len(products):
                cols = st.columns(cards_por_linha)
    else:
        st.info("Nenhum produto encontrado")
```
A primeira cria uma tupla de produtos e caso não exista cria um elemento vazio e a segunda cria uma tabela com o streamlit para mostrar os dados armazenados no banco de dados, caso não tenha nenhum informa que a lista está vazia.

Por fim, é necessário atribuir as funções aos devidos botões que já existem no código inicial:
```
if st.button('Salvar Produto'):
    insert_product(product_name,product_price,product_description,product_image)
    return_message = 'Produto salvo com sucesso'

if st.button('Listar Produtos'):
    list_products_screen()
    return_message = 'Produtos listados com sucesso'

```
