# Data manipulation


## Filtros

1. Filtro Simples

```{Python}

df_filtrado = df.filter(F.col("categoria")== "eletronico")
```

2. Multiplas condicoes


```{Python}

df.filter(
    (F.col("categoria) == "eletronico") &
    (F.preco(preci) > 100)
```

3. Valores Nao Nulos

```{Python}
df.filter(F.col("categoria").isNotull())

```

4. Valores Nulos

```Python:
nulos = df.filter(F.col("categoria").isNull())
```

5. Usando in


```Python:
produtos = ['desktop','tablet']
categorizado = df.filter(F.col("produtos").isin(produtos))

```

## Sorting

Famoso orderBY

```Python:

df.orderBy(F.col("age")) ##menor para o maior
df.orderBy(F.col("age").desc()) # Maior para menor

df.orderBY(F.col("caregoria"),F.col("preco").desc()) #Multiplas colunas

```


## Agregacoes


