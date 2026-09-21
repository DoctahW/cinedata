# CineData

Pipeline de dados para um catálogo de filmes (TMDB/IMDb), construído em **Databricks + PySpark + SQL** seguindo a Arquitetura Medalhão (Bronze → Silver → Gold). O objetivo é transformar arquivos CSV brutos em um modelo dimensional (Star Schema) pronto para BI, além de uma tabela de contexto para consumo por um assistente de IA (RAG).

## O que o pipeline faz

**Bronze**: ingestão bruta de 5 CSVs de entrada (informações, financeiro, métricas de engajamento, elenco/gêneros e avaliações de usuários) mais a cotação do dólar (API PTAX do Banco Central), sem alterar conteúdo, apenas adicionando timestamp de ingestão.

**Silver**: limpeza e padronização dos dados: normalização de texto, tratamento de tipos, deduplicação, tratamento de nulos/valores inconsistentes (símbolos monetários, separadores decimais, column shift, etc.) e conversão de gêneros/elenco/produtoras em dimensões próprias.

**Gold**: modelagem dimensional final:
- Star Schema com tabela fato (`fact_movies_performance`), dimensões (`dim_movies`, `dim_genres`, `dim_people`, `dim_companies`, `dim_reviews`) e tabelas bridge para relações N:N.
- Tabela `gold_genai_movies_context`, com um texto corrido por filme, pensada para alimentar um sistema de RAG.

A orquestração das três etapas é feita por um Job/Workflow do Databricks com dependências explícitas entre as tasks e execução agendada.

## Estrutura de arquivos

```
├── cinedata/
│   └── notebooks/
│       ├── Landing_to_Bronze.ipynb
│       ├── Bronze_to_Silver.ipynb
│       └── Silver_to_Gold.ipynb
├── job.yaml
└── README.md
```
