# Sharding e particionamento

Quando um banco só não dá conta, a saída é dividir os dados. Cada pedaço fica menor, e a consulta fica mais rápida.

## Sharding

Sharding é dividir os dados entre **bancos diferentes**. Cada pedaço se chama **shard**.

### Horizontal: divide as linhas

Cada shard tem as mesmas colunas, mas linhas diferentes. A pergunta é como decidir em qual shard cada linha fica:

| Estratégia | Como funciona | Quem usa |
|------------|---------------|----------|
| **Range-Key** | Faixas de chave. Do 0001 ao 2000 num shard, do 2001 ao 4000 no outro. | MongoDB, Apache HBase, Google Bigtable |
| **Hash modular** | Aplica um hash na chave e tira o resto da divisão pelo número de shards (`hash(chave) % N`). | MySQL, PostgreSQL |
| **Hash consistente** | Os shards ficam num anel. A chave cai num ponto do anel e vai pro shard mais próximo. Adicionar ou remover shard mexe em bem menos dados do que no hash modular. | Dynamo, Cassandra, Riak |
| **Directory-Based** | Uma tabela central diz em qual shard cada chave está. | Azure Cosmos DB |

O hash modular tem um problema: se mudar o N (adicionar um shard), quase todas as chaves trocam de lugar. O hash consistente existe justamente pra evitar essa reorganização gigante.

### Vertical: divide as colunas

Cada shard guarda um grupo de colunas. Por exemplo, os dados de login numa base e o histórico de compras em outra.

### Hot e Cold

Os shards não recebem o mesmo tanto de acesso, então dá pra tratar cada um de um jeito:

- **Hot Shard:** os mais acessados. Ficam no melhor hardware, sempre à disposição.
- **Cold Storage / Archives:** dados "abandonados", que quase ninguém acessa. Podem ir pra um armazenamento mais lento e mais barato.

## Partition

Particionar também é dividir os dados, mas aqui a separação pode ser de dois jeitos:

- **Física:** o banco é separado em máquinas diferentes.
- **Lógica:** os dados são separados em blocos, que podem continuar na mesma máquina.

## Global Secondary Index

Uma tabela central que direciona a pesquisa pro shard ou partição certa. Ela resolve a busca por um campo que **não** é a chave de divisão. Sem ela, essa busca teria que perguntar pra todos os shards.
