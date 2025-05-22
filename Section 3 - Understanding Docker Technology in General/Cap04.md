# Docker CLI no PowerShell

## Introdução

O Docker Command Line Interface (CLI) é uma ferramenta poderosa para gerenciar containers, imagens, volumes e outros recursos Docker diretamente do terminal. Este guia foca na utilização do Docker CLI especificamente no PowerShell do Windows, incluindo sua integração com o Windows Subsystem for Linux (WSL).

![Docker CLI](https://docs.docker.com/engine/images/engine-components-flow.png)

### Requisitos para Docker no Windows

Para utilizar o Docker no Windows com PowerShell, você precisará:

1. **Windows 10/11** (versão 1903 ou posterior, build 18362 ou posterior)
2. **WSL 2** (Windows Subsystem for Linux)
3. **Docker Desktop para Windows** (ou Docker Engine configurado manualmente)

### Configurando o WSL

O WSL (Windows Subsystem for Linux) é um componente essencial para o funcionamento eficiente do Docker no Windows. Para verificar ou instalar o WSL:

```powershell
# Verificar versão do WSL
wsl --version

# Listar distribuições Linux instaladas
wsl --list --verbose

# Definir WSL 2 como padrão
wsl --set-default-version 2

# Instalar uma distribuição Linux (Ubuntu, por exemplo)
wsl --install -d Ubuntu
```

### Verificando a Instalação do Docker

Após instalar o Docker Desktop (ou Docker Engine), verifique se ele está funcionando corretamente:

```powershell
# Verificar versão do Docker
docker --version

# Verificar informações detalhadas do sistema Docker
docker info

# Executar um container de teste
docker run hello-world
```

## Comandos Básicos do Docker

### Listar Containers

```powershell
# Listar containers em execução
docker ps

# Listar todos os containers (incluindo parados)
docker ps -a

# Listar apenas os IDs dos containers em execução
docker ps -q

# Formato personalizado (útil para scripts)
docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}"
```

### Gerenciar o Ciclo de Vida dos Containers

```powershell
# Iniciar um container existente
docker start <nome-ou-id>

# Parar um container em execução
docker stop <nome-ou-id>

# Reiniciar um container
docker restart <nome-ou-id>

# Pausar um container (sem desligá-lo)
docker pause <nome-ou-id>

# Retomar um container pausado
docker unpause <nome-ou-id>

# Remover um container (deve estar parado)
docker rm <nome-ou-id>

# Forçar a remoção de um container em execução
docker rm -f <nome-ou-id>
```

### Gerenciar Imagens

```powershell
# Listar imagens disponíveis localmente
docker images

# Listar imagens detalhadamente
docker images -a

# Listar apenas IDs das imagens
docker images -q

# Remover uma imagem
docker rmi <nome-ou-id>

# Remover imagens não utilizadas
docker image prune

# Remover todas as imagens não utilizadas por containers
docker image prune -a
```

### Buscar Imagens no Docker Hub

```powershell
# Buscar uma imagem no Docker Hub
docker search <termo>

# Buscar imagens oficiais
docker search --filter "is-official=true" <termo>

# Buscar imagens com estrelas (popularidade)
docker search --filter "stars=100" <termo>

# Busca detalhada (formato de tabela)
docker search --format "table {{.Name}}\t{{.Description}}\t{{.Stars}}" <termo>
```

### Baixar Imagens

```powershell
# Baixar a versão mais recente de uma imagem
docker pull <nome-da-imagem>

# Baixar uma versão específica (tag)
docker pull <nome-da-imagem>:<tag>

# Baixar várias tags de uma imagem
docker pull <nome-da-imagem> -a

# Baixar de um registro diferente
docker pull <registro>/<nome-da-imagem>:<tag>
```

### Executar Containers

```powershell
# Executar um container simples
docker run <nome-da-imagem>

# Executar em modo interativo com terminal
docker run -it <nome-da-imagem> <comando>

# Executar em segundo plano (modo detached)
docker run -d <nome-da-imagem>

# Definir um nome para o container
docker run --name meu-container <nome-da-imagem>

# Mapear portas (host:container)
docker run -p 8080:80 <nome-da-imagem>

# Montar volume (host:container)
docker run -v ${PWD}:/app <nome-da-imagem>

# No PowerShell, para caminhos Windows:
docker run -v ${PWD}:/app <nome-da-imagem>

# Definir variáveis de ambiente
docker run -e "VARIAVEL=valor" <nome-da-imagem>

# Limitar recursos (memória/CPU)
docker run --memory="512m" --cpus="0.5" <nome-da-imagem>

# Remover automaticamente após parar
docker run --rm <nome-da-imagem>
```

### Ações em Containers em Execução

```powershell
# Executar um comando em um container em execução
docker exec <nome-ou-id> <comando>

# Acessar o terminal de um container em execução
docker exec -it <nome-ou-id> bash

# No PowerShell, para executar comandos com PowerShell:
docker exec -it <nome-ou-id> pwsh

# Copiar arquivos para um container
docker cp arquivo.txt <nome-ou-id>:/caminho/no/container/

# Copiar arquivos de um container
docker cp <nome-ou-id>:/caminho/no/container/arquivo.txt ./destino/

# Ver logs do container
docker logs <nome-ou-id>

# Seguir logs em tempo real
docker logs -f <nome-ou-id>

# Ver últimas N linhas
docker logs --tail 100 <nome-ou-id>
```

## Gerenciamento de Recursos

### Redes Docker

```powershell
# Listar redes
docker network ls

# Criar uma rede
docker network create minha-rede

# Conectar container a uma rede
docker network connect minha-rede <nome-ou-id>

# Desconectar container de uma rede
docker network disconnect minha-rede <nome-ou-id>

# Inspecionar uma rede
docker network inspect minha-rede
```

### Volumes Docker

```powershell
# Listar volumes
docker volume ls

# Criar um volume
docker volume create meu-volume

# Inspecionar um volume
docker volume inspect meu-volume

# Remover um volume
docker volume rm meu-volume

# Remover volumes não utilizados
docker volume prune
```

## Dicas e Truques para PowerShell

### Usando Aliases

```powershell
# Criar alias para comandos Docker comuns
function docker-clean { docker system prune -af }
Set-Alias -Name dc -Value docker-clean

# Alias para listar containers
function docker-ls { docker ps -a --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Image}}" }
Set-Alias -Name dls -Value docker-ls
```

### Perfis PowerShell para Docker

Você pode adicionar funções e aliases úteis ao seu perfil PowerShell:

```powershell
# Editar perfil PowerShell
notepad $PROFILE

# Adicionar funções úteis
function Start-DockerCleanup {
    Write-Output "Limpando recursos Docker não utilizados..."
    docker system prune -f
}

# Função para iniciar um container e entrar nele automaticamente
function Enter-DockerContainer {
    param (
        [Parameter(Mandatory=$true)]
        [string]$ContainerName
    )
    
    $status = docker inspect -f "{{.State.Running}}" $ContainerName 2>$null
    
    if ($status -ne "true") {
        Write-Output "Iniciando container $ContainerName..."
        docker start $ContainerName
    }
    
    docker exec -it $ContainerName bash
}

# Salvar e carregar o perfil
. $PROFILE
```

### Integração com WSL e Docker

Para melhor desempenho, o Docker no Windows utiliza o WSL2. Você pode alternar entre o PowerShell e o terminal WSL:

```powershell
# Abrir um terminal WSL
wsl

# Executar comandos Docker diretamente no WSL
wsl docker ps

# Acessar sistema de arquivos WSL no PowerShell
cd \\wsl$\Ubuntu\home\user
```

## Gerenciando Imagens e Containers no PowerShell

### Filtrar e Formatar Saídas

```powershell
# Filtrar containers por status
docker ps --filter "status=running"

# Filtrar por nome
docker ps --filter "name=web"

# Formatar saída como tabela personalizada
docker ps --format 'table {{.Names}}\t{{.Status}}\t{{.Ports}}'

# Formatar como JSON
docker inspect --format='{{json .}}' <nome-ou-id> | ConvertFrom-Json

# No PowerShell, para processar facilmente:
$container = docker inspect <nome-ou-id> | ConvertFrom-Json
$container.NetworkSettings.Ports
```

### Operações em Lote

```powershell
# Parar todos os containers em execução
docker stop $(docker ps -q)

# Remover todos os containers parados
docker container prune

# Remover containers, redes e imagens não utilizadas
docker system prune

# Remover absolutamente tudo
docker system prune -a --volumes
```

### Monitoramento no PowerShell

```powershell
# Monitorar uso de recursos de todos os containers
docker stats

# Monitorar apenas containers específicos
docker stats <container1> <container2>

# Monitorar como tabela sem cabeçalho
docker stats --no-stream --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

## Construindo Imagens

### Dockerfile Básico

```dockerfile
FROM nginx:alpine
COPY ./site /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Comandos para Build

```powershell
# Construir imagem do Dockerfile no diretório atual
docker build -t minha-imagem:v1 .

# Especificar um Dockerfile diferente
docker build -f Dockerfile.dev -t minha-imagem:dev .

# Passar argumentos para o build
docker build --build-arg ENV=prod -t minha-imagem:prod .

# Sem usar cache
docker build --no-cache -t minha-imagem .

# Tags múltiplas
docker build -t minha-imagem:v1 -t minha-imagem:latest .
```

## Docker Compose no PowerShell

O Docker Compose permite definir e executar aplicações multi-container. Funciona perfeitamente no PowerShell.

### Comandos Básicos do Docker Compose

```powershell
# Iniciar serviços definidos no arquivo docker-compose.yml
docker-compose up

# Iniciar em segundo plano
docker-compose up -d

# Parar serviços
docker-compose down

# Parar e remover volumes
docker-compose down -v

# Listar serviços em execução
docker-compose ps

# Ver logs de todos os serviços
docker-compose logs

# Ver logs de um serviço específico
docker-compose logs -f servico1
```

### Exemplo de docker-compose.yml

```yaml
version: '3'
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
    volumes:
      - ./site:/usr/share/nginx/html
    depends_on:
      - api
  api:
    build: ./api
    environment:
      - DATABASE_URL=postgres://postgres:password@db:5432/app
    depends_on:
      - db
  db:
    image: postgres:13
    environment:
      - POSTGRES_PASSWORD=password
      - POSTGRES_DB=app
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## Solução de Problemas Comuns no PowerShell

### Permissões e Caminhos

```powershell
# Verificar permissões do Docker
docker version

# Reiniciar o serviço Docker
Restart-Service docker

# Corrigir problemas de caminho
# Use caminhos no formato: 
$caminhoCorrigido = $caminho -replace '\\', '/'
```

### Problemas de Conectividade

```powershell
# Verificar conectividade com o daemon do Docker
docker info

# Verificar configuração de redes
docker network inspect bridge

# Reiniciar rede Docker
docker network disconnect bridge <container>
docker network connect bridge <container>
```

### Problemas de WSL

```powershell
# Verificar status do WSL
wsl --status

# Atualizar WSL
wsl --update

# Reiniciar WSL
wsl --shutdown
```

## Integração com DevOps e Automação

### Scripts PowerShell para Docker

```powershell
# Script para automatizar build e deploy
function Build-DockerImage {
    param (
        [string]$ImageName,
        [string]$Version = "latest",
        [string]$DockerfilePath = "."
    )
    
    docker build -t "${ImageName}:${Version}" $DockerfilePath
    
    if ($LASTEXITCODE -eq 0) {
        Write-Output "Imagem ${ImageName}:${Version} criada com sucesso!"
    } else {
        Write-Error "Falha ao criar imagem!"
    }
}

# Usar o script
Build-DockerImage -ImageName "minha-app" -Version "1.0" -DockerfilePath "./src"
```

### Integração com CI/CD

```powershell
# Exemplo de script para pipeline
function Deploy-DockerContainer {
    param (
        [string]$ImageName,
        [string]$Version,
        [string]$ContainerName,
        [int]$Port = 80
    )
    
    # Parar e remover container existente
    docker rm -f $ContainerName 2>$null
    
    # Implantar nova versão
    docker run -d --name $ContainerName -p "${Port}:80" "${ImageName}:${Version}"
    
    # Verificar status
    Start-Sleep -Seconds 3
    $status = docker inspect -f "{{.State.Running}}" $ContainerName
    
    if ($status -eq "true") {
        Write-Output "Deploy bem-sucedido! Acessível em http://localhost:${Port}"
    } else {
        Write-Error "Falha no deploy. Verificando logs..."
        docker logs $ContainerName
    }
}
```

## Conclusão

O Docker CLI no PowerShell oferece uma experiência de linha de comando poderosa para gerenciar toda a infraestrutura Docker no Windows. Aproveitando a integração com o WSL e as capacidades do PowerShell, você pode criar fluxos de trabalho eficientes para desenvolvimento, teste e implementação de aplicações containerizadas.

Lembre-se de que é possível alternar entre PowerShell e terminais WSL conforme necessário, aproveitando o melhor de ambos os mundos para suas tarefas de desenvolvimento.

## Recursos Adicionais

- [Documentação Oficial do Docker](https://docs.docker.com/)
- [Guia de Início Rápido do Docker no Windows](https://docs.docker.com/desktop/windows/install/)
- [Documentação do WSL](https://docs.microsoft.com/windows/wsl/)
- [Cheat Sheet do Docker](https://www.docker.com/sites/default/files/d8/2019-09/docker-cheat-sheet.pdf)
