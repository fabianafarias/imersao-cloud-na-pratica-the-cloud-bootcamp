# Projeto Cloud: Rede de Hotéis e Resorts de luxo migra aplicação e dados para a nuvem


## Problema

Durante a pandemia de Covid-19, uma rede de Hotéis e Resorts de luxo precisava identificar hóspedes infectados e armazenar os resultados dos exames para que pudessem ser acessados em todos os setores do hotel. A aplicação foi inicialmente colocada em um data center próprio, mas o aumento do número de hóspedes fez com que o data center não suportasse a demanda, deixando a aplicação lenta e atrasando o check-in.

## Solução

Para solucionar o problema, o gerente de TI do hotel decidiu migrar para a nuvem, contratando uma consultoria para auxiliar na migração. Com alguns serviços já existentes na Google Cloud Platform, foi definida como servidor de cloud principal.

A arquitetura da solução foi desenhada, com a criação de uma VPC como primeiro componente, seguido pela camada de banco de dados no Google Cloud SQL. A aplicação foi modernizada, com a troca de máquinas virtuais por containers. Foi criada uma imagem de Docker com os arquivos da aplicação e armazenamento no repositório Google Container Registry. A orquestração dos containers foi feita pelo Google Kubernetes Engine(GKE), que possibilita a comunicação da aplicação com o banco de dados no Google Cloud SQL.

O armazenamento dos resultados dos exames foi feito no AWS S3, que já era utilizado para fazer backups, com o objetivo de minimizar os custos. Para automatizar a aplicação, o provisionamento da infraestrutura na multi-cloud foi feito com Terraform de forma 100% automatizada.

### Arquitetura da solução

![arquitetura-da-solucao](https://user-images.githubusercontent.com/47903743/232522139-bfbbfe49-8eb2-4cd4-9441-29a5b1011cd3.png)

 
 




