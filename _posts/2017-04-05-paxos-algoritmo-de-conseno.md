---
layout: post
title: Paxos — Algoritmo de consenso
subtitle: Algoritmo de consenso muito utilizado para Sistemas Distribuídos
bigimg: /img/paxos.jpeg
---

Algoritmo de consenso muito utilizado para Sistemas Distribuídos, Eleições de Líder e Locks Distribuídos, sua maior caracterista organizar quais processos devem ser escolhidos para serem executados.

O algoritmo apenas escolhe um valor proposto se ele realmente aprendeu sobre o mesmo.

Paxos usa cinco mensagens distintas para alcançar resolução dos problemas. As descrições a seguir fornecem uma visão geral do papel de cada mensagem e especificar o conteúdo necessário. (Permissão de Pedido, de Garantia, Sugestão, Aceitação e Nack), todas essas mensagens usam IDs que asseguram a legitimidade das mensagens e garantem que não haja duplicação dentro da rede.

Existem várias maneiras do algoritmo evidenciar erros dentro de uma rede (mensagens duplicadas, envios de mensagens ao mesmo tempo) e após encontrar essas falhas, a rede entra em processo de inicialização e um novo processo de eleição é iniciado.

Existem três tipos de agentes proposers, acceptors e learners, o algoritmo pode usar um único agente aceitador, com um único líder as chances de ocorrer uma falha é muito menor, pode também ser usado vários agentes aceitadores, mas os critérios de aceitação mudam, para um valor proposto ser escolhido a maioria dos agentes devem concordar com essa escolha. Um agente de aceitação só pode escolher um valor proposto, se várias propostas de processos diferentes forem enviadas ao mesmo tempo, possa ser que nenhum valor seja escolhido.

Os proposers perguntam aos aceitadores quais são os seus critérios de aceitação então, cada proposers enviam suas propostas para os agentes líderes, se os critérios forem válidos essa proposta é marcada com um número incremental e a proposta com maior número de aceite é escolhida.

Implementação — O algoritmo de Paxos assume uma rede de processos e um processo é escolhido como líder para que, possa distribuir os papéis que cada processo deve ter dentro da rede. Os envios de mensagens dos clientes através da rede são identificados por numeração que não podem ter o mesmo valor número para evitar falhas de quais mensagens devem ser escolhidas pelo líder da rede. Aceitantes agi como a “memória” tolerante a falhas do protocolo, pode formar Quóruns. Qualquer mensagem enviada para um aceitador deve ser enviada para um quorum de aceitadores. Qualquer mensagem recebida de um receptor é ignorado a menos que uma cópia é recebida de cada Acceptor em um Quorum (por exemplo, Promises enviadas para um Proponente, ou mensagens aceites enviado para um Learner). A Proponente defende uma solicitação do cliente, tentando convencer os Aceitantes concordar com isso, e agindo como um coordenador para mover o protocolo para a frente quando ocorrem conflitos. Os lerner agem como o fator de replicação para o protocolo. Uma vez que um pedido de cliente foi acordado pelos aceitantes, o aprendiz pode tomar as medidas (ou seja: executar o pedido e enviar uma resposta para o cliente). Para melhorar a disponibilidade de processamento, alunos adicionais podem ser adicionados. Paxos requer um Proponente distinto (chamado de líder) a fazer progressos. Muitos processos podem acreditar que eles são líderes, mas o protocolo só garante o progresso se um deles eventualmente é escolhida. Se dois processos acreditam que eles são os líderes, eles podem parar o protocolo propondo continuamente atualizações conflitantes. No entanto, as características de segurança são preservadas ainda nesse caso.

Implementando uma Máquina de Estado — Como uma boa implementação de uma máquina de estado determinístico cada processo deve executar uma sequência de passos e apresentar sempre seu estado atual para cada vez que o processo passar por um estado. Cada líder de uma rede deve aprender sobre tudo que os demais processos das redes estão executando para ter um aprendizado constante, para uma eventual eleição de um novo líder ou para qual mensagem deve ser escolhida para ser executada dentro da rede.

Exatidão — Enquanto as regras de mensagens são respeitados, exatidão é assegurada pelo algoritmo e é independente de todas as outras considerações de aplicação.
Garantir Avanço — A repetição e recuo lógica definida pelo aplicativo usado para garantir que a resolução é finalmente alcançado.
Aprender o resultado final — O mecanismo definido pelo aplicativo usado para garantir que todos os pares, eventualmente, aprender o resultado, uma vez consenso foi alcançado.
Otimizações de latência — técnicas opcionais que podem reduzir o número de idas e voltas necessárias para obter uma resolução sob certas circunstâncias.
Nível de Aplicação Lógica — Como o aplicativo usa os resultados de casos Paxos
Exemplo de Resolução de Conflito


CASO 1

O caso mais simples é o primeiro. Deve ser restaurada comunicação entre C, D, e E, um consenso pode ser alcançada de imediato sobre o valor de bar. E simplesmente precisa pedir C para aceitar a sugestão (2, E) e seu valor associado Bar. Uma vez C aceitou a sugestão e id valor associado, o consenso foi alcançado e Bar torna-se o valor final.


CASO 2

Se, por outro lado, a comunicação é restaurada entre A, B, e C, é possível que Foo a ser escolhido. Ao contrário do caso anterior, porém, um passo extra é necessária. Quando uma pergunta C para aceitar id sugestão (1, A) e seu valor associado Foo, C irá rejeitar o pedido. Ele é forçado a fazê-lo porque ele concedeu permissão para E para fazer a sua sugestão de (2, E) no passo 2 do cenário. Ao conceder essa permissão, C prometeu implicitamente a rejeitar todas as mensagens com ids sugestão de menos de (2, E) .

Ao ver a rejeição da sua mensagem de aceitação, Um pode simplesmente reiniciar o processo de resolução com um novo ID de sugestão de que é superior (2, E). Fazê-lo com o novo ID sugestão de (3, A) , por exemplo, pode resultar em concessões de permissões que mostram que a maior id sugestão anteriormente aceites de a maioria dos pares é (1, A) e com o valor correspondente Foo . Por a exigência Paxos, A seria então forçado a usar o valor Foo quando ele solicita a aceitação da (3, A). Uma vez que um , B, e C aceitar a nova sugestão, Foo torna-se o valor final.


CASO 3

Como descrito pelos dois casos anteriores, é possível que ambos os Foo ou barra a ser escolhido como o valor final. O que é não possível, no entanto, é para qualquer outro valor de Foo ou barra a ser escolhido.

Por exemplo, se pares C queria sugerir Baz como o valor, seria incapaz de fazê-lo uma vez que qualquer maioria dos concede permissão que incluem pelo menos uma referência a uma previamente aceite Foo ou Bar. Se C fosse para restabelecer a comunicação com A e B, ele seria forçado a definir o valor para a sua proposta de Foo desde os concede permissão deve especificar Foo como um valor previamente aceite. Se se receber uma resposta de D ou E , em complemento ou em vez de um ou B, seria forçado a ajustar o valor de Bar desde (2, E) > (1, A).

Conclusão

O algoritmo de consenso tem como finalidade tomar as melhores decisões dentro de uma rede de sistemas distribuídos, com base em conhecimentos adquiridos ao longo do tempo. O líder não pode escolher uma mensagem emitida por algum processo sem ter a certeza que é melhor escolha há ser feita.

Caso Real:

Building Distributed Log: Twitter’s high-performance replicated log service

Twitter desenvolveu um sistema de replicação e armazenamento de logs de alto desempenho e disponibilidade, tentando resolver os desafios de complexidades de endereçamento de uma quantidade muito grande de dados em tempo real. Usando Paxos ou Raft o Twitter achou uma maneira de solucionar alguns desses problemas, tais como, Abstração orientadas ao fluxo onde os detalhes do gerenciamento do Led BookKeeper estão completamente ocultos, Tubulações de leitura e gravação sintonizáveis DistributedLog fornece um mecanismo batching altamente ajustável no caminho de gravação. As aplicações podem negociar latência para a taxa de transferência limpando periodicamente, imediatamente ou uma vez que o buffer de gravação atinge um determinado tamanho e muito mais. Para saber mais sobre o funcionamento do DistributedLog acesse o link.

Funcionamento do DistributedLog

Um cliente envia uma solicitação de comparação e configuração para um nó de Manhattan chamado Coordenador
O Coordenador escreve a solicitação de comparação e configuração em seu log DistributedLog correspondente, gerenciado pelo serviço DistributedLog
O serviço DistributedLog aceita gravações do Coordenador e as grava no Ledger apropriado no cluster BookKeeper
As réplicas de Manhattan lêem de seu registro associado e atualizam o backend aplicando operações de comparação e configuração em ordem — isso garante que o resultado será o mesmo em todas as réplicas
As réplicas de Manhattan enviam o resultado de comparar e definir de volta ao Coordenador
Depois que o coordenador recebe resultados de um quorum de réplicas de Manhattan, envia a resposta de volta ao cliente

Links de Referência:

https://pt.slideshare.net/mayconbordin/slides-26934789
https://prezi.com/dlxolvmhugkf/algoritmo-de-paxos/
https://repositorio.ufscar.br/bitstream/handle/ufscar/638/PAULA_Anderson_2015.pdf?sequence=1
https://pt.wikipedia.org/wiki/Consenso_Distribu%C3%ADdo
https://github.com/gdub/python-paxos
https://understandingpaxos.wordpress.com/
https://blog.twitter.com/2015/building-distributedlog-twitter-s-high-performance-replicated-log-service