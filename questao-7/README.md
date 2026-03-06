# Questão 7 — A Fronteira Nebulosa: LAN, MAN e WAN

## Como executar

Abra o arquivo `simulador.html` no navegador. Clique nos botões **"Ping LAN"**, **"Ping MAN"** e **"Ping WAN"** para simular medições de latência em cada tipo de rede. Observe os tempos de resposta (RTT), as estatísticas calculadas e as recomendações de timeout geradas automaticamente.

---

## Análise

### Definições teóricas: LAN, MAN e WAN

**LAN (Local Area Network)** é uma rede de área local que conecta dispositivos num espaço geograficamente restrito — uma sala, um andar, um edifício ou um campus universitário. Utiliza tecnologias como Ethernet (IEEE 802.3) e Wi-Fi (IEEE 802.11), com velocidades que variam de 100 Mbps a 100 Gbps nos switches modernos. A latência é mínima, tipicamente **abaixo de 5 ms**, pois os sinais percorrem poucos metros e passam por apenas um ou dois equipamentos de rede.

**MAN (Metropolitan Area Network)** é uma rede de área metropolitana que interliga LANs dentro de uma mesma cidade ou região urbana — como conectar dois campi de uma universidade, ou os prédios de uma empresa espalhados pela cidade. Utiliza fibra óptica metropolitana (Metro-Ethernet) ou tecnologias de rádio licenciadas. A latência fica na faixa de **5 a 50 ms**, dependendo da distância física e do número de equipamentos de roteamento intermediários.

**WAN (Wide Area Network)** é uma rede de área ampla que conecta localidades geograficamente distantes — cidades, estados, países ou continentes. A internet pública é o exemplo mais amplo de WAN. Utiliza backbones de fibra óptica terrestres e cabos submarinos, com roteamento em múltiplos saltos (hops). A latência varia de **50 ms** (dentro do mesmo país) a **300 ms ou mais** (conexões intercontinentais), limitada fundamentalmente pela velocidade da luz no vidro (~200.000 km/s).

### O que os milissegundos na tela revelam

| Rede | Latência típica | Distância equivalente | Causa dominante |
|---|---|---|---|
| LAN | ~1 ms | Metros a centenas de metros | Tempo de processamento nos switches |
| MAN | ~15 ms | Poucos quilômetros | Propagação na fibra + roteamento |
| WAN | ~200 ms | Milhares de quilômetros | Propagação intercontinental + múltiplos hops |

A simulação deixa claro que **latência é determinada principalmente pela distância física**, não pela velocidade do link. Um link de 1 Gbps entre São Paulo e Nova York ainda terá ~130 ms de latência — é fisicamente impossível ser mais rápido do que a velocidade da luz.

### Como a distância impacta a configuração de timeout em aplicações

O timeout é o tempo máximo que uma aplicação aguarda por uma resposta antes de declarar erro. Se configurado muito curto, gera **falsos positivos** — a aplicação declara falha mesmo quando a resposta ainda está a caminho. Se muito longo, atrasa a detecção de falhas reais.

Uma regra prática segura é `timeout = RTT_max × 3 a 4`. Os valores simulados mostram:

- **LAN:** RTT ≈ 1 ms → timeout mínimo viável ≈ 4–5 ms
- **MAN:** RTT ≈ 15 ms → timeout mínimo viável ≈ 50–60 ms
- **WAN:** RTT ≈ 200 ms → timeout mínimo viável ≈ 600–800 ms

**Uma aplicação configurada com timeout de 5 ms funcionará perfeitamente na LAN de desenvolvimento, mas vai reportar erro em 100% das chamadas em produção via WAN.** Esse é um dos bugs mais comuns em sistemas distribuídos que são desenvolvidos e testados apenas em ambiente local.

### Por que testar somente na LAN pode levar a erros futuros na implantação global

Durante o desenvolvimento, toda a comunicação ocorre na mesma máquina ou na mesma rede local. O desenvolvedor configura timeouts de 10 ms, thresholds de performance de 5 ms, e o sistema "passa em todos os testes". Ao implantar globalmente:

1. Os timeouts disparam para usuários em outros países, causando erros intermitentes aparentemente aleatórios.
2. Bibliotecas de retry com backoff exponencial sobrecarregam os servidores com tentativas, piorando o problema.
3. Bancos de dados com replicação geográfica exibem latências inesperadas em escritas.
4. Filas de mensagens com TTL baixo descartam mensagens válidas que ainda estão em trânsito.

A recomendação da engenharia de software é sempre **testar com os piores parâmetros de rede esperados em produção**, usando ferramentas como `tc` (Linux traffic control) para simular alta latência e perda de pacotes em ambiente de teste.

### Aumentar a velocidade do link resolve o problema de latência?

**Não.** Velocidade do link (banda/throughput) e latência são grandezas **independentes**:

- **Banda** determina quantos bits por segundo podem ser transferidos — relevante para transferência de arquivos grandes.
- **Latência** determina quanto tempo o primeiro bit leva para chegar ao destino — determinada principalmente pela distância e pela velocidade de propagação do sinal.

Dobrar a velocidade do link de 100 Mbps para 200 Mbps reduz o tempo para transferir um arquivo de 1 GB de 80 s para 40 s. Mas a latência de 200 ms para um servidor nos EUA permanece exatamente igual — a distância geográfica não muda.

Para reduzir latência de fato, as soluções são:
- **CDNs (Content Delivery Networks):** replicam conteúdo em servidores próximos do usuário final.
- **Edge Computing:** processam dados próximos da origem, antes de enviar ao servidor central.
- **Escolha estratégica de região de cloud:** hospedar em regiões geograficamente próximas do público-alvo.

Em resumo: banda resolve gargalos de throughput; localização resolve gargalos de latência.
