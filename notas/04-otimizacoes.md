# Otimizações de banco de dados

Coisas que dá pra fazer quando o banco começa a ficar lento. Quase todas trocam uma coisa pela outra, então o segredo é saber o que está sendo pago.

## Índices

É como o banco encontra os dados sem ler a tabela inteira. O preço: **escritas mais lentas** (o índice também precisa ser atualizado) e **mais armazenamento**.

### B-Tree

Guarda as chaves numa árvore balanceada e sempre ordenada. É o índice padrão da maioria dos bancos.

Bom para:
- Consulta por intervalo: `>`, `<`, `>=`, `BETWEEN`
- Ordenação e agrupamento: `ORDER BY`, `GROUP BY`
- Busca por prefixo: `LIKE 'Silva%'`
- Uso geral

```sql
SELECT * FROM clientes WHERE nascimento BETWEEN '1990-01-01' AND '1999-12-31';
SELECT * FROM clientes WHERE sobrenome LIKE 'Silva%';
```

### Hash

Aplica uma função de hash no valor da chave pra calcular direto onde o registro está. Serve pra acesso pontual.

Bom para buscas exatas: `=`, `IN`, `WHERE id = 100`.
**Não serve pra intervalo.** O hash espalha os valores, então a ordem se perde.

```sql
SELECT * FROM pedidos WHERE id = 100;
```

## Cache

Uma camada de armazenamento temporário em memória RAM, fora do banco. Os dados mais pedidos ficam ali (no Redis, por exemplo), e o banco nem é chamado.

## Materialized View / Snapshot

Uma tabela física com o resultado de uma consulta pesada **já calculado**. Quem guarda e mantém é o próprio banco. Em vez de rodar um JOIN enorme toda vez, a aplicação lê o resultado pronto.

## Réplicas de leitura

Na maioria dos sistemas tem muito mais leitura (READ) do que escrita (WRITE). Então um banco vira o **principal** (Master/Main), o único que escreve, e ele é copiado pra várias **réplicas** que só atendem leitura.

## Sharding e Teorema de CAP

Também são formas de otimizar, mas cada um tem nota própria: [Sharding](03-sharding-e-particionamento.md) e [Teorema de CAP](02-teorema-cap.md).

## Normalização x Denormalização

São opostas, e a escolha depende do que o sistema mais faz.

| | Normalização | Denormalização |
|---|---|---|
| O que é | Quebrar os dados em várias tabelas relacionadas | Guardar dados duplicados de propósito |
| Evita | Redundância | JOINs |
| Escrita | Mais fácil | Mais difícil (tem que atualizar várias cópias) |
| Leitura | Mais difícil (precisa de JOIN) | Mais rápida |

## Otimização de queries

Analisar como cada query roda pra descobrir o que melhorar. O ponto de partida é o `EXPLAIN`, que mostra o plano de execução: se está usando índice, se está lendo a tabela inteira etc.

```sql
EXPLAIN SELECT * FROM pedidos WHERE cliente_id = 42;
```

## Connection Pooling

Abrir uma conexão com o banco custa caro. O pool mantém um conjunto de conexões já abertas, na memória, prontas pra serem reutilizadas pelas requisições da aplicação.

## Batching e paginação

**Batching:** juntar várias leituras ou escritas numa chamada só, em vez de uma chamada por registro.

```sql
-- em vez de 3 INSERTs separados
INSERT INTO logs (msg) VALUES ('a'), ('b'), ('c');
```

**Paginação:** quando o resultado é grande, devolver um pedaço por vez (as "páginas") em vez de tudo de uma vez.

```sql
SELECT * FROM produtos ORDER BY id LIMIT 20 OFFSET 40; -- página 3
```
