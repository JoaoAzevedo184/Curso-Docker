# Fundamentos de Docker

## O que é Docker?

Docker é uma plataforma de código aberto que automatiza o processo de implantação de aplicações dentro de ambientes isolados chamados containers. Desenvolvido pela Docker, Inc., ele permite que os desenvolvedores empacotem aplicações com todas as suas dependências em uma unidade padronizada para desenvolvimento de software.

O Docker utiliza tecnologias de virtualização em nível de sistema operacional para fornecer estes containers leves e portáteis. Diferentemente das máquinas virtuais tradicionais, os containers Docker compartilham o kernel do sistema operacional host, o que os torna mais eficientes em termos de recursos.

## Por que usar Docker?

O Docker oferece inúmeras vantagens para o desenvolvimento e implantação de aplicações:

1. **Consistência de ambiente**: Elimina o problema "funciona na minha máquina" ao garantir que a aplicação rode exatamente da mesma forma em qualquer ambiente.

2. **Isolamento**: Cada container opera de forma isolada, sem interferir em outros containers ou no sistema host.

3. **Portabilidade**: Containers podem ser executados em qualquer sistema que possua o Docker instalado, independentemente do sistema operacional subjacente.

4. **Eficiência de recursos**: Containers são mais leves que máquinas virtuais tradicionais, consumindo menos memória e CPU.

5. **Escalabilidade**: Facilita a criação e remoção dinâmica de múltiplas instâncias da mesma aplicação.

6. **Velocidade de implantação**: Containers iniciam em segundos, agilizando processos de desenvolvimento, teste e produção.

7. **Controle de versão**: Permite versionar facilmente ambientes completos de aplicações.

8. **Microserviços**: Facilita a adoção de arquiteturas baseadas em microserviços.

## Por que usar Container?

Os containers oferecem vantagens específicas que os tornam uma escolha excelente para desenvolvimento moderno:

### Vantagens dos Containers

1. **Leveza**: Containers compartilham o kernel do sistema operacional host, tornando-os muito mais leves que máquinas virtuais.

2. **Rapidez**: Inicializam em segundos, contra minutos de uma VM tradicional.

3. **Densidade**: É possível executar muito mais containers do que VMs no mesmo hardware.

4. **Imutabilidade**: Containers são projetados para serem imutáveis, facilitando o gerenciamento de configuração.

5. **Padronização**: O formato padronizado dos containers simplifica o processo de implantação.

6. **CI/CD**: Integram-se naturalmente em pipelines de integração e entrega contínuas.

7. **DevOps**: Reduzem a diferença entre ambientes de desenvolvimento e produção.

8. **Infraestrutura como código**: Containers são definidos em arquivos de configuração que podem ser versionados.

## Documentação Docker

A documentação oficial do Docker é um recurso essencial para qualquer pessoa que trabalhe com esta tecnologia:

### Recursos Importantes

- **Documentação Oficial**: [https://docs.docker.com](https://docs.docker.com) - Referência completa para todas as funcionalidades do Docker.

- **Docker Hub**: [https://hub.docker.com](https://hub.docker.com) - Repositório oficial de imagens Docker.

- **Docker Compose**: [https://docs.docker.com/compose/](https://docs.docker.com/compose/) - Documentação para orquestração de múltiplos containers.

- **Docker CLI**: [https://docs.docker.com/engine/reference/commandline/cli/](https://docs.docker.com/engine/reference/commandline/cli/) - Referência para comandos da interface de linha de comando.

- **Dockerfile Reference**: [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/) - Guia para criação de imagens Docker personalizadas.

- **Docker API**: [https://docs.docker.com/engine/api/](https://docs.docker.com/engine/api/) - Documentação da API para integração programática.

## Principais componentes do Docker

O ecossistema Docker é composto por diversos componentes que trabalham em conjunto:

### Docker Engine

- **Daemon (dockerd)**: Processo em segundo plano que gerencia objetos Docker como imagens, containers, redes e volumes.
- **REST API**: Interface para programas interagirem com o daemon.
- **CLI (docker)**: Interface de linha de comando para controlar o Docker.

### Imagens Docker

- Modelos somente leitura com instruções para criar containers.
- Compostas por camadas empilhadas usando um sistema de arquivos de union.
- Armazenadas em registries como Docker Hub ou privados.
- Definidas através de um Dockerfile.

### Containers

- Instâncias executáveis de imagens Docker.
- Isolados, mas compartilham o kernel do sistema operacional host.
- Possuem seu próprio sistema de arquivos, rede e processos.
- Estado efêmero por definição (mudanças são perdidas quando removidos).

### Dockerfile

- Arquivo de texto com instruções para construir uma imagem Docker.
- Define o ambiente, dependências, configurações e comandos da aplicação.
- Permite automação e reprodutibilidade no processo de construção de imagens.

### Docker Compose

- Ferramenta para definir e gerenciar aplicações multi-container.
- Usa arquivos YAML para configurar os serviços da aplicação.
- Simplifica comandos para criar e iniciar todos os containers.

### Docker Swarm

- Ferramenta de orquestração nativa do Docker para clusters de containers.
- Permite gerenciamento de múltiplos hosts Docker como um único recurso virtual.
- Oferece funcionalidades de balanceamento de carga e escalabilidade.

### Docker Registry

- Serviço para armazenar e distribuir imagens Docker.
- Docker Hub é o registry público oficial.
- Empresas frequentemente mantêm registries privados para imagens proprietárias.

### Docker Volume

- Mecanismo para persistência de dados gerados e utilizados por containers.
- Independente do ciclo de vida do container.
- Pode ser compartilhado entre múltiplos containers.

### Docker Network

- Sistema de rede para comunicação entre containers.
- Oferece diferentes drivers para diversos casos de uso.
- Permite isolar containers em redes virtuais específicas.
