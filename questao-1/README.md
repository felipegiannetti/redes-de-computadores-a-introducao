# Questão 1 — Acme S/A: Virtualização e Gargalos de Rede

## Como executar

Abra o arquivo `simulador.html` no navegador e clique em **"Processar Dados"** para iniciar a simulação. Observe a diferença de tempo de processamento entre o servidor físico e o virtualizado, e acompanhe a barra de "link de dados" enchendo após o processamento do servidor virtual terminar.

---

## Análise

### Histórico de evolução das redes e o processamento de dados

As redes de computadores evoluíram em paralelo com os modelos de processamento ao longo das décadas:

| Época | Modelo | Gargalo dominante |
|---|---|---|
| Anos 60–70 | **Mainframe** — processamento centralizado, terminais burros | CPU do mainframe |
| Anos 80–90 | **Cliente-Servidor** — processamento distribuído, LANs surgem | CPU dos servidores lentos |
| Anos 90–2000 | **Internet / Web** — volume de dados explode | Links de acesso (banda) |
| Anos 2000+ | **Virtualização** — múltiplas VMs em hardware físico | Interface de rede compartilhada |

Cada geração resolveu o gargalo da anterior e criou um novo. A virtualização surgiu como resposta direta ao problema de CPU dos anos 2000: em vez de dezenas de servidores físicos subutilizados — cada um ocioso 80% do tempo — passou a ser possível consolidar tudo em poucos servidores potentes rodando múltiplas Máquinas Virtuais (VMs).

### Por que a virtualização resolve o problema de CPU

O **hypervisor** (software de virtualização, ex: VMware ESXi, KVM, Hyper-V) gerencia as VMs e distribui os recursos do hardware físico dinamicamente:

- Cada VM recebe **vCPUs** (núcleos virtuais) conforme a demanda do momento, em vez de núcleos físicos dedicados ociosos.
- A memória RAM é alocada sob demanda com técnicas como **ballooning** e **overcommit**.
- Servidores que antes operavam a 10–15% de utilização chegam a 70–80%, representando enorme ganho de eficiência operacional e redução de custos.

Na simulação isso fica evidente: o servidor físico antigo leva vários segundos para processar os dados (CPU lenta, recurso dedicado único), enquanto o servidor virtualizado termina o mesmo trabalho muito mais rápido, pois tem acesso a mais núcleos e a alocação é eficiente.

### O que a simulação demonstra

Após o processamento terminar, o volume de dados gerado precisa sair pela **interface de rede física** — e é aqui que o novo gargalo aparece. A barra de "link de dados" começa a encher progressivamente, enfileirando pacotes e atrasando a entrega final, mesmo com a CPU já ociosa.

Esse comportamento ilustra que **o gargalo foi apenas movido de lugar**: saiu da CPU e foi para a rede.

### Os novos desafios criados pela virtualização

A afirmação da Acme S/A de que "a virtualização elimina qualquer gargalo de rede" é **tecnicamente incorreta**. Os principais desafios de rede introduzidos ou agravados pela virtualização são:

- **Saturação do link físico compartilhado:** todas as VMs no mesmo host físico disputam a mesma interface de rede (NIC). Um processamento intenso em uma VM pode privar as outras de largura de banda.
- **Tráfego East-West interno:** VMs que se comunicam entre si no mesmo host geram tráfego que, dependendo da configuração, ainda passa pelo switch virtual e pela NIC física, consumindo banda sem sair do servidor.
- **Overhead de rede do hypervisor:** cada pacote passa por uma camada extra de software (o virtual switch do hypervisor) antes de chegar à NIC física, adicionando latência que não existia em servidores bare-metal.
- **Congestionamento em ambientes multi-tenant:** em nuvens públicas, vizinhos ruidosos (*noisy neighbors*) podem saturar o backbone de rede compartilhado, degradando o desempenho de todas as VMs no mesmo host físico.

A solução correta passa por dimensionar corretamente as interfaces de rede (NICs de 10/25/100 Gbps), usar técnicas como **SR-IOV** (Single Root I/O Virtualization) para bypass do hypervisor, e aplicar políticas de QoS por VM — algo que a Acme S/A claramente não considerou na proposta.
