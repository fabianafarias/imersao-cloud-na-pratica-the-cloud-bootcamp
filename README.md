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



# Implementação do Projeto em três fases

## 1 - Provisionamento do projeto no Google Cloud Platform e no AWS com Terraform

### Amazon Web Services (AWS)

- [x] Acesse a console da AWS e na barra de pesquisas, digite IAM. Na seção Services, clique em IAM.
![1-buscar-iam](https://user-images.githubusercontent.com/47903743/232870986-6f93a1ed-d3c9-473d-a742-ccbe9ebc1cd5.png)

&nbsp; 

- [x] Na seção Access management, clique em Users.
![2-clicar-em-users](https://user-images.githubusercontent.com/47903743/232871139-8482b349-f170-4b79-a21d-a728585114c0.png)

&nbsp; 

- [x] Clique no botão Add users.
![3-clique-em-add-user](https://user-images.githubusercontent.com/47903743/232872144-bc523b30-75fa-4729-959a-bcc4b5848f61.png)

&nbsp; 

- [x] Insira o nome terraform-pt-1 e clique em Next para criar o usuário do tipo programmatic.
![4-insira-nome-e-clique-next](https://user-images.githubusercontent.com/47903743/232872274-67cd9312-576b-4447-8a0a-d0245ca1f6a7.png)

&nbsp; 

- [x] Após avançar, em Set permissions/Permissions options, clique no botão Attach existing policies
directly.
![5-atach-existing-policies](https://user-images.githubusercontent.com/47903743/232872345-ae36b424-32da-4aac-84b9-b869cab25096.png)

&nbsp; 

- [x] Na barra de busca em Permissions policies busque por AmazonS3FullAccess.
![6-busque-AmazonS3FullAccess](https://user-images.githubusercontent.com/47903743/232872386-358182ba-9c9f-4438-ba66-e0917706c703.png)

&nbsp; 

- [x] Selecione AmazonS3FullAccess e clique em Next
![7-selecionar-AmazonS3FullAccess](https://user-images.githubusercontent.com/47903743/232872444-bc438844-71b7-4e6e-a9f7-b02791cd646c.png)

&nbsp; 

- [x] Clique em Create user  
![8-create-user](https://user-images.githubusercontent.com/47903743/232872488-d83d5fbc-f194-41f6-8ca4-c90684789453.png)

&nbsp; 

- [x] Acesse o usuário terraform-pt-1
![9-acesse-usuario-terraform](https://user-images.githubusercontent.com/47903743/232872539-324aefab-4a5e-4c01-ab90-84ec7eb4b6b7.png)

&nbsp; 

- [x] Clique em Security credentials
![10-clique-security-credentials](https://user-images.githubusercontent.com/47903743/232872587-4e887b27-f688-4be8-b54e-1678076c7a8d.png)

&nbsp; 

- [x] Navegue até a seção Access keys e clique em Create access key
![11-create-acess-key](https://user-images.githubusercontent.com/47903743/232872664-b622a2a8-a067-4049-aebd-320e8b29e2b0.png)

&nbsp; 

- [x] Selecione Command Line Interface (CLI) e 'I understand the above recommendation and want to proceed to create an access key' e clique em Next.
![12-selecione-cli](https://user-images.githubusercontent.com/47903743/232872707-8e846e64-4dfe-42a8-864c-143607228139.png)

&nbsp; 

- [x] Clique em Create access key
![13-crete-acess-key](https://user-images.githubusercontent.com/47903743/232872836-028068d7-d737-44f5-9de1-828dbb3023cc.png)

&nbsp; 

- [x] Clique em Download .csv file
![14-clique-download-csv-file](https://user-images.githubusercontent.com/47903743/232872884-18ffd4ae-240c-450d-9681-a863b5a9d415.png)

&nbsp; 

- [x] Após o download finalizar, clique em Done.
![15-done](https://user-images.githubusercontent.com/47903743/232872937-ddb95377-fd9b-4f90-a98c-60d45a3cc767.png)

&nbsp; 

- [x] Com o download feito, renomeie o .csv para accessKeys.csv
![16-renomeie](https://user-images.githubusercontent.com/47903743/232872980-453cf962-e40e-4bb5-937e-7fbca07f46c9.png)

&nbsp; 

### Google Cloud Platform (GCP)

- [x] <a href="https://github.com/fabianafarias/imersao-cloud-na-pratica-the-cloud-bootcamp/files/11265232/mission1.zip">CLIQUE AQUI</a> para baixar os arquivos do projeto hands-on.

&nbsp; 

- [x] Acessar a console da GCP e abrir o Cloud Shell
![17-abrir-cloud-shell](https://user-images.githubusercontent.com/47903743/232873406-ae436c05-7839-446d-a770-dbbc4c1b2cc0.png)


&nbsp; 

- [x] Fazer o upload dos arquivos accessKeys.csv e mission1.zip para o Cloud Shell
<div>
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232873457-2739fed8-41ed-4cb9-aa02-520eeb750ff1.png" />
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232873991-f1a12b51-9877-4954-a35f-b8bac42d99a9.png" />
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232873989-23f2cd90-0af6-47c8-b8c4-8b988dded5b4.png" />
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232873984-5d784467-1275-413e-b115-bcf11fd0226e.png" />
</div>

&nbsp; 

- [x] Após fazer o upload, executar os comandos de preparação dos arquivos:

<div style="margin: 8px 20px 0;">
  
```bash
  
mkdir mission1_pt
mv mission1.zip mission1_pt 
cd mission1_pt
unzip mission1.zip
mv ~/accessKeys.csv mission1/pt
cd mission1/pt 
chmod+x *.sh

```
</div>

![23-executar-comandos](https://user-images.githubusercontent.com/47903743/232873672-9c6a274e-00a7-46e9-8e4f-6923b3bf01a1.png)

&nbsp; 


- [x] Execute os comandos abaixo um a um para preparar o ambiente da AWS e GCP

<div style="margin: 8px 20px 0;">
  
```bash
./aws_set_credentials.sh accessKeys.csv
gcloud config set project<your-project-id>
```
</div>

<div>

</div>

- [x] Clique em Autorize e execute o comando abaixo para setar o projeto no Google
Cloud Shell

<div style="margin: 8px 20px 0;">
  
```bash

./gcp_set_project.sh

```
</div>

![26-setar-projeto-no-gcs](https://user-images.githubusercontent.com/47903743/232877099-a0d8481e-5775-4d6f-b725-b80a1b1010ee.png)

&nbsp; 


- [x] Execute o comando para habilitar as APIs do Kubernetes, Container Registry e
Cloud SQL

<div style="margin: 8px 20px 0;">
  
```bash

gcloud services enable containerregistry.googleapis.com
gcloud services enable container.googleapis.com
gcloud services enable sqladmin.googleapis.com

```
</div>

![27-habilitar-apis](https://user-images.githubusercontent.com/47903743/232877184-06431f8d-10bf-4ce9-8b78-2ec81aa4b68e.png)

&nbsp; 


### OBS IMPORTANTE (NÃO PULE ESTE PASSO):
Antes de executar os comandos do terraform, abra o Google Cloud Editor
e atualizar o arquivo tcb_aws_storage.tf substituindo o nome do bucket(na linha 4)
para um exclusivo (na AWS, os buckets precisam ter nomes únicos).

- [x] Abra o Google Cloud Editor
![28-abrir-editor](https://user-images.githubusercontent.com/47903743/232877316-df9ce7c8-e280-4e25-9739-a5c94561d2be.png)

&nbsp; 

- [x] Substituir xxxx pelas iniciais do seu nome mais dois números. Exemplo: luxxy-covid-testing-system-pdf-pt-jr29
![29-alterar-caracteres](https://user-images.githubusercontent.com/47903743/232877476-008cb1bb-c997-4727-bd22-90dadc8745e4.png)

&nbsp; 

- [x] Execute os seguintes comandos para provisionar os recursos de infraestrutura
  
<div style="margin: 8px 20px 0;">

```bash

cd ~/mission1_pt/mission1/pt/terraform/
terraform init
terraform plan
terraform apply

```
</div>

![31-provisionar-recursos-infra](https://user-images.githubusercontent.com/47903743/232877644-520a514c-4710-4472-b297-11caef8b305e.png)

&nbsp;

- [x] No console AWS, procurar por S3 para ver se o bucket foi criado
![32-verificar-s3](https://user-images.githubusercontent.com/47903743/232877728-f51c005f-fc72-4455-9e48-f777fcf7d1e1.png)

&nbsp;
  
- [x] No console dp GCP, procurar por GKE e verificar se o cluester de kubernetes foi criado 
![33-verificar-gke](https://user-images.githubusercontent.com/47903743/232877761-bc361fdf-808e-4799-b1f2-ac1152ce5025.png)

&nbsp;
- [x] Ainda no console GCP, buscar por SQL e verificar a criação do banco de dados
![35-clicar-instancia-cloudSql](https://user-images.githubusercontent.com/47903743/232879630-b376a4d2-c6b1-4718-98bb-83d0de510af4.png)

&nbsp;


### Configuração de Rede SQL
- [x] Clique na sua instância do Cloud SQL.
![35-clicar-instancia-cloudSql](https://user-images.githubusercontent.com/47903743/232877873-e421cc1a-b918-46c8-8deb-59835aa6dcd7.png)

&nbsp;
  
- [x] Na lateral direita, em Primary Instance, clique em “Connections”.
![36-clicar-connections](https://user-images.githubusercontent.com/47903743/232877959-7a2e6e02-098c-4dfb-8b50-4667f08b06df.png)

&nbsp;
  
- [x] Em Instance IP assignment, habilite o Private IP. Em Associated Network, selecione “Default”.
![37-ativa-privateip-e-network-default](https://user-images.githubusercontent.com/47903743/232878036-8f02e555-e313-428b-891c-1a953e28c409.png)

&nbsp;
  
- [x] Clique em Set up connection
![38-clique-setup-conection](https://user-images.githubusercontent.com/47903743/232878177-7b80d144-a6a0-4bd4-9cf2-ce0b116f6807.png)

&nbsp;
  
- [x] Selecione Enable Service Networking API (se solicitar)
![39-clique-enable](https://user-images.githubusercontent.com/47903743/232878236-a456d1ee-4b96-4832-a93d-745e39c59502.png)

&nbsp;
  
- [x] Selecione Use an automatically allocated IP range in your network e clique em Continue.
![40-use-automatic-api](https://user-images.githubusercontent.com/47903743/232878422-330c6e58-0e6d-432e-a30f-c75a92b1ca03.png)

&nbsp;
  
- [x] Clique em Create connection e aguarde alguns minutos.
![41-create-connection](https://user-images.githubusercontent.com/47903743/232878522-587a6337-3221-4a2b-9dca-83e09b676cf0.png)

&nbsp;
  
- [x] Após finalizar, em “Connections”, Autorized Networks, clique em "Adicionar Rede (Add Network)".
![43-add-network](https://user-images.githubusercontent.com/47903743/232878632-3babbb6c-c1be-494c-9b4e-faea971feedd.png)

&nbsp;
  
- [x] Em New Network, insira as seguintes informações:
- Nome: Public Access (Apenas para testes)
- Network: 0.0.0.0/0
- Clique em Done.
  
![44-nome-network-done](https://user-images.githubusercontent.com/47903743/232878810-900dcf86-1e63-4580-b508-5e701364f460.png)

&nbsp;

- [x] Clique em Save e aguarde finalizar a edição do Cloud SQL Instance.
![46-concluido](https://user-images.githubusercontent.com/47903743/232882016-17f8a987-d609-4c32-80ab-4b588d6fb771.png)

&nbsp;

PS: Para ambientes de produção, é recomendado utilizar apenas a Rede Privada
para o acesso ao banco de dados

## 2 - Implementação da aplicação com Docker e Kubernetes

### Amazon Web Services
- [x] Acessar a console da AWS. Na barra de pesquisas, digite IAM. Na seção
Services, clique em IAM.
![1-IAM](https://user-images.githubusercontent.com/47903743/232939333-d96534da-d1ba-4094-88ef-38f1cd534560.png)

&nbsp;
  
- [x] Clique em Add user, insira o nome luxxy-covid-testing-system-pt-app1 e
clique em Next para criar o usuário do tipo programmatic.
![2-ADD-USER](https://user-images.githubusercontent.com/47903743/232939425-ff820afe-90f5-4a38-bb5d-a7d2220f8204.png)

&nbsp;
  
- [x] Após avançar, em Set permissions, clique no botão Attach existing policies directly.
![3-atach](https://user-images.githubusercontent.com/47903743/232939518-be095e1b-ad6f-4eeb-a6b9-326939a1367c.png)

&nbsp;
  
- [x] Em Permissions policies, busque por AmazonS3FullAccess, selecione-o e clique em next.
![4-AmazonS3FullAccess](https://user-images.githubusercontent.com/47903743/232939797-c3919d49-91f7-43f1-a70c-4ef8e29d31e8.png)

&nbsp;
  
- [x] Revise todos os detalhes e clique em Create user
![6-revise-create](https://user-images.githubusercontent.com/47903743/232939902-722a4ce4-771c-40e4-a281-753d3c5b349c.png)

&nbsp;
  
- [x] Acesse o usuário luxxy-covid-testing-system-pt-app1 e crie uma access key, como foi feito anteriormente.
![5-acesse-luxxy-covid-testing-system-pt-app1](https://user-images.githubusercontent.com/47903743/232940019-37bc4811-18a9-48da-8fc5-4ee625a968f1.png)

&nbsp;


### Google Cloud Platform (GCP)
- [x] Navegue até a Cloud SQL instance e crie um novo usuário app com a senha welcome123456 no Cloud SQL MySQL database.
<div>
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232940094-37460089-45da-4945-842c-37874abe08bc.png" />
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232940336-de25f23e-1730-43ea-804f-487d69a50ef8.png" />
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232940441-22a65554-c9ce-425b-a4d0-025fd3b81830.png" />
</div>

&nbsp;

- [x] Se conecte ao Google Cloud Shell
- [x] Faça o download dos arquivos da missão 2 diretamente para o Cloud Shell usando o comando wget abaixo:

<div style="margin: 8px 20px 0;">
  
```bash

cd 
mkdir mission2_pt
cd mission2_pt
wget https://tcb-public-events.s3.amazonaws.com/icp/mission2.zip
unzip mission2.zip

```
</div>

![14-download-mission2](https://user-images.githubusercontent.com/47903743/232940928-ad4240e9-85fd-40f7-bf79-be14af718af2.png)

&nbsp;


- [x] Conecte ao MySQL DB em execução no Cloud SQL (assim que aparecer a janela para colocar a senha, insira welcome123456)

<div style="margin: 8px 20px 0;">
  
```bash

mysql --host=<public_ip_cloudsql> --port=3306 -u app -p

```
</div>

![15-conecte-mysql](https://user-images.githubusercontent.com/47903743/232940999-1068546f-623a-4cc8-ac0c-1992b020044a.png)

&nbsp;


- [x] Após estar conectado ao banco de dados da instância, crie a tabela de produtos para testes.

<div style="margin: 8px 20px 0;">
  
```bash

use dbcovidtesting;
source mission2/pt/db/create_table.sql;
show tables;
exit;

```
</div>

![17-criar-tabela](https://user-images.githubusercontent.com/47903743/232941049-fc68bc15-e352-48c0-9241-30d47c3a974d.png)

&nbsp;


- [x] Habilite a Cloud Build API através do Cloud Shell.

<div style="margin: 8px 20px 0;">
  
```bash

gcloud services enable cloudbuild.googleapis.com

```
</div>

![18-habilite-cloud-build-api](https://user-images.githubusercontent.com/47903743/232941091-0a83b65d-7044-4ed7-b511-c4f695e252f4.png)

&nbsp;


- [x] Faça o Build da Docker image e suba para o Google Container Registry. Por gentileza, substitua o <PROJECT_ID> com o My First Project ID

<div style="margin: 8px 20px 0;">
  
```bash

cd ~/mission2_pt/mission2/pt/app
gcloud builds submit --tag gcr.io/<PROJECT_ID>/luxxy-covid-testing-system-app-pt

```
</div>

![19-build-docker-img](https://user-images.githubusercontent.com/47903743/232941157-c14cfdc3-f7b9-4537-9f27-5af7995a45e1.png)

&nbsp;


- [x] Abra o Cloud Editor e edite o Kubernetes deployment file (luxxy-covid-testing-system.yaml) e atualize as variáveis abaixo (em vermelho) com o seu
<PROJECT_ID> no caminho da imagem Docker no Google Container Registry, AWS Bucket, AWS Keys (do arquivo luxxy-covid-testing-system-pt-app1.csv) e
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

![26-arquivo-alterado](https://user-images.githubusercontent.com/47903743/232941260-a3fb404a-3b69-4860-9bd4-0b42ac9146aa.png)

&nbsp;


- [x] Se conecte ao GKE (Google Kubernetes Engine) cluster via Console

<div>
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232941971-7245fecb-7490-45e3-999f-4f197504bb48.png" />
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232941978-06750d19-d1cb-4bf9-a90a-8e1c99142ea5.png" />
</div>
 

&nbsp;

- [x] Faça o Deploy da aplicação COVID-19 Testing Status System no Cluster

<div style="margin: 8px 20px 0;">
  
```bash

cd ~/mission2_pt/mission2/pt/kubernetes
kubectl apply -f luxxy-covid-testing-system.yaml

```
</div>

<div>
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232942326-890a3467-f9a5-4c5c-9fb5-f86a619ac43e.png" />
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232942336-d9ad2c08-6293-4def-bfed-8ce7a1bba138.png" />
<img width="500px" src="https://user-images.githubusercontent.com/47903743/232942338-2191bb2c-2230-425f-a889-175a8183b839.png" />
</div>

&nbsp;


- [x] Obtenha o IP Público e faça o teste da aplicação
![33-endpoint](https://user-images.githubusercontent.com/47903743/232942692-00a77612-e738-4b1a-8fd5-cc8f74811ef0.png)

&nbsp;

- [x] Você deve visualizar a aplicação up & running! Congrats!
![34-aplicacao-rodando](https://user-images.githubusercontent.com/47903743/232942800-a92f4021-b889-45e1-8e73-2c4e9b3f73c2.png)

&nbsp;


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
- [x] Conectar ao Google Cloud Shell
- [x] Download o dump do banco de dados

<div style="margin: 8px 20px 0;">
  
```bash

cd mkdir mission3
cd mission3
wget https://tcb-public-events.s3.amazonaws.com/icp/mission3.zip
unzip mission3.zip

```
</div>


![36-download-dump](https://user-images.githubusercontent.com/47903743/232942968-c0829844-6bdb-4c5e-876c-3b07b5c53e59.png)

&nbsp;

- [x] Conectar ao banco de dados MySQL no Cloud SQL. Senha: welcome123456
  
  <div style="margin: 8px 20px 0;">
  
```bash

mysql --host=<public_ip_address> --port=3306 -u app -p

```
</div>

![37-conectando-bd](https://user-images.githubusercontent.com/47903743/232943091-d153f426-2ca7-49bd-bd7c-593e55695aab.png)

&nbsp;


- [x] Importar o dump do banco de dados no Cloud SQL

<div style="margin: 8px 20px 0;">
  
```bash

use dbcovidtesting;
source ~/mission3/pt/db/db_dump.sql

```
</div>

![38-importar-dump](https://user-images.githubusercontent.com/47903743/232943137-be1b203a-761a-45d1-ae67-43e10f00dd7d.png)

&nbsp;

- [x] Checar se os dados foram importados com sucesso

<div style="margin: 8px 20px 0;">
  
```bash

SELECT * FROM records;
exit;

```
</div>

![39-checar-dados](https://user-images.githubusercontent.com/47903743/232943213-ebaf6154-97e1-4796-a122-9514a590121d.png)

&nbsp;
![40-resultados-no-sql](https://user-images.githubusercontent.com/47903743/232943259-6796d8c7-4919-435d-98a1-cf938d8fc965.png)

&nbsp;


### Amazon Web Services - Passos para a Migração dos arquivos PDF
- [x] Conectar no AWS Cloud Shell
![43-abrir-cloud-shell](https://user-images.githubusercontent.com/47903743/232943316-b0068bab-479e-4e11-9d90-e12a0e1b1bc0.png)

&nbsp;


- [x] Download dos arquivos PDF (Comprovante de teste negativo escaneado em PDF)


<div style="margin: 8px 20px 0;">
  
```bash

mkdir mission3_pt
cd mission3_pt
wget https://tcb-public-events.s3.amazonaws.com/icp/mission3.zip
unzip mission3.zip

```
</div>

![44-colar-comandos-cs](https://user-images.githubusercontent.com/47903743/232943540-c4e9040a-8db2-47e3-9482-b971511f009b.png)

&nbsp;
![45-arquivos-descompactados](https://user-images.githubusercontent.com/47903743/232943546-8b7b9de2-0c25-4b5c-9592-91b056bfe058.png)

&nbsp;


- [x] Sincronizar os arquivos PDF com o seu bucket criado no AWS S3 usado para o COVID-19 Testing Status System. Altere o nome do bucket para o seu
bucket.

<div style="margin: 8px 20px 0;">
  
```bash

cd mission3/pt/pdf_files
aws s3 sync . s3://luxxy-covid-testing-system-pdf-pt-xxxx

```
</div>

![46-sincronizar-pdf-bucket](https://user-images.githubusercontent.com/47903743/232943588-5533f3d0-e819-4fbd-95e0-8e86e460592e.png)

&nbsp;


- [x] Testar a aplicação. Ao testar a aplicação e navegar na opção "Ver registros" você deverá ser capaz de visualizar os dados importados!
![48-pdf-visivel-aplicacao](https://user-images.githubusercontent.com/47903743/232943682-27d71c05-86e8-40ee-b915-e93a9086d21a.png)

&nbsp;


&nbsp; 
  
  ![gif-parabens](https://media.giphy.com/media/SITBfZCDrnsb0qQidj/giphy.gif)

&nbsp;

# Parabéns!🎉

### Você migrou uma aplicação e seu banco de dados do "on-premises" para uma Arquitetura MultiCloud!

&nbsp; 

### Como deletar os recursos dos múltiplos provedores de Cloud

**Primeiro passo - Excluir todos os objetos do bucket**

- Acesse a Console da AWS e abra o serviço do S3.
- Selecione o seu bucket e clique em Empty.
- Para confirmar a exclusão dos objetos, digite *permanently delete*.
- Clique em Empty.

**Primeiro passo concluído!**

**Segundo passo - Atualize o instance deletion_protection de true para false**

- Abra o arquivo tcb_gcp_database.tf disponível no diretório ~/mission1_pt/mission1/pt/terraform/ usando o Google Editor.
- Na linha 11 do arquivo tcb_gcp_database.tf:
    - Atualize de deletion_protection = “true” para deletion_protection = “false”
    **Example:**
        - Versão atual: deletion_protection = "true”
        - Versão atualizada: deletion_protection = "false”
        
- Mude do Editor para Terminal
- Execute os comandos para aplicar as mudanças feitas no arquivo do Terraform:

<div style="margin: 8px 20px 0;">
  
```bash

cd ~/mission1_pt/mission1/pt/terraform/
terraform apply

```
</dev>

***Digite o valor: yes***

Agora, você está pronto para excluir todos os recursos com o terraform destroy. 🚀

- Execute o comando abaixo:
  
<div style="margin: 8px 20px 0;">
  
```bash

terraform destroy

```
</dev>

***Digite o valor: yes***

![49-destroyed](https://user-images.githubusercontent.com/47903743/232943750-ada78cfd-c6da-439f-9488-72811168180a.png)

&nbsp;


Muito show! Após alguns minutos, todos os recursos estarão excluídos dos múltiplos provedores de Cloud! 🚀✅



 
 




