# Questão 1 — Acme S/A: Virtualização e Gargalos de Rede

Abra o arquivo `simulador.html` no navegador e clique em **"Processar Dados"** para ver a simulação.

---

## Análise

Historicamente, o gargalo das redes sempre acompanhou a evolução do processamento. Nos primeiros mainframes o problema era CPU, depois veio a era cliente-servidor e o gargalo migrou para a largura de banda das LANs. Com a popularização da internet e do modelo web, links de acesso passaram a ser o ponto fraco. Quando a virtualização chegou nos anos 2000, a CPU deixou de ser problema porque várias VMs passaram a dividir o mesmo hardware físico com muito mais eficiência, um servidor que ficava ocioso 85% do tempo passou a ser aproveitado de verdade.

A virtualização resolve o processamento porque o hypervisor distribui os núcleos físicos entre várias máquinas virtuais, cada uma recebendo quantos vCPUs precisar. Na simulação dá pra ver claramente: o servidor virtualizado termina o cálculo mais rápido que o físico antigo. O problema é que esse processamento rápido gera um volume alto de dados quase que instantaneamente, e todos eles precisam sair pela mesma interface de rede física que todas as VMs compartilham.

E aí está o desafio que foi ignorado: a rede não escala automaticamente junto com a CPU virtualizada. Na simulação o link de dados enche, fica vermelho e começa a enfileirar pacotes, atrasando a entrega final mesmo com o processamento já concluído. Além disso, VMs se comunicando entre si geram tráfego interno que compete pelo mesmo canal, e o próprio hypervisor adiciona uma camada a mais no caminho de cada pacote, aumentando a latência. A afirmação de que "virtualização elimina qualquer gargalo de rede" está errada, ela só muda onde o gargalo aparece.
