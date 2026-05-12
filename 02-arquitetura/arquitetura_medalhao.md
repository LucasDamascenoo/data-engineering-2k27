# Arquiteteura Medalhao

Eh a forma que vamos organizar os dados no processo do ETL/ELT (comumente) utilizando com ELT(datalakes).

## Bronze (raw ou landing)

Onde armazenamos os dados brutos diretamete da origem(api,csv,json,parquet).

1. **Copia fiel da origem**: os dados chegam conforme a origem, sem nenhuma transformacao. Se a fonte tem erro, duplicadas, a bronze tbm tera.

2. **Imutavel**: os dados na bronze nunca sao alterados apos a ingestao.

3. **Multi-formato**: aceita qualquer formato — JSON, CSV, Parquet, Avro, respostas de API, mensagens de Kafka, dumps de banco etc.

4. **Append-only**: os novos dados sao adicionados,nunca subescrito, isso garante rastreabilidade.



## Silver (curated)

Onde os dados comecam a serem tratatos (normalizados, filtrados)

1. **Limpeza**: remoção de duplicatas, tratamento de nulos, correção de tipos de dados ("150.00" string → 150.00 float)

2. **Normalização**: padronização de formatos — datas no mesmo padrão, strings em lowercase, códigos de país no mesmo padrão ISO

3. **Filtros**: descarte de registros inválidos ou irrelevantes para o negócio
Joins: combinação de tabelas de origens diferentes (ex: cruzar dados de pedidos com dados de clientes)

4. **Enriquecimento**: adição de colunas calculadas ou derivadas (ex: idade calculada a partir de data_nascimento)
Validação de qualidade: aplicação de regras de negócio para garantir integridade dos dados


**A Silver não é uma camada de agregação — os dados ainda ficam no nível de granularidade original (uma linha por transação, por evento, por registro). A agregação e sumarização acontecem na Gold. A Silver entrega dados limpos, mas ainda detalhados.**



## Gold (serving)

Nossa etapa final, os dados ja estao otimizados, onde comecam a serem usados no bi, equipe de ml, analicts entre outros.

1. Inclusao de KPIS
2. Metricas de negocios
3. Tabelas analiticas

**Nessa etapa fazemos agregacoes**

![Arquitetura Medalhao](/img/medallion_architecture.svg)