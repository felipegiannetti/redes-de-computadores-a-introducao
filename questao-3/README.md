# Questão 3 — O Mito da "Banda Larga" Comercial

Abra o arquivo `simulador.html` no navegador. Clique em **"Baixar Atualização"** e depois em **"Enviar Backup"** para comparar as velocidades.

---

## Análise

O termo "banda larga" é comercialmente impreciso porque as operadoras brasileiras vendem o plano pelo valor de download, o famoso "600 Mega" significa 600 Mbps de velocidade de descida. O upload, que é a velocidade de subida dos dados, costuma ser muito menor e muitas vezes nem aparece no anúncio. Na tecnologia ADSL e em boa parte dos planos de fibra residencial e comercial, a relação é assimétrica por design: o download pode ser 560 Mbps enquanto o upload fica em 30 Mbps ou menos. Ou seja, o link não é simétrico, as velocidades de entrada e saída são completamente diferentes. Tecnicamente o termo correto seria "acesso assimétrico de banda larga", não simplesmente "banda larga de 600 Mega".

Na simulação isso fica evidente: o download de uma atualização acontece em segundos porque o canal de descida tem capacidade enorme. Já o upload do backup de 50 GB a 30 Mbps levaria aproximadamente 3 horas e 47 minutos na vida real, e durante todo esse tempo o canal de subida estaria completamente ocupado. Isso explica o problema do cliente: o sistema de backup monopolizou o único canal de upload disponível e travou a rede para todos os outros usuários da empresa, de forma que ninguém conseguia enviar e-mails, fazer chamadas ou usar sistemas em nuvem.

Quanto ao tipo de comunicação: o link é full duplex, pois é capaz de transmitir e receber dados ao mesmo tempo (ao contrário do simplex, que só vai em uma direção, ou do half duplex, que alterna entre enviar e receber). Porém, mesmo sendo full duplex, ele é assimétrico, ou seja, os dois canais têm larguras de banda diferentes. A solução para o problema do cliente seria implementar controle de qualidade de serviço (QoS) na aplicação de backup para limitar a taxa de upload e não monopolizar o link, ou agendar os backups para horários de baixo uso da rede.