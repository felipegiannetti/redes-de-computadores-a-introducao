# Questão 2 — Banda Passante

Abra o arquivo `simulador.html` no navegador. Teste os três botões: **Apenas Rotina A**, **Apenas Rotina B** e **A + B Simultâneas**.

---

## Análise

Banda passante é a capacidade máxima de um link de rede transmitir dados em um determinado intervalo de tempo, medida em bits por segundo (Mbps, Gbps). É como uma rodovia: o número de faixas define quantos carros podem trafegar ao mesmo tempo. Ter um link de 100 Mbps não garante que toda aplicação vai funcionar bem — significa apenas que o total de dados trafegando não pode ultrapassar esse limite sem consequências.

Quando a Rotina A (download de 800 MB) roda sozinha, ela ocupa quase toda a banda disponível e funciona perfeitamente, porque transferências em massa são tolerantes a atraso — não importa se um pacote levou 50 ms a mais para chegar, o arquivo final fica completo do mesmo jeito. Já a Rotina B (chamada de vídeo) funciona bem quando está sozinha porque tem o link inteiro disponível, com latência baixa e jitter estável. O problema aparece quando as duas rodam ao mesmo tempo: a Rotina A engole a banda, sobra muito pouco para o vídeo, a latência dispara, o jitter fica instável e pacotes começam a ser perdidos — a chamada trava mesmo com um link de 100 Mbps.

Isso mostra que não basta ter um link rápido: o tipo de tráfego importa. Aplicações em tempo real como vídeo, VoIP e jogos online são extremamente sensíveis a latência e jitter, mas usam pouca banda. Aplicações de transferência de arquivos usam muita banda, mas aguentam atraso. Por isso é fundamental que o desenvolvedor entenda as características do link e configure a aplicação adequadamente — usando QoS para priorizar tráfego sensível, limitando a taxa de transferência de operações em massa ou separando os tipos de tráfego em canais diferentes. Ignorar isso garante que usuários de um sistema vão ter experiências ruins mesmo em infraestruturas modernas.
