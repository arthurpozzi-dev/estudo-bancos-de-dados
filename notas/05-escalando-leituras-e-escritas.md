# Escalando leituras e escritas

Quando muita gente lê e escreve o mesmo dado ao mesmo tempo, o banco precisa decidir quem espera quem. Nessa decisão, ele troca velocidade por consistência ou o contrário.

## Leituras

### Uncommitted Reads

A leitura acontece mesmo enquanto outro processo está escrevendo naquele dado. Ninguém espera, então a disponibilidade sobe. O risco é ler um valor que ainda não foi confirmado e que pode até ser desfeito (o chamado *dirty read*).

### Committed Reads

A leitura só enxerga o que já teve commit. Enquanto uma escrita está em andamento, quem lê vê a última versão confirmada ou espera ela terminar, dependendo do banco. Fica mais lento, mas a leitura é consistente.

## Escritas

### Locks

O lock trava a linha (row) enquanto a escrita acontece. Ele existe pra evitar a **race condition**, quando dois processos tentam alterar o mesmo dado ao mesmo tempo e um apaga o trabalho do outro.

### Lock pessimista

Parte do princípio de que vai dar conflito. Quando a escrita começa, a linha é travada inteira. A próxima alteração só é liberada depois que essa terminar.

```sql
BEGIN;
SELECT saldo FROM contas WHERE id = 1 FOR UPDATE; -- trava a linha
UPDATE contas SET saldo = saldo - 100 WHERE id = 1;
COMMIT; -- libera
```

### Lock otimista

Parte do princípio de que quase nunca dá conflito, então não trava nada. Cada linha tem uma **versão**. Na hora de salvar, o banco confere se a versão ainda é a mesma que foi lida. Se outro processo salvou antes, a versão mudou, e a segunda escrita é recusada e precisa tentar de novo.

```sql
UPDATE produtos
SET estoque = 9, versao = versao + 1
WHERE id = 7 AND versao = 3; -- se ninguém mexeu, atualiza 1 linha; se mexeu, 0
```

**Quando usar cada um:** o pessimista vale a pena quando o conflito é frequente (saldo, estoque disputado). O otimista funciona melhor quando o conflito é raro, porque ninguém fica esperando lock.
