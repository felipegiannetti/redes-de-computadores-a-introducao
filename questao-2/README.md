# Questão 2 — PIT Telecom / Oi: Banda Passante vs. Latência

## Como executar

Abra o arquivo `simulador.html` no navegador e clique em **"Iniciar Simulação"**. As duas rotinas transmitem 10 MB cada pelo mesmo link de alta velocidade. Observe a diferença no tempo de conclusão entre a Rotina A (1 pacote gigante) e a Rotina B (10.000 pacotes minúsculos).

---

## Análise

### O conceito técnico de Banda Passante

**Banda passante** (ou largura de banda) é a capacidade máxima de um link de rede transmitir dados por unidade de tempo, medida em bits por segundo (bps, Kbps, Mbps, Gbps). É como a largura de uma rodovia: define quantos veículos (bits) podem trafegar simultaneamente num dado instante.

É fundamental distinguir dois conceitos frequentemente confundidos:

| Conceito | Definição | Analogia |
|---|---|---|
| **Banda passante (Bandwidth)** | Capacidade máxima do canal em bits/s | Largura da rodovia |
| **Latência (RTT)** | Tempo para um pacote ir ao destino e voltar | Velocidade máxima permitida na rodovia |
| **Throughput efetivo** | Taxa real de transferência medida na prática | Fluxo médio de veículos por hora |

Alta banda passante **não garante** alto throughput efetivo quando a aplicação gera muitos pacotes pequenos com confirmações individuais — a latência por pacote domina o tempo total.

### O que a simulação demonstra

**Rotina A — 1 pacote gigante (stream contínuo):**

- Os 10 MB são enviados como um único bloco contínuo.
- O link é utilizado próximo de sua capacidade máxima durante toda a transmissão.
- A confirmação de entrega acontece apenas uma vez, ao final.
- **Resultado:** transmissão rápida, throughput próximo da banda contratada.

**Rotina B — 10.000 pacotes minúsculos (latência acumulada):**

- Os mesmos 10 MB são fragmentados em 10.000 pacotes de ~1 KB.
- Cada pacote exige uma confirmação (ACK) individual antes de enviar o próximo.
- Cada confirmação custa um tempo de latência (ex: 20 ms de RTT).
- **Cálculo:** 10.000 pacotes × 20 ms = **200 segundos** apenas em espera por ACKs.
- O link fica ocioso a maior parte do tempo, aguardando respostas.
- **Resultado:** throughput efetivo colapsado, mesmo com link de alta velocidade.

### Por que mesmo um link rápido pode ficar lento

A promessa de marketing da PIT Telecom ("sua internet não vai mais travar") é enganosa porque trata **banda passante** como solução universal. Na prática:

- Um link de fibra óptica de 1 Gbps com latência de 50 ms entrega **menos dados por segundo** numa transmissão de 10.000 pacotes sequenciais do que um link de 10 Mbps otimizado com janelas de transmissão.
- A velocidade da luz no vidro (~200.000 km/s) é um limite físico inviolável. Mais banda não reduz o tempo de propagação.
- Protocolos que exigem confirmação por pacote (ex: protocolos de transferência de arquivo mal projetados, consultas de banco de dados síncronas, APIs REST sequenciais) sofrem diretamente com a latência, independente da banda disponível.

### Como adequar sua aplicação às características do link

O engenheiro de software deve conhecer as características do link para projetar aplicações de alto desempenho:

- **Batching:** agrupar múltiplos registros numa única transmissão em vez de enviar um por vez.
- **Pipelining / Janela de transmissão:** enviar vários pacotes antes de aguardar confirmações (mecanismo já presente no TCP com *window scaling*).
- **Protocolos assíncronos:** usar filas de mensagens (ex: Kafka, RabbitMQ) para desacoplar envio de confirmação.
- **Compressão:** reduzir o volume de dados transmitidos, especialmente relevante em links com banda limitada.
- **Conexões persistentes:** evitar o overhead do handshake TCP a cada requisição (use HTTP/2 ou conexões keep-alive).

O problema da Rotina B não é a velocidade do link — é a arquitetura do protocolo. Aumentar a banda de 100 Mbps para 1 Gbps na Rotina B reduziria o tempo total de transmissão em segundos, mas os 200 segundos de latência acumulada permaneceriam idênticos.
