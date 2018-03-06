---
layout: post
title: O teorema de CAP – Utilizado para auxiliar um servidor de sistema distribuídos
subtitle: Teorema CAP, também chamado de Teorema de Brewer
bigimg: /img/cap.png
---

Teorema CAP, também chamado de Teorema de Brewer, afirma que é impossível que o armazenamento de dados distribuído forneça simultaneamente mais de duas das três garantias seguintes:

- **Consistência**: Cada leitura recebe a escrita mais recente ou um erro.

- **Disponibilidade**: Cada pedido recebe uma resposta (sem erro) – sem garantia de que contém a escrita mais recente.

- **Partição**: sistema continua a funcionar apesar de um número arbitrário de mensagens serem descartadas (ou atrasadas) pela rede entre nós.

Em outras palavras, o Teorema CAP afirma que, na presença de uma partição da rede, é preciso escolher entre consistência e disponibilidade. Observe que a consistência conforme definido o mesmo é bastante diferente da consistência garantida em transações de bases de dados ACID.

O Teorema CAP é utilizado para auxiliar um servidor de sistema distribuídos a encontrar as respostas corretas para suas determinadas solicitações, como uma requisição pode ter um número massivo de respostas corretas usando o CAP isso pode ser minimizado até que seja encontrado o resultado correto, consistência é a palavra que melhor define esse protocolo de sistema distribuídos.

Como toda boa implementação de sistema distribuído o CAP também se preocupa com a disponibilidade que os seus sistemas tentam transmitir alguma resposta para suas solicitações.

Partições compartilhadas também fazem parte das suas especificações de um sistema distribuído, isso ajuda a manter uma alta disponibilidade ao sistema, mas tem um lado negativo as mensagens de respostas pode ser atrasadas ou até mesmo defasadas a depender de quando os servidores foram particionados.

Suas requisições são tratadas de maneira assíncronas (ou seja, os processos não têm a priori limite sobre o quanto tempo leva para uma mensagem ser entregue), é possível que a mesma situação ocorra mesmo em cenários que não houveram mensagens perdidas.

Assim, a partir desta perspectiva, o Teorema CAP afirma que é impossível para qualquer protocolo de implementação atômico de leitura/escrita garantir segurança e vivacidade em um sistema propenso a partições compartilhadas.

Surpreendente é que tal sistema de consenso é impossível no cenário do Teorema CAP, porque cada protocolo de consenso deve garantir concordância e validade das suas informações dentro de sua rede e o mesmo temos que lidar com partições compartilhar isso pode ficar complicado ao decorrer do tamanho que cada partição começa assumir dentro da rede.

O grande problema está em encontrar a melhor estratégia de sincronia de dados para o Teorema CAP.

Mesmo com todas dificuldades citadas aqui os desenvolvedores de sistema destruídos continuam a desenvolver sistemas com qualidade e satisfação para o cliente final, mas claro tendo que fazer uns sacrifícios para disponibilizar os sistemas com qualidade satisfatória. Como mostrado o Teorema CAP comprova que em um sistema distribuído não podemos alcançar consistência e disponibilidade sendo necessário encontrar um equilíbrio entre ambas as partes.

Talvez a abordagem mais comum para lidar com redes não confiáveis seja conceber um serviço que garanta, ou seja, operações corretas, independente do comportamento da rede. Otimizar os serviços também é uma boa alternativa para conseguir esse resultado por exemplo, o Chubby Lock Service do Google atualmente utilizado na sua infra-estrutura para garantir um serviço de respostas para pesquisas de DNS.

Uma abordagem bastante comum das limitações do Teorema CAP é redesenhar o sistema, segmentando-o em componentes que oferecem diferentes tipos de garantias. Ao fazê-lo, podemos ter um serviço como um todo, que não garante nem a consistência e nem a disponibilidade. No entanto, no final, cada parte do serviço fornece exatamente aquilo que é solicitado.

**Escalabilidade**: Cada vez mais exigimos que nossos sistemas sejam cada vez escaláveis, projetados não apenas para os clientes hoje, mas também amanhã. Intuitivamente, nós pensamos em um sistema escalável que possa crescer de maneira eficiente utilizando os recursos mais modernos que temos hoje a nossa disposição de forma que podemos lidar com o aumento de carga de requisições que parte de todos os tipos de dispositivos.

**Tolerar ataques**: com o poder computacional aumentando cada vez mais, cada vez mais sistemas têm sido alvos de ataques de negação de serviços, por isso é importante ter uma boa estratégia de compartilhamento de partições por assim quando algum servidor for inutilizado outros poderam suprir a suas ausência acarretando assim uma perda de eficiência do sistema, mas ainda assim garantindo disponibilidade.

### Exemplos:

1. Se houvesse qualquer falha na rede ou hardware, comprometendo a comunicação entre estes dois nós, quando o processo B requeresse a leitura destes dados ao nó X’, este iria receber como resposta V=V0, que não é o valor atualizado de V, presente apenas no nó X. Logo neste exemplo, vê-se que a consistência dos dados fica comprometida.

2. Para privilegiar a consistência dos dados, neste exemplo foi tratada a escrita e a replicação dos dados como um processo único e homogêneo. Porém se qualquer falha de rede impedisse a replicação dos dados, o processo B teria que esperar até que isto fosse solucionado para realizar a leitura dos dados. Assim a disponibilidade do sistema ficaria comprometida.

Consistência e tolerância a partição, comprometendo a disponibilidade (CP): Seguindo a lógica da situação 2, na iminência da falha de um nó, este sistema pode ficar indisponível por bastante tempo. Recomendado para casos em que os dados devem ser consistentes, por exemplo, transações monetárias.

Consistência e disponibilidade, comprometendo a tolerância à partição (CA): Majoritariamente encontrado nas bases de dados relacionais, estes sistemas não sabem lidar com uma eventual falha de um nó, caso isso ocorra, o sistema inteiro pode ficar indisponível até o membro do cluster voltar.

Tolerância a partição e disponibilidade, comprometendo a consistência (AP)

### Referencias:

- http://www.geati.ifc-camboriu.edu.br/wiki/index.php/Exemplo_explicativo_do_Teorema_CAP
- https://dzone.com/articles/better-explaining-cap-theorem
- https://groups.csail.mit.edu/tds/papers/Gilbert/Brewer2.pdf