# Questão 1 — Acme S/A: Virtualização e Gargalos de Rede

Abra o arquivo `simulador.html` no navegador e clique em **"Processar Dados"** para ver a simulação.

---

## Análise

As redes de computadores evoluíram junto com o processamento de dados ao longo das décadas. Na era dos mainframes, todo o processamento era centralizado e os terminais apenas exibiam resultados, a rede mal existia. Com o modelo cliente-servidor nos anos 80 e 90, o processamento foi distribuído e as LANs passaram a ter papel importante, mas o gargalo ainda era a CPU dos servidores lentos da época. Com a internet e as aplicações web, o volume de dados cresceu tanto que o gargalo migrou para os links de acesso. Nos anos 2000, a virtualização surgiu como resposta direta ao problema de CPU: em vez de ter vários servidores físicos subutilizados, passou a ser possível rodar múltiplas máquinas virtuais em um único hardware, aproveitando muito melhor os recursos de processamento.

A virtualização resolve o problema de CPU porque o hypervisor — software responsável por gerenciar as VMs, distribui os núcleos físicos dinamicamente entre elas, cada uma recebendo quantos vCPUs forem necessários naquele momento. Na simulação isso fica evidente: enquanto o servidor físico antigo demora vários segundos para concluir o processamento, o servidor virtualizado termina o mesmo trabalho muito mais rápido, pois tem acesso a mais núcleos e memória de forma eficiente. Servidores que antes ficavam ociosos a maior parte do tempo passam a ser utilizados de verdade, o que representa uma grande evolução em termos de capacidade de processamento.

O problema é que a virtualização não resolve tudo, ela apenas troca o gargalo de lugar. Como mostrado na simulação, depois que o processamento termina rapidamente, o volume de dados gerado precisa sair pela mesma interface de rede física compartilhada por todas as VMs. O link começa a encher, fica saturado e passa a enfileirar pacotes, atrasando a entrega final mesmo com a CPU já ociosa. Além disso, VMs que se comunicam entre si dentro do mesmo servidor geram tráfego interno que compete pelo mesmo canal, e o próprio hypervisor adiciona mais uma camada no caminho de cada pacote de rede, aumentando a latência. Esses são os novos desafios: saturação do link físico compartilhado, tráfego interno entre VMs e overhead de rede imposto pela camada de virtualização.
