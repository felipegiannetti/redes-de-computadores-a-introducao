# Questão 6 — Missão TRE-MG: Confiabilidade na Transmissão de Votos

## Como executar

Abra o arquivo `simulador.html` no navegador. Clique em **"▶ Iniciar UDP"** para ver a transmissão sem conexão — observe os pacotes sendo perdidos sem retransmissão. Em seguida clique em **"▶ Iniciar TCP"** para ver o handshake, os ACKs de confirmação, as retransmissões automáticas e a entrega garantida de todas as urnas.

---

## Análise

### Observando os logs do simulador: diferença no tratamento de pacotes

**UDP (User Datagram Protocol):** O log mostra pacotes sendo disparados imediatamente, sem nenhuma preparação prévia. Quando um pacote é perdido (30–35% de chance simulada), não há nenhuma ação de recuperação — o resultado eleitoral fica permanentemente incompleto. Não há confirmação de entrega, nem ordenação garantida, nem mecanismo de retransmissão. É um protocolo *fire-and-forget*.

**TCP (Transmission Control Protocol):** O log demonstra o processo completo:

1. **3-way Handshake:** `SYN → SYN-ACK → ACK` — uma conexão lógica é estabelecida antes de qualquer dado ser enviado.
2. **Envio com confirmação:** Cada urna é enviada com um número de sequência. O receptor envia um `ACK` confirmando o recebimento.
3. **Retransmissão automática:** Se o ACK não chega dentro do timeout, o TCP detecta e reenvia o pacote automaticamente.
4. **Ordenação garantida:** Mesmo que pacotes cheguem fora de ordem, o TCP os reordena antes de entregar à aplicação.
5. **Encerramento gracioso:** `FIN → FIN-ACK` fecha a conexão formalmente, garantindo que todos os dados foram entregues.

**Resultado:** TCP entrega 100% das urnas, mesmo quando há perdas na rede. UDP pode entregar menos que 100% sem qualquer aviso.

### Qual a melhor escolha para o TRE-MG? Por quê?

**TCP é a única escolha viável.** No contexto de uma eleição:

- **Integridade é inegociável:** a perda de uma única urna invalida o resultado eleitoral de seção inteira. O UDP não oferece nenhuma garantia de entrega — qualquer perda de pacote simplesmente é ignorada.
- **Ordenação importa:** os registros eleitorais precisam ser processados na sequência correta para auditoria e consistência dos totalizadores.
- **Detecção de erros:** o TCP utiliza checksums para detectar corrupção de dados em trânsito.
- **Overhead aceitável:** o overhead do handshake e dos ACKs é desprezível num link dedicado de 50 Mbps com transmissão que ocorre apenas uma vez a cada eleição.

O UDP seria aceitável para aplicações onde velocidade supera confiabilidade (streaming de vídeo ao vivo, jogos online), mas é completamente inadequado para transmissão de dados críticos como votos.

### Diferença entre link orientado a conexão e protocolo orientado a conexão

São conceitos de **camadas diferentes** e não devem ser confundidos:

| Conceito | Camada | O que significa |
|---|---|---|
| **Link dedicado orientado a conexão** | Física / Enlace | O meio físico é **reservado exclusivamente** para os dois pontos. O circuito existe permanentemente, independente de haver tráfego. Analogia: uma linha telefônica fixa dedicada entre dois prédios. |
| **Protocolo orientado a conexão (TCP)** | Transporte | Uma **conexão lógica** é estabelecida por software (handshake). O meio físico pode ser compartilhado. A "conexão" existe apenas enquanto os endpoints concordam em mantê-la. |

No caso do TRE-MG:

- O **link dedicado** garante que a banda de 50 Mbps está sempre disponível, sem competição com outros usuários — nenhum tráfego de terceiros pode saturar o canal no dia da eleição.
- O **TCP** garante, na camada de transporte, que cada pacote é confirmado, retransmitido se necessário, e entregue na ordem correta.

### Por que o TRE escolheu também um link dedicado orientado a conexão?

Um link compartilhado (como a internet pública) estaria sujeito a:

- **Congestionamento imprevisível** no dia da eleição (pico de uso nacional);
- **Jitter** (variação de latência) que poderia acionar timeouts do TCP desnecessariamente;
- **Indisponibilidade** por problemas em roteadores intermediários que estão fora do controle do TRE.

Um link dedicado ponto-a-ponto elimina todas essas variáveis. A combinação **link dedicado + TCP** fornece duas camadas independentes de confiabilidade: o link garante que o canal físico estará disponível; o TCP garante que os dados que trafegam nesse canal chegarão íntegros e completos.
