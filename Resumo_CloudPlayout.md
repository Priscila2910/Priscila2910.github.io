# Central Casting Belo Horizonte

## Overview

Central Casting é um conjunto de elementos responsáveis pelo gerenciamento e programação do conteúdo a ser reproduzido na TV. O sistema funciona de forma automatizada e centralizada, ou seja, vários canais são gerenciados por software em um mesmo local. Com intuito de melhoria na operação, a solução de automação e programação do Central Casting de Belo Horizonte, plataforma Pebble, foi migrada para a TV Globo no Rio de Janeiro. 

### Topologia

Topologia Central Casting Belo Horizonte:






Dentre esses componentes, a Automação, o Playout e os Gateways SRT virtuais são a parte da infraestrutura que está em fase de migração para a nuvem, os demais componentes seguirão na infraestrutura on-premise. As aplicações da nuvem estão no ambiente da Google Cloud Platform (GCP), nas regiões do Estados Unidos (us-east1), Europa (europe-west1 e europe-southwest1) e América do Sul (southamerica-east1). A divisão dos canais por região está prevista da seguinte forma: 
•Estados Unidos: 2 Canais Tier 4 e 16 Canais Tier 3 
•América do Sul: 18 canais Canais Tier 2 

Além da estrutura na nuvem, que é o sistema titular do Cloud Playout, também existe uma estrutura física com as mesmas aplicações que temos na nuvem, porém elas estão instaladas em servidores on-premise. 

Sobre os principais componentes do Cloud Playout: 

### Automação

A aplicação que usamos para a automação, é o ASTRA do fornecedor AVECO. Ela é responsável por fazer o controle e gerenciamento das mídias, e está instalada em quatro máquinas virtuais (VM) na Google Cloud Engine (GCE), sendo: 
•1 VM Back office: controla até 7 playlists, além de conter o banco de dados 
•2 VMs Orbiters: cada Orbiter controla até 7 playlists
•1 VM MULE: gerencia ativos de mídia verificando sua disponibilidade nos storages on-premise (G800 e DIVA), além de enviar comandos para upload de mídia no Playout (VoS). 

Em uma operação padrão, o usuário acessa o client da aplicação ASTRA instalada em uma máquina no ambiente on-premise, para fazer o upload da playlist com a programação dos próximos dias. Essa playlist tem metadados necessários para que a automação reconheça o momento em que essa mídia será tocada (início, fim, duração, ID, ...). Se a automação identificar que as mídias já estão no Playout, o ASTRA indica para operador que essas mídias estão prontas para serem tocadas. Caso esteja faltando alguma mídia, a automação faz uma requisição ao Playout (VoS) para que ele localize as mídias faltantes nos servidores on-premise, e faça a transferência dessas mídias para o Storage na nuvem (Google Cloud Storage – GCS) 

![img](ASTRA AVECO.png "Automação ASTRA (AVECO)")

### Playout

A aplicação que usamos para o Playout, é o VoS do fornecedor Harmonic. Ela está instalada no Google Cloud Kubernetes (GKE), podendo ser acessada via web browser. Além de receber as requisições de transferência de mídias da automação (ASTRA), o VoS também é responsável por criar canais, fazer o encoding de áudio e vídeo, fazer o processamento de grafismo, gerar saídas SRT para os gateways on-premise (ION) e virtuais (GCP – Europa). Outra função importante do VOS é a recepção dos sinais de Live feeds via protocolo SRT. 

![img](VOS.png "Playout VoS (HARMONIC)")

### Mídias e Live Feeds

Existe a possibilidade do sistema Cloud Playout receber mídias já gravadas (novelas, comerciais, chamadas, ...) e sinais de eventos ao vivo, chamados de Live Feeds (jogo de futebol, shows, ...). As mídias gravadas estão armazenadas em storages on-premise que são o G800 (acessados via NAS-1 e NAS-2) e o Diva. Já os Live Feeds são recebidos via SDI pelo encoder CP9000 (Harmonic) e enviados via protocolo TS para a rede. Tantos os storages, quanto os encoders estão no ambiente Globo on-premise. 

### Gateways SRT virtuais e físicos

O Haivision Media Gateway (HMG) é usado para receber os fluxos SRT gerados pelo VoS, e estão em uso no Cloud Playout tanto em aplicações instaladas em VMs no GCE, quanto no ambiente on-premise no ION. No ION, são recebidos os fluxos SRT dos Canais Tier 3 e enviados como TS para a cadeia de transmissão. Já os gateways HMG virtuais estão em VMs na nuvem da Europa e são enviados para uma operadora em Portugal.

### Monitoração de áudio e vídeo 

Para monitoração de áudio e vídeo, são usadas duas aplicações: o TAG Multiviewer e o Kaleido-IP. O TAG Multiviewer é uma aplicação que está instalada na GCE, e recebe fluxo SRT diretamente do VOS. Esse multiviewer é acessado via web browser e permite a criação de mosaicos personalizados que podem ser criados conforme a necessidade da operação.

![img](TAG Multiviewer.png "TAG Multiviewer")

Já o Kaleido-IP está no ambiente on-premise, cujo multiviewer está exibido em monitores da exibição, recebendo um sinal TS gerado pelo Gateway SRT on-premise. 

![img](Kaleido-IP.png "Multiviewer Kaleido-IP")




