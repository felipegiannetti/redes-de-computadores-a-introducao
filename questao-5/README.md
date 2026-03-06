# Questão 5 — Tratamento de Sinais e Ruído em Código

## Como executar

Abra o arquivo `simulador.html` no navegador. Use os botões **"Próximo →"** e **"← Anterior"** para navegar pelas 4 etapas. Cada slide exibe uma animação em canvas que ilustra a transformação do sinal naquela etapa.

---

## Análise: Tipos de tratamento realizados no sinal antes da transmissão

### Etapa 1 — Placa de Rede (NIC): Sinal Digital + Correção de Erros

Os dados saem da CPU em formato binário puro e chegam à placa de rede (NIC). Antes de qualquer saída pelo meio físico, a NIC aplica o primeiro tratamento: o **encapsulamento com FCS (Frame Check Sequence)**. O FCS é calculado via **CRC (Cyclic Redundancy Check)** sobre todo o payload do frame. O receptor, ao receber o frame, recalcula o CRC: se o valor divergir, o frame é descartado e a camada superior solicita retransmissão. Esse mecanismo de software garante a **integridade dos dados** contra corrupções físicas, sendo a primeira linha de defesa contra erros.

### Etapa 2 — Codificação de Linha: Preparação para o Modem

Os bits não podem ser transmitidos "crus" pelo cabo. Uma longa sequência de 0s seria indistinguível do silêncio elétrico, fazendo o receptor perder a sincronização de clock. A **codificação de linha** resolve esse problema garantindo transições de sinal suficientemente frequentes.

Exemplos de esquemas utilizados:

- **Manchester:** Cada bit contém uma transição no meio do período — bit `1` = transição BAIXO→ALTO, bit `0` = ALTO→BAIXO. Garante pelo menos uma transição por bit.
- **NRZ-I (Non-Return-to-Zero Inverted):** Uma inversão no início do período indica bit `1`; ausência de inversão indica `0`.
- **4B5B:** Cada grupo de 4 bits é mapeado para um código de 5 bits que nunca tem mais de três 0s consecutivos, garantindo densidade de transições mínima.

O resultado é que o receptor consegue recuperar o clock do transmissor a partir do próprio sinal, sem necessidade de um canal de sincronização separado.

### Etapa 3 — Modulação Digital/Analógico no Modem

O **Modem** realiza o terceiro tratamento: a conversão do sinal digital em analógico para compatibilidade com o meio físico (cabo coaxial, linha telefônica). Essa conversão é chamada de **modulação**. Os principais esquemas são:

- **ASK (Amplitude Shift Keying):** A amplitude da portadora varia conforme o bit — alta amplitude para `1`, baixa para `0`. Simples, porém sensível a variações de amplitude no canal.
- **FSK (Frequency Shift Keying):** A frequência varia — frequência alta para `1`, baixa para `0`. Mais robusto a variações de amplitude.
- **QAM (Quadrature Amplitude Modulation):** Combina variações de amplitude **e** fase, permitindo codificar múltiplos bits por símbolo (ex: QAM-64 = 6 bits/símbolo). Utilizado em modems a cabo modernos (DOCSIS).

Na recepção, o processo inverso (**demodulação**) reconstrói os bits originais a partir da onda analógica.

### Etapa 4 — Multiplexação na Operadora

Na infraestrutura da operadora, o quarto tratamento é a **multiplexação**: o processo de combinar sinais de múltiplos clientes em um único meio físico de alta capacidade.

Os três paradigmas principais:

| Técnica | Mecanismo | Aplicação típica |
|---|---|---|
| **FDM** (Frequency Division Multiplexing) | Cada canal usa uma faixa de frequência diferente | TV a cabo, ADSL |
| **TDM** (Time Division Multiplexing) | Cada canal usa fatias de tempo em rodízio | Redes SDH/SONET, T1/E1 |
| **WDM / DWDM** (Wavelength Division Multiplexing) | Cada canal usa um comprimento de onda (cor) diferente da luz | Backbones de fibra óptica |

O DWDM moderno permite mais de 160 canais simultâneos num único par de fibras, cada um com capacidades de 100 Gbps ou mais — tornando possível o tráfego global da internet num conjunto relativamente pequeno de cabos submarinos.

### Resumo do fluxo completo

```
CPU → [bits brutos]
   → NIC: encapsula + calcula FCS/CRC
   → Codificação de Linha: Manchester/NRZ-I/4B5B
   → Modem: modulação ASK/FSK/QAM → onda analógica
   → Operadora: multiplexação FDM/TDM/WDM → backbone
```

Cada etapa resolve um problema específico de compatibilidade ou confiabilidade, e todas são necessárias para que dados criados num processador cheguem intactos a milhares de quilômetros de distância.
