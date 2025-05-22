# Introdução ao Docker: Images e Containers

## O que é Docker?

Docker é uma plataforma open-source que automatiza o processo de desenvolvimento, implantação e execução de aplicações dentro de ambientes isolados chamados contêineres. Esta tecnologia permite que você empacote uma aplicação com todas as suas dependências em uma unidade padronizada para desenvolvimento de software.

### Principais benefícios:

- **Consistência de ambiente**: "Funciona na minha máquina" deixa de ser um problema
- **Isolamento**: Aplicações e suas dependências são isoladas do sistema operacional host
- **Portabilidade**: Contêineres podem ser executados em qualquer ambiente que suporte Docker
- **Eficiência de recursos**: Utiliza menos recursos que máquinas virtuais tradicionais
- **Escalabilidade**: Facilita a criação e gerenciamento de múltiplas instâncias de aplicações

## Relação entre Images e Containers

### Docker Images

Uma imagem Docker é um pacote leve, independente e executável que inclui tudo necessário para executar uma aplicação:
- Código
- Runtime
- Bibliotecas
- Variáveis de ambiente
- Arquivos de configuração

Características importantes das imagens:
- São **imutáveis** - uma vez criadas não mudam
- Possuem estrutura em **camadas**, o que otimiza armazenamento e transferência
- São definidas por um **Dockerfile**, que contém instruções para sua construção
- Seguem o princípio de **build once, run anywhere**

### Docker Containers

Um contêiner é uma instância em execução de uma imagem Docker. Se a imagem é o "modelo", o contêiner é o "objeto" criado a partir deste modelo.

Características dos contêineres:
- São **efêmeros** - podem ser criados, iniciados, parados, movidos ou deletados facilmente
- São **isolados** uns dos outros e do sistema host, mas podem se comunicar através de canais definidos
- Mantêm seu próprio sistema de arquivos, processos e recursos de rede
- Podem ter **estado** - quando executados, quaisquer mudanças são salvas apenas naquela instância

### Analogia para entender a relação:
- **Imagem**: como uma classe ou template em programação orientada a objetos
- **Contêiner**: como uma instância dessa classe, com seu próprio estado e ciclo de vida

## Docker Hub

O Docker Hub é um repositório centralizado de imagens Docker, similar ao GitHub para código.

### Principais características:

- **Biblioteca pública** com milhares de imagens oficiais e da comunidade
- Suporte para **repositórios privados** para armazenar imagens proprietárias
- **Integração com CI/CD** para build e deploy automáticos
- Sistema de **tags** para versionamento de imagens
- **Webhooks** para integração com outros sistemas

### Usos comuns:
- Baixar imagens pré-construídas (ex: `docker pull ubuntu`)
- Compartilhar suas próprias imagens (`docker push`)
- Descobrir soluções prontas para diversas tecnologias

## Docker Desktop

Docker Desktop é uma aplicação que simplifica o uso do Docker em ambientes Windows e macOS, fornecendo uma interface gráfica amigável para gerenciar o ecossistema Docker.

### Componentes principais do Docker Desktop:

### Docker Engine

O Docker Engine é o core da plataforma Docker, responsável pela criação e execução dos contêineres.

- **Arquitetura cliente-servidor**
- **Daemon**: Processo em segundo plano que gerencia objetos Docker
- **REST API**: Interface para programas interagirem com o daemon
- **Formato de contêiner OCI** (Open Container Initiative)

### Docker CLI

A Command Line Interface (CLI) do Docker é a principal forma de interação com o Docker Engine.

#### Comandos essenciais:
- `docker run`: Cria e inicia um contêiner
- `docker build`: Constrói uma imagem a partir de um Dockerfile
- `docker pull/push`: Baixa/envia imagens do/para um registro
- `docker ps`: Lista contêineres em execução
- `docker images`: Lista imagens disponíveis localmente

### Docker Compose

Docker Compose é uma ferramenta para definir e gerenciar aplicações multi-contêiner.

- Usa arquivos **YAML** para configuração
- Permite definir toda a infraestrutura como código
- Facilita o **gerenciamento de dependências** entre serviços
- Simplifica o processo de desenvolvimento e teste local

Exemplo simples de `docker-compose.yml`:
```yaml
version: '3'
services:
  web:
    build: ./web
    ports:
      - "8000:8000"
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: example
```

### Docker Networking

Docker Networking possibilita a comunicação entre contêineres e com o mundo externo.

#### Tipos de redes:
- **Bridge**: Rede padrão isolada no host (default)
- **Host**: Usa a rede do host diretamente
- **Overlay**: Conecta múltiplos daemons Docker para comunicação entre swarm services
- **Macvlan**: Permite atribuir endereços MAC aos contêineres
- **None**: Desabilita toda a rede

#### Comandos úteis:
- `docker network create`: Cria uma rede personalizada
- `docker network connect`: Conecta um contêiner a uma rede
- `docker network ls`: Lista as redes disponíveis

### Docker Volumes

Docker Volumes são o mecanismo preferido para persistir dados gerados e utilizados por contêineres Docker.

#### Características principais:
- **Persistência** independente do ciclo de vida do contêiner
- **Compartilhamento** de dados entre contêineres
- Melhor **performance** que mounts de bind
- Facilidade para **backup e restore**
- Suporte para **drivers de volume** (local, nfs, etc.)

#### Tipos de armazenamento:
1. **Volumes**: Gerenciados completamente pelo Docker
2. **Bind mounts**: Mapeiam diretórios do host para o contêiner
3. **tmpfs mounts**: Armazenamento em memória

## Conclusão

Docker revolucionou a forma como desenvolvemos, distribuímos e executamos software. O entendimento de seus componentes fundamentais - imagens e contêineres - assim como das ferramentas associadas (Docker Hub, Engine, CLI, Compose, Networking e Volumes) é essencial para aproveitar todo o potencial desta tecnologia na criação de ambientes de desenvolvimento e produção consistentes e eficientes.
