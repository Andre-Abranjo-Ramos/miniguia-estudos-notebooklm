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
1. [Link ou Nome do Artigo 1 - Ex: Databricks Architecture Guide]
2. [Link ou Nome do Artigo 2 - Ex: PySpark Window Functions Documentation]
3. [Link ou Nome do Artigo 3 - Ex: Princípios de Gestão de Estoque no Varejo]

##  Engenharia de Prompts e "Cicatrizes" (Troubleshooting)
Durante o desenvolvimento das consultas e métricas com o auxílio da IA, documentei os seguintes aprendizados:
- **Prompt Utilizado para Modelagem:** *"Como criar chaves substitutas (Surrogate Keys) eficientes em tabelas Delta no Databricks usando PySpark?"*
- **Dificuldade Encontrada (Cicatriz):** Inicialmente, a IA sugeriu funções de incremento que não escalam bem em sistemas distribuídos (Spark). Para mitigar isso, evoluímos o prompt para utilizar funções de hash como `md5` combinando chaves naturais de negócio, garantindo performance e idempotência.

##  Miniguia de Estudo e Entrega Final
### Glossário de Conceitos Aprendidos
- **Lakehouse:** Arquitetura moderna que combina a flexibilidade de armazenamento do Data Lake com o gerenciamento de transações ACID do Data Warehouse.
- **Cobertura de Estoque:** Métrica que indica quantos dias o estoque atual durará com base na média de vendas recentes.
- **Ruptura de Estoque:** Evento onde a demanda por um produto não é atendida por falta de disponibilidade física na gôndola/depósito.

### Prompts Reutilizáveis para Revisões Futuras
- *"Explique como otimizar o particionamento de tabelas Delta na AWS focando em filtros por data de venda."*
- *"Gere um template de script PySpark para ler arquivos CSV incrementais de um bucket S3."*

### Visualizações no Power BI (Projeto Final)
O Dashboard final consome os dados da camada Gold para exibir:
- Taxa de ruptura por categoria de produto.
- Giro de estoque e curva ABC de faturamento vs. volume.
- Alertas visuais de produtos com cobertura abaixo do estoque de segurança.
