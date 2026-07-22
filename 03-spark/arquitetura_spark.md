# O que eh o Spark

Spark eh um framework de computacao distribuida **divide as tarefas em diversas maquinas** no qual cada um (node/worker) processa os dados,e no fim o resultado sao reunidas.


## Cluster

clustes sao um conjuntos de maquinas(nos) disponivel para executar o spark, pode ser um cluster local ou em nuvem por exemplo (EMR/Databricks)

├── Worker 1
├── Worker 2
├── Worker 3
└── Worker 4

---

## Driver

É o processo "cérebro" da aplicação. É onde:

- Seu código Spark começa a rodar
- O plano de execução é criado (as tarefas são planejadas)
- As tarefas são distribuídas para os workers
- Os resultados finais são coletados

> Se o Driver morre, a aplicação toda para.

```
Driver
└── planeja e distribui o trabalho
```

---


## Cluster Manager

É quem **aloca os recursos** (CPU e memória) do cluster para a aplicação. Exemplos: YARN, Kubernetes, Mesos, ou o modo Standalone do próprio Spark.

O Driver pede recursos → o Cluster Manager libera as máquinas (workers) → o trabalho começa.

```
Driver → pede recursos → Cluster Manager → libera Workers
```

---

## Workers e Executores

- **Worker**: a máquina/nó física do cluster.
- **Executor**: processo que roda **dentro** do worker e executa as tarefas de verdade (e guarda dados em memória/cache).

Cada worker pode ter um ou mais executores. Cada executor roda várias tarefas em paralelo.

```
Worker
└── Executor
      ├── Task 1
      ├── Task 2
      └── Task 3
```

## Partições

Os dados não ficam inteiros em uma única máquina — eles são **divididos em pedaços chamados partições**. Cada partição vira uma task, processada por um executor, em paralelo.

- Mais partições → mais paralelismo (mas cuidado com overhead se forem pequenas demais)
- Poucas partições → pouco paralelismo, executores ociosos
- Regra prática: número de partições ≈ 2 a 4x o número de cores do cluster

```
Dados
├── Partição 1 → Task → Executor
├── Partição 2 → Task → Executor
└── Partição 3 → Task → Executor
```

---

## RDD vs DataFrame

| | RDD | DataFrame |
|---|---|---|
| O que é | Coleção distribuída "crua", baixo nível | Coleção distribuída organizada em colunas, como uma tabela |
| Otimização | Sem otimizador automático | Passa pelo Catalyst Optimizer, que otimiza o plano de execução |
| Performance | Geralmente mais lento | Mais rápido na maioria dos casos |
| Facilidade | Mais verboso (map, filter, reduce) | Mais parecido com SQL/Pandas |
| Quando usar | Casos bem específicos, controle fino | Praticamente sempre — é o padrão recomendado |

---

                 Você escreve PySpark
                          │
                          ▼
                Driver (cérebro da aplicação)
                          │
                          ▼
              Cluster Manager (aloca recursos)
                          │
          ┌───────────────┴───────────────┐
          ▼                               ▼
     Worker 1                        Worker 2
          │                               │
     Executor                        Executor
          │                               │
      Partição 1                     Partição 2
      Partição 3                     Partição 4
          │                               │
          └────────── Resultado ──────────┘
                          │
                          ▼
                        Driver
                          │
                          ▼
                        Você


```Python:

from pyspark.sql import SparkSession

# 1. Inicializa a sessão (O Driver entra em ação)
spark = SparkSession.builder \
    .appName("FiltroDeVendas") \
    .getOrCreate()

# 2. Lê os dados (O Driver divide o arquivo em partições)
df_vendas = spark.read.csv("s3://meu-bucket/vendas.csv", header=True, inferSchema=True)

# 3. Transformação (As TASKS são distribuídas para os EXECUTORES nos WORKERS)
df_filtrado = df_vendas.filter(df_vendas["valor"] > 100)

# 4. Ação (Os executores processam e mostram o resultado)
df_filtrado.show(5)


```