# Redes de Computadores — Lista A: Introdução

**PUC Minas — Engenharia de Software**

Primeiro exercício de Redes de Computadores. Atividade em dupla com foco em compreender os conceitos fundamentais de infraestrutura de redes e seu impacto no desenvolvimento e arquitetura de software.

Cada questão possui um simulador interativo em HTML + JavaScript puro (sem dependências externas) e uma análise escrita dos conceitos demonstrados.

---

## Como executar

Abra qualquer arquivo `simulador.html` diretamente no Chrome ou Edge — nenhuma instalação necessária.

---

## Questões

### [Questão 1 — Acme S/A: Virtualização e Gargalos de Rede](questao-1/)

**Cenário:** A empresa Acme S/A alega que "a virtualização elimina qualquer gargalo de rede" para o Banco do Brasil.

**Simulador:** Dashboard comparando Servidor Físico vs. Servidor Virtualizado. O servidor virtual processa muito mais rápido, mas a barra de "link de dados" enche e atrasa a entrega final.

**Conceitos:** Evolução histórica das redes, virtualização com hypervisor, saturação de link compartilhado, overhead de rede em VMs.

---

### [Questão 2 — PIT Telecom / Oi: Banda Passante vs. Latência](questao-2/)

**Cenário:** O marketing afirma que "com a nova fibra de alta banda passante, sua internet não vai mais travar."

**Simulador:** Duas barras de progresso — Rotina A envia 1 pacote gigante (stream contínuo), Rotina B envia 10.000 pacotes minúsculos com latência por pacote.

**Conceitos:** Definição técnica de banda passante, diferença entre throughput e latência, impacto do overhead por pacote no tempo total.

---

### [Questão 3 — O Mito da "Banda Larga" Comercial](questao-3/)

**Cenário:** Software de backup de 50 GB trava a rede, apesar do cliente ter "600 Mega de Banda Larga."

**Simulador:** Terminal de comandos com Download (ultrarrápido) e Upload (extremamente lento) simulados.

**Conceitos:** Assimetria download/upload nos planos residenciais brasileiros, links simplex, half-duplex e full-duplex, definição técnica vs. comercial de "banda larga."

---

### [Questão 4 — O Tradutor de Mundos: Função do Modem](questao-4/)

**Cenário:** Operadoras como a Net (Claro) entregam um Cable Modem para uso na rede coaxial.

**Simulador:** Animação em canvas — dados digitais (onda quadrada) fluem do computador, o modem converte, e a onda analógica senoidal viaja pelo cabo coaxial. Botão para simular ruído e comparar a resiliência de cada tipo de sinal.

**Conceitos:** Função do modem (modulação/demodulação), diferença entre sinais digitais e analógicos, sensibilidade a ruído e limiar de decisão.

---

### [Questão 5 — Tratamento de Sinais e Ruído em Código](questao-5/)

**Cenário:** Dados percorrem 4 etapas de transformação desde a placa de rede até o backbone da operadora.

**Simulador:** Apresentação animada em 4 slides com visualização em canvas de cada etapa de transformação do sinal.

| Etapa | Tratamento |
|---|---|
| 1 | Placa de Rede (NIC): sinal digital + FCS/CRC para correção de erros |
| 2 | Codificação de Linha: Manchester/NRZ-I para sincronização de clock |
| 3 | Modulação D/A no Modem: ASK/FSK/QAM para compatibilidade com o meio físico |
| 4 | Multiplexação na Operadora: FDM/TDM/WDM para compartilhamento do canal |

**Conceitos:** CRC, codificação Manchester, modulação ASK/QAM, multiplexação FDM/TDM/WDM.

---

### [Questão 6 — Missão TRE-MG: Confiabilidade](questao-6/)

**Cenário:** TRE-MG contrata link dedicado de 50 Mbps, full-duplex, síncrono e orientado a conexão para transmitir votos de urnas eletrônicas.

**Simulador:** Log de servidor comparando lado a lado UDP (fire-and-forget com perdas aleatórias de ~35%) e TCP (handshake, ACKs, retransmissão automática, entrega garantida 100%).

**Conceitos:** UDP vs. TCP, 3-way handshake, ACK e retransmissão automática, diferença entre link orientado a conexão (físico/enlace) e protocolo orientado a conexão (transporte), por que um link dedicado é necessário.

---

### [Questão 7 — A Fronteira Nebulosa: LAN, MAN, WAN](questao-7/)

**Cenário:** A UFMG interliga campus Pampulha (LAN), prédio de Arquitetura no centro de BH (MAN) e servidores de nuvem no exterior (WAN). O software precisa se comunicar com todos.

**Simulador:** Terminal de ping com 3 botões. Cada ping retorna 4 medições com RTT realista (LAN ≈ 1 ms, MAN ≈ 15 ms, WAN ≈ 200 ms), estatísticas min/avg/max/mdev e recomendação automática de timeout.

**Conceitos:** Definições de LAN/MAN/WAN, latência vs. banda, impacto da distância em timeouts de aplicação, por que testar só na LAN é perigoso, por que aumentar banda não reduz latência.

---

## Estrutura do repositório

```
redes-de-computadores-a-introducao/
├── questao-1/
│   ├── simulador.html
│   └── ANALISE1.md
├── questao-2/
│   ├── simulador.html
│   └── ANALISE2.md
├── questao-3/
│   ├── simulador.html
│   └── ANALISE3.md
├── questao-4/
│   ├── simulador.html
│   └── README.md
├── questao-5/
│   ├── simulador.html
│   └── README.md
├── questao-6/
│   ├── simulador.html
│   └── README.md
└── questao-7/
    ├── simulador.html
    └── README.md
```

---

## Tecnologias utilizadas

- HTML5 + CSS3 + JavaScript puro (ES6+)
- Canvas API para animações e visualizações de sinais
- `async/await` + `Promise`-wrapped `setTimeout` para simulações assíncronas
- `insertAdjacentHTML` + `scrollTop = scrollHeight` para logs de terminal sem travar o browser
- Nenhuma dependência externa — todos os simuladores abrem diretamente no navegador
