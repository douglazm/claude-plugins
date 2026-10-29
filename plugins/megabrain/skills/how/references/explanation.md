# Formato da explicação

Na língua do dev. Omita a seção que não se aplica.

### Visão geral
Um ou dois parágrafos: o que é, o que faz, por que existe. Quem ler só isso decide se continua.

### Conceitos
Os tipos, entidades e serviços necessários para seguir o resto. Definição curta.

### Como funciona
A parte maior. O que dispara, o que acontece passo a passo, por onde o dado vai, onde estão as
decisões. Prosa, não pseudocódigo. `arquivo:linha` para cada passo; bloco de código só quando
um trecho é essencial.

Fluxo com várias peças conversando: diagrama mermaid ou ASCII. Com três peças ou mais, não
desenhe tudo de uma vez: monte em sequência, cada diagrama redesenha o anterior e acrescenta
uma peça. Se a prosa basta, sem diagrama.

### Onde mora
Mapa curto de arquivos e pastas, só os que alguém precisa para começar.

### Armadilhas
Comportamento surpreendente, pré-condição escondida, divergência com a `.megabrain/`. Omita se
não houver.

## Estilo

- Concreto: "`CloseCashRegister` chama `IPaymentRepository.findByCashRegister`", não "o caso
  de uso delega ao repositório".
- Quando algo é complexo, diga por que é complexo. Quando é simples, não encha.
- Lacuna dos exploradores aparece, não se esconde.
