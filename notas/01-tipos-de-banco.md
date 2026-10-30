# Tipos de banco

São três famílias: relacional, não relacional e NewSQL. A diferença que mais importa no dia a dia é o schema: fixo ou flexível.

## Relacionais (SQL)

Têm **schema** definido. Cada tabela tem colunas certas, com tipo certo, e o banco não deixa você fugir disso.

Seguem o **ACID**:

| Letra | Nome | Na prática |
|-------|------|------------|
| A | Atomicidade | A transação acontece inteira ou não acontece. Não fica pela metade. |
| C | Consistência | Os dados sempre saem de um estado válido para outro estado válido. |
| I | Isolamento (Isolation) | Uma transação não enxerga a outra no meio do caminho. |
| D | Durabilidade | Deu commit, está gravado, mesmo se o servidor cair logo depois. |

**Exemplos:** MySQL, PostgreSQL, MariaDB, Oracle Database, SQLite.

## Não relacionais (NoSQL)

O schema é **flexível**: dois registros da mesma coleção podem ter campos diferentes. Em troca de abrir mão de algumas garantias, aguentam um **throughput alto**.

Seguem o **BASE**:

- **Basicamente Disponível:** o sistema quase sempre responde.
- **Estado Flexível** (Soft state): os dados podem mudar sozinhos por um tempo, enquanto as réplicas se acertam.
- **Eventual Consistência:** se ninguém escrever mais nada, uma hora todas as cópias ficam iguais.

Tem vários formatos:

| Tipo | Como guarda | Exemplos |
|------|-------------|----------|
| Documento | Documentos tipo JSON | MongoDB, RavenDB |
| Chave-valor | Uma chave aponta pra um valor | Redis, Amazon DynamoDB |
| Colunar | Famílias de colunas | Apache Cassandra |
| Grafo | Nós e relações entre eles | Neo4j, Amazon Neptune |

## NewSQL

> Ainda vou estudar. Pelo que vi até agora, a ideia é ter o ACID dos relacionais com a escala horizontal dos NoSQL. Exemplos que aparecem bastante: CockroachDB, Google Spanner e TiDB.
