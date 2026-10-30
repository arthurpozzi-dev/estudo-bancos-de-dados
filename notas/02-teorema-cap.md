# Teorema de CAP

É o conceito que eu uso pra pensar nos trade-offs de qualquer sistema distribuído. Vale pra banco de dados e pra qualquer outro sistema que rode em mais de uma máquina.

## As três letras

**C: Consistency (Consistência)**
Toda leitura devolve o dado mais atualizado.

**A: Availability (Disponibilidade)**
O sistema responde aos requests mesmo que partes dele falhem ou fiquem fora do ar.

**P: Partition Tolerance (Tolerância a partição)**
O sistema continua funcionando mesmo quando a rede entre as máquinas quebra e elas param de se falar.

## O que o teorema diz

Não dá pra ter os três ao mesmo tempo. Só que, num sistema distribuído, a rede uma hora vai falhar, então o **P não é bem uma opção**. A escolha real acontece quando a partição acontece:

- **CP:** prefiro recusar o request a devolver um dado desatualizado.
- **AP:** prefiro responder sempre, mesmo que o dado esteja um pouco velho.

Por isso, na maioria das vezes, a decisão fica entre **consistência e disponibilidade**.

## Exemplo pra fixar

Um saldo bancário pede **CP**. É melhor dar erro do que mostrar um saldo que não existe mais.

O contador de curtidas de um post aguenta **AP**. Se alguém ver 1.203 em vez de 1.205 por alguns segundos, ninguém sai prejudicado.
