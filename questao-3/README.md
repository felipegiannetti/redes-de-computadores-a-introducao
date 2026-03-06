# Questão 3 — O Mito da "Banda Larga" Comercial

## Como executar

Abra o arquivo `simulador.html` no navegador. Clique em **"Baixar Atualização (Download)"** e observe a velocidade altíssima. Em seguida clique em **"Enviar Backup (Upload)"** e observe o contraste brutal — o upload é extremamente lento, simulando a assimetria real dos planos residenciais e comerciais brasileiros.

---

## Análise

### Por que o termo "banda larga" é tecnicamente impreciso

O termo popular **"banda larga de 600 Mega"** é comercialmente impreciso por duas razões:

**1. Especifica apenas o download:** As operadoras brasileiras anunciam o plano pelo valor de descida (download). O upload — velocidade de subida, fundamental para qualquer operação que envia dados para a nuvem — costuma ser drasticamente menor e raramente aparece com destaque nos anúncios.

**2. Confunde velocidade com capacidade bidirecional:** "600 Mega" sugere que o link como um todo tem essa capacidade, quando na realidade é apenas em uma direção.

Valores típicos reais no Brasil (tecnologia GPON/fibra residencial):

| Plano anunciado | Download real | Upload real | Razão |
|---|---|---|---|
| 600 Mbps | ~560 Mbps | ~300 Mbps | 1,9:1 |
| 200 Mbps | ~185 Mbps | ~100 Mbps | 1,85:1 |
| Plano empresarial 50 Mbps simétrico | ~50 Mbps | ~50 Mbps | 1:1 |

Em tecnologias mais antigas como **ADSL**, a assimetria era ainda mais extrema (ex: 10 Mbps download / 1 Mbps upload — razão 10:1), pois o meio físico (par de cobre telefônico) foi deliberadamente projetado para favorecer o download, padrão de uso doméstico da época.

O nome tecnicamente correto seria **"serviço de acesso assimétrico de banda larga"** — não simplesmente "banda larga de 600 Mega."

### O que a simulação demonstra

- **Download:** simula o cenário de receber uma atualização de software — consome o canal de descida com altíssima velocidade. Termina em segundos.
- **Upload:** simula o envio de um backup de 50 GB para a nuvem — usa o canal de subida, muito mais estreito. No plano de "600 Mega" típico com upload de 30 Mbps, esse backup levaria aproximadamente **3 horas e 47 minutos** na vida real.

Durante esse período de upload, o **canal de subida fica 100% ocupado**. Isso explica o problema do cliente: o software de backup monopolizou o único canal de upload disponível. Nenhum outro usuário ou sistema da empresa conseguia enviar dados — e-mails, chamadas VoIP, sincronizações de sistemas em nuvem, commits git — tudo ficou bloqueado.

### O link é simétrico? É simplex, half-duplex ou full-duplex?

**O link não é simétrico** — as velocidades de download e upload são diferentes.

Quanto ao modo de operação:

| Modo | Descrição | Exemplo |
|---|---|---|
| **Simplex** | Comunicação em apenas uma direção, nunca na outra | Transmissão de TV analógica |
| **Half-duplex** | Comunicação nos dois sentidos, mas nunca simultaneamente | Walkie-talkie, Wi-Fi em canal único |
| **Full-duplex** | Comunicação simultânea nos dois sentidos | Telefone, links de fibra óptica modernos |

O link do cliente é **full-duplex assimétrico**: ele consegue baixar e subir dados **ao mesmo tempo** (full-duplex), mas a capacidade dos dois canais é diferente (assimétrico — download >> upload). Isso significa que o download e o upload não se interferem diretamente, mas o upload lento ainda monopoliza o canal de subida para todos os outros processos que precisam enviar dados.

### Como resolver o problema do cliente

O problema não é o plano de internet — é a ausência de controle de fluxo na aplicação. As soluções corretas são:

- **QoS (Quality of Service) na aplicação:** configurar a taxa máxima de upload do backup para, por exemplo, 50% do canal de subida, garantindo que os outros 50% estejam disponíveis para uso normal da empresa.
- **Agendamento fora do horário comercial:** executar o backup durante a madrugada, quando o link de upload está ocioso.
- **Compressão e deduplicação:** reduzir o volume de dados enviados, diminuindo o tempo de ocupação do canal.
- **Upgrade para link simétrico dedicado:** planos empresariais com links simétricos (mesmo upload e download) existem, mas custam mais e são contratados separadamente dos planos residenciais.

A raiz do problema é arquitetural: o software foi projetado sem considerar a característica assimétrica do link do cliente — um erro clássico de engenharia que só aparece em produção.
