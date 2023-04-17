# Projeto Cloud: Rede de Hotéis e Resorts de luxo migra aplicação e dados para a nuvem

<img width="800px" src="https://user-images.githubusercontent.com/47903743/232585848-1b6055c5-39a3-4f78-9258-72601fea03e7.png" />

## Problema

<div>
<img width="400px" height=400px" src="https://user-images.githubusercontent.com/47903743/232585737-6e05ca63-8ea5-488d-8fe4-e8745e9963e5.png" />
<img width="400px" height=400px" src="https://user-images.githubusercontent.com/47903743/232590939-19a28e63-19fa-4919-b5c0-f529371527cf.png" />
</div>

Durante a pandemia de Covid-19, uma rede de Hotéis e Resorts de luxo precisava identificar hóspedes infectados e armazenar os resultados dos exames para que pudessem ser acessados em todos os setores do hotel. A aplicação foi inicialmente colocada em um data center próprio, mas o aumento do número de hóspedes fez com que o data center não suportasse a demanda, deixando a aplicação lenta e atrasando o check-in.


## Solução

Para solucionar o problema, o gerente de TI do hotel decidiu migrar para a nuvem, contratando uma consultoria para auxiliar na migração. Com alguns serviços já existentes na Google Cloud Platform, foi definida como servidor de cloud principal.

A arquitetura da solução foi desenhada, com a criação de uma VPC como primeiro componente, seguido pela camada de banco de dados no Google Cloud SQL. A aplicação foi modernizada, com a troca de máquinas virtuais por containers. Foi criada uma imagem de Docker com os arquivos da aplicação e armazenamento no repositório Google Container Registry. A orquestração dos containers foi feita pelo Google Kubernetes Engine(GKE), que possibilita a comunicação da aplicação com o banco de dados no Google Cloud SQL.

O armazenamento dos resultados dos exames foi feito no AWS S3, que já era utilizado para fazer backups, com o objetivo de minimizar os custos. Para automatizar a aplicação, o provisionamento da infraestrutura na multi-cloud foi feito com Terraform de forma 100% automatizada.

### Arquitetura da solução

<img width="800px" src="https://user-images.githubusercontent.com/47903743/232522139-bfbbfe49-8eb2-4cd4-9441-29a5b1011cd3.png"/>


# Passos para implementação do Projeto

## Automatização de processos com Terraform no Google Cloud Platform e no AWS

### Amazon Web Services (AWS)

- [ ] Acessar a console da AWS. Na barra de pesquisas, digite IAM. 
- [ ] Na seçãoServices, clique em IAM.
- [ ] Clique em Add user, insira o nome terraform-pt-1 e clique em Next para criar o usuário do tipo programmatic.
- [ ] Após avançar, em Set permissions, clique no botão Attach existing policies
directly.
- [ ] Digite AmazonS3FullAccess em Filter distributions by text, property or
value e aperte Enter.
- [ ] Selecione AmazonS3FullAccess e clique em Next
- [ ] Clique em Create user  
- [ ] Acesse o usuário terraform-pt-1
- [ ] Clique em Security credentials
- [ ] Navegue até a seção Access keys
- [ ] Clique em Create access key
- [ ] Selecione Command Line Interface (CLI) e 'I understand the above
recommendation and want to proceed to create an access key' e clique em Next.
- [ ] Clique em Create access key
- [ ] Clique em Download .csv file
- [ ] Após o download finalizar, clique em Done.
- [ ] Com o download feito, renomeie o .csv para accessKeys.csv

### Google Cloud Platform (GCP)

- [ ] CLIQUE AQUI para baixar os arquivos do projeto hands-on.(anexar mission.zip)
- [ ] Acessar a console da GCP e abrir o Cloud Shell
- [ ] Fazer o upload dos arquivos accessKeys.csv e mission1.zip para o Cloud Shell
- [ ] Após fazer o upload, executar os comandos de preparação dos arquivos:

<div style="margin: 8px 20px 0;">
  
```bash
  
mkdir mission1_pt
mv mission1.zip mission1_pt
cd mission1_pt unzip mission1.zip
mv ~/accessKeys.csv mission1/pt 
cd mission1/pt
chmod+x *.sh

```
</div>

- [ ] Execute os comandos abaixo para preparar o ambiente da AWS e GCP

<div style="margin: 8px 20px 0;">
  
```bash
./aws_set_credentials.sh accessKeys.csv gcloud config set project<your-project-id>
```
</div>

- [ ] Clique em Autorize e execute o comando abaixo para setar o projeto no Google
Cloud Shell

<div style="margin: 8px 20px 0;">
  
```bash

./gcp_set_project.sh

```
</div>

- [ ] Execute o comando para habilitar as APIs do Kubernetes, Container Registry e
Cloud SQL

<div style="margin: 8px 20px 0;">
  
```bash

gcloud services enable containerregistry.googleapis.com gcloud
services enable container.googleapis.com gcloud services enable
sqladmin.googleapis.com

```
</div>

### OBS IMPORTANTE (NÃO PULE ESTE PASSO):
Antes de executar os comandos do terraform, abra o Google Cloud Editor
e atualizar o arquivo tcb_aws_storage.tf substituindo o nome do bucket(na linha 4)
para um exclusivo (na AWS, os buckets precisam ter nomes únicos).

- [ ] Abra o Google Cloud Editor
- [ ] Substituir xxxx pelas iniciais do seu nome mais dois números. Exemplo: luxxy-covid-testing-system-pdf-pt-jr29
- [ ] Execute os seguintes comandos para provisionar os recursos de infraestrutura
  
<div style="margin: 8px 20px 0;">
  
```bash

cd ~/mission1_pt/mission1/pt/terraform/ terraform init terraform
plan terraform apply

```
</div>

- [ ] Após acessar o serviço do GKE para criar o cluster, clicar no botão 'Compare' para Comparar os modes de cluster para entender mais sobre as suas diferenças.

### Configuração de Rede SQL
- [ ] Após a conclusão do provisionamento da instância do CloudSQL, acesse o
serviço do Cloud SQL.
- [ ] Clique na sua instância do Cloud SQL.
- [ ] Na lateral direita, em Primary Instance, clique em “Connections”.
- [ ] Em Instance IP assignment, habilite o Private IP.
- [ ] Em Associated Network, selecione “Default”.
- [ ] Clique em Set up connection
- [ ] Enable Service Networking API (se solicitar)
- [ ] Selecione Use an automatically allocated IP range in your network e clique em Continue.
- [ ] Clique em Create connection e aguarde alguns minutos.
- [ ] Após finalizar, em “Connections”, Autorized Networks, clique em "Adicionar
Rede (Add Network)".
- [ ] Em New Network, insira as seguintes informações:
- Nome: Public Access (Apenas para testes)
- Network: 0.0.0.0/0
- Clique em Done.
- Clique em Save e aguarde finalizar a edição do Cloud SQL Instance.
PS: Para ambientes de produção, é recomendado utilizar apenas a Rede Privada
para o acesso ao banco de dados


## Implementação da aplicação com Docker e Kubernetes com Kubernetes Engine

### Amazon Web Services
- [ ] Acessar a console da AWS. Na barra de pesquisas, digite IAM. Na seção
Services, clique em IAM.
- [ ] Clique em Add user, insira o nome luxxy-covid-testing-system-pt-app1 e
clique em Next para criar o usuário do tipo programmatic.
- [ ] Após avançar, em Set permissions, clique no botão Attach existing policies directly.
- [ ] Digite AmazonS3FullAccess em Filter distributions by text, property or
value e aperte Enter.
- [ ] Selecione AmazonS3FullAccess e clique em Next
- [ ] Revise todos os detalhes
- [ ] Clique em Create user

Passos para fazer o download da chave de acesso
- [ ] Acesse o usuário luxxy-covid-testing-system-pt-app1
- [ ] Clique em Security credentials
- [ ] Navegue até a seção Access keys
- [ ] Clique em Create access key
- [ ] Selecione Command Line Interface (CLI) e I understand the above
recommendation and want to proceed to create an access key e clique em Next
- [ ] Clique em Create access key
- [ ] Clique em Download .csv file.
- [ ] Após o download finalizar, clique em Done.
- [ ] Com o download feito, renomeie o .csv para accessKeys.csv

### Google Cloud Platform (GCP)
- [ ] Navegue até a Cloud SQL instance e crie um novo usuário app com a senha
welcome123456 no Cloud SQL MySQL database.
- [ ] Se conecte ao Google Cloud Shell
- [ ] Faça o download dos arquivos da missão 2 diretamente para o Cloud Shell
usando o comando wget abaixo:

<div style="margin: 8px 20px 0;">
  
```bash

cd mkdir mission2_pt cd mission2_pt wget https://tcb-public-events.s3.amazonaws.com/icp/mission2.zip unzip mission2.zip

```
</div>


- [ ] Conecte ao MySQL DB em execução no Cloud SQL (assim que aparecer a
janela para colocar a senha, insira welcome123456)

<div style="margin: 8px 20px 0;">
  
```bash

mysql --host=<public_ip_cloudsql> --port=3306 -u app -p

```
</div>

- [ ] Após estar conectado ao banco de dados da instância, crie a tabela de
produtos para testes.

<div style="margin: 8px 20px 0;">
  
```bash

use dbcovidtesting;
source mission2/pt/db/create_table.sql;
show tables;
exit;

```
</div>

- [ ] Habilite a Cloud Build API através do Cloud Shell.

<div style="margin: 8px 20px 0;">
  
```bash

cloudbuild.googleapis.com

```
</div>

- [ ] Faça o Build da Docker image e suba para o Google Container Registry. Por
gentileza, substitua o <PROJECT_ID> com o My First Project ID

<div style="margin: 8px 20px 0;">
  
```bash

cd ~/mission2_pt/mission2/pt/app gcloud builds submit --tag
gcr.io/<PROJECT_ID>/luxxy-covid-testing-system-app-pt

```
</div>


- [ ] Abra o Cloud Editor e edite o Kubernetes deployment file (luxxy-covid-testing-
system.yaml) e atualize as variáveis abaixo (em vermelho) com o seu
<PROJECT_ID> no caminho da imagem Docker no Google Container Registry,
AWS Bucket, AWS Keys (do arquivo luxxy-covid-testing-system-pt-app1.csv) e
o IP Privado do Cloud SQL Database.

<div style="margin: 8px 20px 0;">
  
```bash

cd ~/mission2_pt/mission2/pt/kubernetes luxxy-covid-testing-
system.yaml image: gcr.io/<PROJECT_ID>/luxxy-covid-testing-system-
app-pt:latest ... - name: AWS_BUCKET value: "luxxy-covid-testing-
system-pdf-pt-xxxx" - name: S3_ACCESS_KEY value:
"xxxxxxxxxxxxxxxxxxxxx" - name: S3_SECRET_ACCESS_KEY value:
"xxxxxxxxxxxxxxxxxxxx" - name: DB_HOST_NAME value: "172.21.0.3"

```
</div>


- [ ] Se conecte ao GKE (Google Kubernetes Engine) cluster via Console (seguir
video)
- [ ] Faça o Deploy da aplicação COVID-19 Testing Status System no Cluster

<div style="margin: 8px 20px 0;">
  
```bash

cd ~/mission2_pt/mission2/pt/kubernetes kubectl apply -f luxxy-
covid-testing-system.yaml

```
</div>

- [ ] Obtenha o IP Público e faça o teste da aplicação (CLIQUE AQUI para baixar
exemplo de Teste de COVID-19)
- [ ] Você deve visualizar a aplicação up & running! Congrats!

__

## Known issue during this step

ERROR: (gcloud.builds.submit) INVALID_ARGUMENT: could not resolve
source: googleapi: Error 403:
989404026119@cloudbuild.gserviceaccount.com does not have
storage.objects.get access to the Google Cloud Storage object.,
forbidden 

Para solucionar:
1. Acesse o IAM & Admin;
2. Clique na sua Cloud Build Service Account. Exemplo:
989404026119@cloudbuild.gserviceaccount.com Cloud Build Service
Account;
3. Na sua Cloud Build Service Account, do lado direito,
clique em Edit principal;
4. Clique em Add another role (Adicionar outra função);
5. Clique em Select Role, e filtre por Storage Admin
ou gcs. Selecione Storage Admin (Full control of GCS resources);
6. Clique em Save and retorno para o Cloud Shell.
 

 ## Migrando uma aplicação e seu banco de dados do "on-premises" para uma Arquitetura MultiCloud

### Google Cloud Platform - Passos para Migração do Banco de Dados MySQL
- [ ] Conectar ao Google Cloud Shell
- [ ] Download o dump do banco de dados

<div style="margin: 8px 20px 0;">
  
```bash

cd mkdir mission3_pt cd mission3_pt wget https://tcb-public-
events.s3.amazonaws.com/icp/mission3.zip unzip mission3.zip

```
</div>


- [ ] Conectar ao banco de dados MySQL no Cloud SQL. Senha: welcome123456
mysql --host=<public_ip_address> --port=3306 -u app -p
- [ ] Importar o dump do banco de dados no Cloud SQL

<div style="margin: 8px 20px 0;">
  
```bash

use dbcovidtesting; source ~/mission3_pt/mission3/pt/db/db_dump.sql

```
</div>

- [ ] Checar se os dados foram importados com sucesso

<div style="margin: 8px 20px 0;">
  
```bash

SELECT * FROM records;
exit;

```
</div>

### Amazon Web Services - Passos para a Migração dos arquivos PDF
- [ ] Conectar no AWS Cloud Shell
- [ ] Download dos arquivos PDF (Comprovante de teste negativo escaneado em
PDF)

<div style="margin: 8px 20px 0;">
  
```bash

mkdir mission3_pt cd mission3_pt wget https://tcb-public-
events.s3.amazonaws.com/icp/mission3.zip unzip mission3.zip

```
</div>

- [ ] Sincronizar os arquivos PDF com o seu bucket criado no AWS S3 usado para o COVID-19 Testing Status System. Altere o nome do bucket para o seu
bucket.

<div style="margin: 8px 20px 0;">
  
```bash

cd mission3/pt/pdf_files aws s3 sync . s3://luxxy-covid-testing-
system-pdf-pt-xxxx

```
</div>

- [ ] Testar a aplicação. Ao testar a aplicação e navegar na opção "Ver registros" você deverá ser capaz de visualizar os dados importados!

## Parabéns! Você migrou uma aplicação e seu banco de dados do "on-premises" para uma Arquitetura MultiCloud!






 
 




