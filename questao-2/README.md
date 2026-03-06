# Questão 2 — PIT Telecom / Oi: Banda Passante vs. Latência

Abra o arquivo `simulador.html` no navegador e clique em **"Iniciar Simulação"**. As duas rotinas transmitem 10 MB cada pelo mesmo link de alta velocidade.

---

## Análise

Banda passante é a capacidade máxima de um link de rede transmitir dados por unidade de tempo, medida em bits por segundo (Mbps, Gbps). É como a largura de uma rodovia: define quantos carros (bits) podem circular ao mesmo tempo. Ter mais banda significa poder enviar mais dados em paralelo, mas não significa, necessariamente, que os dados vão chegar mais rápido, pois banda e latência são coisas diferentes. Latência é o tempo que um único pacote leva para sair da origem, chegar ao destino e ter sua confirmação de recebimento retornada. Em links de fibra óptica, a banda pode ser enorme, mas a latência por pacote ainda existe e não desaparece só porque o link é rápido.

A simulação deixa isso muito claro. A Rotina A envia os 10 MB como um único bloco contínuo: o link é usado quase por completo durante toda a transmissão e a confirmação só precisa acontecer uma vez no final, então o dado chega rápido. A Rotina B divide os mesmos 10 MB em 10.000 pacotes de 1 KB e precisa esperar a confirmação individual de cada um antes de enviar o próximo. Cada confirmação custa um tempo de latência. Multiplique esse atraso por 10.000 e o tempo acumulado domina o tempo total de transmissão, mesmo que o link tenha alta velocidade, a maior parte do tempo ele fica parado esperando respostas, resultando em um throughput efetivo muito baixo.

Por isso é tão importante que o desenvolvedor conheça as características do link ao projetar sua aplicação. Um sistema que faz muitas chamadas de rede pequenas e sequenciais, como um protocolo que confirma cada registro individualmente antes de enviar o próximo, vai ter desempenho ruim mesmo em fibra óptica de última geração. A solução é adequar a aplicação ao link: agrupar dados em transmissões maiores (batching), usar protocolos que não exijam confirmação por pacote, ou empregar técnicas como pipeline e janelas de transmissão para enviar vários pacotes antes de esperar as confirmações. O marketing pode prometer que "a internet não vai travar", mas a culpa pode estar na arquitetura da aplicação, não no link.