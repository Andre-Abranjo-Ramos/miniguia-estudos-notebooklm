# miniguia-estudos-notebooklm

# Otimização de Estoque e Planejamento de Demanda no Varejo com Databricks e AWS

##  Contexto e Objetivos
Este projeto foi desenvolvido como parte do Desafio de Projeto do Bootcamp de Power BI com IA da DIO. O objetivo principal é aplicar técnicas modernas de Engenharia de Dados para resolver um problema crítico do varejo: a ruptura de estoque e o excesso de capital imobilizado. Unindo minha bagagem de 25 anos em planejamento de demanda e controladoria à tecnologia de Big Data, este repositório simula um pipeline escalável focado na tomada de decisão estratégica.

##  Arquitetura de Dados Utilizada
Para este projeto, foi desenhada uma estrutura de Lakehouse utilizando a **Arquitetura Medalhão** rodando em **Databricks na AWS**:
- **Camada Bronze:** Armazenamento dos dados brutos de vendas e inventário em formato JSON/CSV em um bucket do **Amazon S3**.
- **Camada Silver:** Limpeza, padronização de esquemas, remoção de duplicatas e tratamento de valores nulos utilizando **PySpark** no Databricks.
- **Camada Gold:** Criação do modelo dimensional (Star Schema) contendo tabelas fato e dimensões, otimizadas para consultas analíticas rápidas.
- **Camada de Consumo:** Conexão direta do **Power BI** na camada Gold para criação de um Dashboard Gerencial focado em KPIs de inventário.

##  Curadoria de Fontes (NotebookLM)
O processo de aprendizagem e design do pipeline foi acelerado com o uso do Google NotebookLM, onde foram utilizadas as seguintes fontes abertas:
1.  [https://blog.dsacademy.com.br/arquitetura-medalhao-o-guia-definitivo-para-organizar-o-data-lakehouse-fundamentos/]
3.  [https://datauniverse.com.br/arquitetura-medallion-bronze-silver-gold-x-modelo-sor-sot-e-spec/]
4.  [https://www.databricks.com/br/blog/how-move-apache-airflowr-databricks-lakeflow-jobs]
5.  [https://docs.databricks.com/aws/pt/security/network/classic/vpc-peering]
6.  [https://airbyte.com/how-to-sync/ibm-db2-to-databricks-lakehouse]
7.  [https://docs.databricks.com/aws/pt/ingestion/lakeflow-connect/sql-server-pipeline]
8.  [https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/]
9.  [https://docs.databricks.com/aws/en/connect/jdbc-connection]
10. [https://www.databricks.com/br/blog/navigating-oracle-databricks-migration-tips-seamless-transition]
11. [https://www.databricks.com/br/blog/navigating-sql-server-databricks-migration-tips-seamless-transition]
12. [https://www.databricks.com/br/blog/2020/01/30/what-is-a-data-lakehouse.html]
13. [https://docs.databricks.com/aws/pt/lakehouse-architecture/data-governance/best-practices]
14. [https://ijaibdcms.org/index.php/ijaibdcms/article/view/571]
15. [https://docs.databricks.com/aws/en/data-governance/unity-catalog/upgrade/]

##  Engenharia de Prompts (Troubleshooting)
Durante o desenvolvimento das consultas e métricas com o auxílio da IA, documentei os seguintes aprendizados:

- Com base nos documentos e no histórico da nossa conversa, aqui está uma lista consolidada de aprendizados essenciais sobre engenharia de dados, migração e governança no ecossistema Databricks e AWS:

### 1. Arquitetura Medalhão e Modernização (Lakehouse)
*   **O padrão de camadas (Bronze, Silver, Gold)** é a base para organizar dados de forma lógica, melhorando incrementalmente sua qualidade e confiabilidade.
*   A camada **Bronze** preserva a "fonte da verdade" bruta, permitindo auditorias e reprocessamentos futuros sem perdas.
*   A camada **Silver** (ou SOT - Fonte Única da Verdade) é onde ocorre a limpeza, padronização e resolução de discrepâncias entre sistemas.
*   A camada **Gold** foca no consumo final, utilizando modelos dimensionais (Star Schema) otimizados para ferramentas de BI e IA.
*   O conceito de **Lakehouse** une a flexibilidade e o baixo custo dos data lakes com a performance e transações ACID dos data warehouses.

### 2. Ingestão Eficiente e Migração de Dados
*   O **Auto Loader (`cloudFiles`)** é a ferramenta recomendada para processar incrementalmente milhões de arquivos vindos do S3 com eficiência de custo e evolução automática de schema.
*   Para migrações de RDBMS (como SQL Server ou Oracle), o **LakeFlow Connect** automatiza a extração (Snapshot e CDC) de forma contínua para evitar perda de dados por truncagem de logs.
*   A estratégia de **reconciliação** para bilhões de registros deve envolver contagem de linhas e hash de colunas críticas para garantir a integridade entre origem e destino.
*   A migração do Airflow para o **Lakeflow Jobs** deve ser incremental, substituindo sensores por gatilhos de chegada de arquivos e transformando XComs em tabelas do Unity Catalog ou valores de tarefas.

### 3. Governança e Segurança com Unity Catalog
*   O **Unity Catalog (UC)** centraliza a gestão de metadados, controle de acesso e auditoria para dados e ativos de IA em um único lugar.
*   O uso de um **namespace de três níveis** (`catálogo.esquema.tabela`) simplifica a segregação de ambientes e unidades de negócio.
*   A **linhagem de dados (Data Lineage)** capturada em tempo de execução permite rastrear o dado desde o arquivo bruto no S3 até o dashboard, facilitando a depuração e o compliance.
*   A segurança de rede em ambientes cross-account deve priorizar o **VPC Peering** para tráfego privado e o **AWS PrivateLink** para isolar a comunicação entre planos de controle e de dados.

### 4. Otimização de Performance e Custos (FinOps)
*   Comandos como **OPTIMIZE** e **Z-ORDER BY** são vitais após grandes cargas de dados para compactar arquivos pequenos e acelerar filtros de consulta.
*   O **Liquid Clustering** surge como uma alternativa moderna ao particionamento fixo, oferecendo agrupamento dinâmico que se ajusta aos padrões de consulta.
*   Sob a ótica de **FinOps**, os **Serverless SQL Warehouses** reduzem o TCO ao eliminar custos administrativos e escalar elasticamente conforme a demanda real.
*   O monitoramento do **Spark UI** (Skewness, Spill e Shuffle) é crucial para identificar gargalos em jobs de validação lentos [conforme nossa discussão anterior].

### 5. Qualidade de Dados e Resiliência
*   A implementação de **DLT Expectations** (`expect_or_drop`, `expect_or_fail`) automatiza a barreira contra dados corrompidos diretamente no pipeline de processamento.
*   O recurso de **Time Travel (Viagem no Tempo)** do Delta Lake permite reverter tabelas para estados íntegros anteriores instantaneamente via comando `RESTORE`, minimizando o tempo de recuperação (RTO).
*   Políticas de **tags obrigatórias** em clusters Databricks garantem que cada recurso provisionado possa ser rastreado por centro de custo, squad e ambiente [conforme nossa discussão anterior].

##  Miniguia de Estudo e Entrega Final
### Glossário de Conceitos Aprendidos
- **Lakehouse:** Arquitetura moderna que combina a flexibilidade de armazenamento do Data Lake com o gerenciamento de transações ACID do Data Warehouse.
- **Cobertura de Estoque:** Métrica que indica quantos dias o estoque atual durará com base na média de vendas recentes.
- **Ruptura de Estoque:** Evento onde a demanda por um produto não é atendida por falta de disponibilidade física na gôndola/depósito.

### 1. Arquitetura e Modelagem
*   **Arquitetura Medalhão (Medallion):** Padrão de design de dados que organiza as informações em camadas progressivas de qualidade (Bronze, Silver e Gold) dentro de um Lakehouse. Seu objetivo é melhorar a confiabilidade e o desempenho dos dados à medida que fluem pelo sistema.
*   **Camada Bronze:** Ponto de entrada onde os dados são armazenados em seu formato original ("raw"), preservando a "fonte da verdade" para auditorias e reprocessamentos.
*   **Camada Silver (Prata):** Camada de refinamento onde os dados são limpos, validados e padronizados, servindo como a "Fonte Única da Verdade" (SOT) para a organização.
*   **Camada Gold (Ouro):** Nível final com dados agregados e otimizados para consumo por ferramentas de BI, dashboards e modelos de Machine Learning.
*   **Data Lakehouse:** Arquitetura aberta que combina a flexibilidade e o baixo custo dos data lakes com as transações ACID e a performance dos data warehouses.
*   **SOR, SOT e SPEC:** Nomenclaturas de governança clássica equivalentes à Arquitetura Medalhão; SOR (System of Record) equivale à Bronze, SOT (Source of Truth) à Silver, e SPEC (Specific) à Gold.
*   **Star Schema (Esquema Estrela):** Modelo de dados dimensional comum na camada Gold para otimizar consultas analíticas.

### 2. Ingestão e Migração
*   **Auto Loader (`cloudFiles`):** Recurso do Databricks que processa arquivos de forma incremental e eficiente conforme chegam ao armazenamento em nuvem (S3), com suporte à evolução automática de schema.
*   **CDC (Change Data Capture):** Tecnologia que captura apenas as alterações (inserções, atualizações e exclusões) de um banco de dados de origem em tempo real.
*   **LakeFlow Connect:** Serviço gerenciado que automatiza a ingestão de dados de fontes como SQL Server e Oracle, cuidando do Snapshot inicial e da replicação contínua (CDC).
*   **SCD Tipo 2 (Slowly Changing Dimensions):** Técnica de modelagem que rastreia o histórico completo de alterações em dimensões, criando novos registros para cada mudança.

### 3. Governança e Segurança
*   **Unity Catalog (UC):** Solução de governança unificada que centraliza o controle de acesso, auditoria, linhagem e descoberta de dados e ativos de IA em várias nuvens.
*   **Data Lineage (Linhagem de Dados):** Capacidade do Unity Catalog de rastrear a origem e as transformações de um dado, desde o arquivo bruto até o consumo final, essencial para auditoria e análise de impacto.
*   **External Locations:** Objetos no Unity Catalog que gerenciam o acesso governado a caminhos de armazenamento em nuvem (como buckets S3) sem expor chaves estáticas.
*   **Metastore:** O contêiner de nível superior no Unity Catalog que armazena metadados de tabelas, views e permissões por região de nuvem.
*   **VPC Peering:** Conexão de rede privada que permite que clusters do Databricks acessem outras infraestruturas AWS (como RDS ou Redshift) usando IPs internos.

### 4. Performance e Qualidade
*   **Expectations (Expectativas):** Regras de qualidade definidas em pipelines de Delta Live Tables (DLT) para monitorar, descartar ou interromper o processamento de dados corrompidos.
*   **Liquid Clustering (Agrupamento Líquido):** Técnica moderna de organização de dados que substitui o particionamento fixo, ajustando-se dinamicamente aos padrões de consulta para melhorar a performance.
*   **OPTIMIZE e Z-ORDER:** Comandos usados para compactar arquivos pequenos e colocalizar dados relacionados, acelerando drasticamente a leitura de tabelas Delta.
*   **Time Travel (Viagem no Tempo):** Recurso do Delta Lake que permite consultar ou restaurar versões anteriores de uma tabela usando o log de transações.

### 5. Operações e FinOps
*   **DBU (Databricks Unit):** Unidade de medida padronizada usada para quantificar o consumo de computação na plataforma Databricks.
*   **Serverless SQL Warehouse:** Recurso de computação totalmente gerenciado pelo Databricks que escala elasticamente para consultas SQL, oferecendo o melhor TCO e performance para BI.
*   **Lakeflow Jobs:** Orquestrador nativo do Databricks focado em dados, que substitui o polling de agendadores externos por gatilhos baseados em eventos e chegada de arquivos.

### Prompts Reutilizáveis para Revisões Futuras
- Para otimizar o particionamento de tabelas Delta na AWS, especialmente para consultas que filtram dados de vendas (como datas de transação ou IDs de produto), é necessário evoluir de estratégias de particionamento estático para técnicas dinâmicas e automatizadas oferecidas pelo Delta Lake.

Abaixo estão as principais estratégias recomendadas pelas fontes para garantir alta performance e eficiência de custo:

### 1. Implementar Liquid Clustering (Agrupamento Líquido)
O **Liquid Clustering** é a recomendação mais moderna para substituir o particionamento fixo e o Z-Ordering tradicional. 
*   **Por que usar em vendas:** Consultas de vendas geralmente sofrem com a **assimetria de dados** (skewness) quando particionadas apenas por data de transação (ex: picos de vendas em datas festivas). 
*   **Benefício:** Ele organiza os dados de forma dinâmica e contínua, produzindo um conjunto de dados transacional equilibrado e otimizando o *Data Skipping* (pular dados irrelevantes) sem exigir ajustes manuais constantes.

### 2. Utilizar Z-Ordering em Colunas de Filtro Frequentes
Se o Liquid Clustering ainda não for uma opção, o **Z-Ordering** deve ser aplicado para colocalizar informações relacionadas nos mesmos arquivos.
*   **Foco em Vendas:** Aplique Z-Order em colunas que os usuários de negócio utilizam frequentemente como filtros `WHERE` ou em joins, como `id_produto`, `id_cliente` ou `regiao`.
*   **Impacto:** Isso reduz drasticamente a quantidade de dados que o Spark precisa ler do S3 para responder a uma consulta específica.

### 3. Evitar a Super-partição (Over-partitioning)
Um erro comum ao migrar sistemas legados para o Delta Lake é manter esquemas de partição muito granulares.
*   **O Problema:** Criar partições pequenas demais (ex: por hora ou por minuto em uma tabela de vendas) aumenta a sobrecarga de metadados e pode retardar as varreduras de arquivos (scans).
*   **A Recomendação:** Mantenha partições que resultem em arquivos de tamanho considerável. Utilize o comando **OPTIMIZE** para comprimir arquivos pequenos remanescentes, o que reduz a latência de leitura.

### 4. Otimização Preditiva via Unity Catalog
Se as tabelas forem gerenciadas no **Unity Catalog**, é altamente recomendável habilitar a **Otimização Preditiva**.
*   **Automação:** Esse recurso desbloqueia a manutenção automática da tabela, incluindo tarefas de compactação de arquivos (compaction) e clustering (agrupamento). 
*   **Vantagem FinOps:** Reduz o esforço manual da equipe de engenharia e garante que a performance de leitura para dashboards de BI permaneça consistente ao longo do tempo.

### 5. Modelagem Dimensional na Camada Gold
Para o consumo final dos dados de venda, a estruturação na **camada Gold** deve seguir modelos otimizados para BI.
*   **Esquema Estrela (Star Schema):** Projete a tabela fato de vendas e suas dimensões (produto, calendário) para facilitar consultas rápidas em ferramentas como Power BI e Tableau.
*   **Desempenho:** Tabelas nesta camada devem ser projetadas especificamente para alta performance de leitura, suportando agregações de métricas de negócio como "receita mensal" de forma eficiente.

Em resumo, para otimizar filtros de venda, você deve priorizar o **Liquid Clustering** para lidar com a distribuição de dados e o **Unity Catalog** para automatizar a manutenção da saúde das tabelas Delta.

Como gerar um template de script PySpark para ler arquivos CSV incrementais de um bucket S3?

A ferramenta recomendada para ler arquivos CSV de forma incremental de um bucket S3 no Databricks é o **Auto Loader** (usando o formato `cloudFiles`).

O Auto Loader é mais eficiente que o comando `COPY INTO` para volumes de milhões de arquivos, pois reduz a sobrecarga de listagem no S3 e oferece suporte nativo para **inferência e evolução de schema**.

Abaixo está um template de script PySpark estruturado para a **camada Bronze** da Arquitetura Medalhão:

### Template: Ingestão Incremental com Auto Loader

```python
# 1. Definição de caminhos e parâmetros
# Recomenda-se o uso de Unity Catalog Volumes ou External Locations para acesso ao S3
source_path = "s3://seu-bucket/dados-brutos/vendas/"
checkpoint_path = "s3://seu-bucket/_checkpoints/vendas_bronze/"
schema_location = "s3://seu-bucket/_schemas/vendas_bronze/"
target_table = "catalogo.vendas.vendas_bronze"

# 2. Configuração da leitura incremental (Auto Loader)
df_incremental = (spark.readStream
    .format("cloudFiles")
    .option("cloudFiles.format", "csv")
    # Ativa a inferência automática de schema e rastreamento de mudanças
    .option("cloudFiles.schemaLocation", schema_location) 
    # Opções específicas para arquivos CSV
    .option("header", "true")
    .option("delimiter", ",")
    .option("inferSchema", "true")
    .load(source_path))

# 3. Escrita para a Tabela Delta (Camada Bronze)
# O uso de checkpoints garante que apenas novos arquivos sejam processados
query = (df_incremental.writeStream
    .format("delta")
    .option("checkpointLocation", checkpoint_path)
    # Permite adicionar novas colunas automaticamente se o CSV de origem mudar
    .option("mergeSchema", "true") 
    # 'trigger(availableNow=True)' processa todos os novos dados e encerra o job (custo-eficiente)
    .trigger(availableNow=True) 
    .toTable(target_table))

query.awaitTermination()
```

### Principais Componentes do Script:

*   **`format("cloudFiles")`**: Ativa o motor do Auto Loader para processar novos arquivos conforme eles chegam ao S3, sem a necessidade de configurar notificações de eventos complexas na AWS.
*   **`cloudFiles.schemaLocation`**: Local onde o Databricks armazena os metadados do schema. Isso permite que o pipeline lide com **Evolução de Schema** sem quebrar, o que é vital em migrações de sistemas legados.
*   **`checkpointLocation`**: Garante a resiliência e a semântica de processamento incremental. Se o job falhar, ele saberá exatamente de onde parou no bucket S3.
*   **`trigger(availableNow=True)`**: Uma prática de **FinOps** para cargas em lote que chegam periodicamente; ele processa todos os arquivos pendentes em uma micro-batch e desliga o cluster, economizando DBUs.
*   **Integração com Unity Catalog**: Ao usar o nome `catalogo.esquema.tabela`, você garante que o acesso aos dados e a **linhagem (lineage)** sejam governados centralmente desde o S3 até a camada Gold.

### Visualizações no Power BI (Projeto Final)
O Dashboard final consome os dados da camada Gold para exibir:
- Taxa de ruptura por categoria de produto.
- Giro de estoque e curva ABC de faturamento vs. volume.
- Alertas visuais de produtos com cobertura abaixo do estoque de segurança.
