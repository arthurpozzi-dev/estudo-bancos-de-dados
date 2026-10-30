# Federation DB

Entra em cena quando os dados precisam ficar em **mais de uma região física**: outro datacenter, outra zona, outro país. O Federation é o sistema que gerencia, localiza e coordena a leitura e a escrita de dados espalhados por essas regiões.

Tem três jeitos principais de montar isso.

## Federated Query Layer

Uma API com um **cache global**, num lugar só, que guarda o id de cada dado e em qual região ele está. A aplicação pergunta pra essa camada, e ela sabe pra onde mandar.

- **A favor:** é simples.
- **Contra:** latência maior, porque toda consulta passa por esse ponto central antes de chegar no dado.

## Read Replica em cada região

Cada região tem servidores de leitura com cópia dos dados. A escrita continua indo pra região "dona" do dado.

- **A favor:** leitura global com baixa latência, porque cada um lê da réplica mais perto.
- **Contra:** gasta bem mais armazenamento, porque os dados ficam copiados em todo lugar.

## Arquitetura Mediador e Wrapper

Serve quando os bancos de cada região nem são do mesmo tipo (um PostgreSQL aqui, um MongoDB ali).

**Mediador (Mediator)**
Recebe a consulta SQL global da aplicação, monta o plano de execução distribuído, quebra a consulta em subconsultas pra cada banco e junta os resultados no final.

**Wrappers (Conectores)**
São os tradutores. Cada wrapper pega a subconsulta do mediador e converte pra linguagem e protocolo do banco dele (por exemplo, de SQL pra uma query do MongoDB). Depois, padroniza o formato dos dados que voltam.

```
Aplicação
   │  SQL global
   ▼
Mediador ── divide a consulta ──┬──► Wrapper PostgreSQL ──► PostgreSQL
                                └──► Wrapper MongoDB    ──► MongoDB
   ▲
   └── junta os resultados e devolve pra aplicação
```
