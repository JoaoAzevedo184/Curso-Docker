# Docker Hub - Explorando e Utilizando Imagens

## Introdução

O Docker Hub é o maior registro público de imagens de contêiner do mundo. É o local onde desenvolvedores e organizações compartilham suas aplicações containerizadas, oferecendo milhões de imagens prontas para uso.

## O que é o Docker Hub?

O Docker Hub é um serviço de registro baseado em nuvem que permite:
- **Armazenar** e **compartilhar** imagens Docker
- **Descobrir** imagens oficiais e da comunidade
- **Automatizar** builds através de integração com repositórios Git
- **Colaborar** em projetos de containerização

### Características principais:
- **Repositórios públicos gratuitos** ilimitados
- **Repositórios privados** (limitados na conta gratuita)
- **Imagens oficiais** mantidas pela Docker Inc. e parceiros
- **Builds automáticos** conectados ao GitHub/Bitbucket
- **Webhooks** para integração com CI/CD

## Site para Praticar: Play with Docker

**URL**: https://labs.play-with-docker.com

O Play with Docker é uma plataforma online gratuita que oferece:
- **Ambiente Docker completo** no navegador
- **Múltiplas instâncias** para simular clusters
- **4 horas de uso gratuito** por sessão
- **Sem necessidade de instalação** local
- **Ideal para aprendizado** e experimentação

### Como usar o Play with Docker:
1. Acesse https://labs.play-with-docker.com
2. Faça login com sua conta Docker Hub
3. Clique em "Start" para iniciar uma nova sessão
4. Clique em "+ Add New Instance" para criar uma máquina virtual
5. Use o terminal web para executar comandos Docker

## Explorando Imagens no Docker Hub

### Anatomia de uma página de imagem

Quando você visita uma imagem no Docker Hub, encontra:

#### 1. **Informações básicas**
- Nome da imagem
- Descrição
- Número de downloads (pulls)
- Classificação por estrelas
- Data da última atualização

#### 2. **Tags disponíveis**
- Diferentes versões da imagem
- Tags especiais como `latest`, `alpine`, `slim`
- Tags específicas de versão como `16.20`, `18.17`

#### 3. **Dockerfile**
- Link para o código-fonte da imagem
- Transparência sobre como a imagem foi construída

#### 4. **Documentação**
- Como usar a imagem
- Variáveis de ambiente disponíveis
- Exemplos de comandos

## Tipos de Imagens no Docker Hub

### 1. **Imagens Oficiais**
- Mantidas pela Docker Inc. ou pelos fornecedores oficiais
- **Identificadas** pelo nome simples (ex: `nginx`, `node`, `python`)
- **Altamente confiáveis** e bem documentadas
- **Atualizadas regularmente**

Exemplos:
```bash
docker pull ubuntu
docker pull nginx
docker pull node
docker pull mysql
```

### 2. **Imagens Verificadas**
- Publicadas por organizações verificadas
- **Selo de verificação** azul
- **Qualidade assegurada**

### 3. **Imagens da Comunidade**
- Criadas por usuários individuais ou organizações
- **Formato**: `usuario/nome-imagem`
- **Qualidade variável**

Exemplo:
```bash
docker pull meuusuario/minha-aplicacao
```

## Descarregando Imagens do Docker Hub

### Comando básico - docker pull

```bash
# Sintaxe básica
docker pull [OPÇÕES] NOME[:TAG]

# Exemplos
docker pull ubuntu                    # Baixa ubuntu:latest
docker pull ubuntu:20.04            # Baixa versão específica
docker pull node:16-alpine          # Baixa versão Node.js 16 Alpine
```

### Exemplo Prático: Imagem Node.js

Vamos trabalhar com a imagem oficial do Node.js:

#### 1. Explorando as tags disponíveis

No Docker Hub (https://hub.docker.com/_/node), você encontrará tags como:
- `node:latest` - Versão mais recente
- `node:18` - Node.js versão 18
- `node:16` - Node.js versão 16
- `node:18-alpine` - Versão 18 baseada no Alpine Linux (menor)
- `node:16-slim` - Versão 16 otimizada

#### 2. Baixando a imagem

```bash
# Baixar a versão mais recente
docker pull node:latest

# Verificar se a imagem foi baixada
docker images node
```

#### 3. Executando um contêiner Node.js interativo

```bash
# Comando corrigido (o original tinha um erro de sintaxe)
docker run -it --name node_container node:latest bash
```

**Anatomia do comando:**
- `docker run`: Cria e executa um contêiner
- `-it`: Combina `-i` (interativo) e `-t` (pseudo-TTY)
  - `-i`: Mantém STDIN aberto
  - `-t`: Aloca um pseudo-TTY (terminal)
- `--name node_container`: Define o nome do contêiner
- `node:latest`: Imagem a ser usada
- `bash`: Comando a ser executado (shell bash)

#### 4. Explorando o ambiente Node.js

Dentro do contêiner, você pode:

```bash
# Verificar a versão do Node.js
node --version

# Verificar a versão do npm
npm --version

# Executar Node.js interativamente
node

# Listar arquivos do sistema
ls -la

# Ver informações do sistema
cat /etc/os-release

# Sair do contêiner
exit
```

## Comandos Úteis para Gerenciar Imagens

### Listar imagens locais
```bash
# Todas as imagens
docker images

# Apenas imagens Node.js
docker images node

# Com filtros
docker images --filter "dangling=true"
```

### Informações detalhadas sobre uma imagem
```bash
# Histórico de camadas
docker history node:latest

# Informações completas
docker inspect node:latest
```

### Removendo imagens
```bash
# Remover uma imagem específica
docker rmi node:16

# Remover imagens não utilizadas
docker image prune

# Remover todas as imagens não utilizadas (cuidado!)
docker image prune -a
```

## Exercícios Práticos

### Exercício 1: Explorando diferentes versões do Node.js

```bash
# 1. Baixar diferentes versões
docker pull node:16
docker pull node:18
docker pull node:16-alpine

# 2. Comparar tamanhos
docker images node

# 3. Testar cada versão
docker run --rm node:16 node --version
docker run --rm node:18 node --version
docker run --rm node:16-alpine node --version
```

### Exercício 2: Criando um ambiente de desenvolvimento

```bash
# 1. Executar Node.js com volume para código
mkdir meu-projeto-node
cd meu-projeto-node

# 2. Criar um arquivo JavaScript simples
echo 'console.log("Hello from Docker Node.js!");' > app.js

# 3. Executar o arquivo no contêiner
docker run --rm -v $(pwd):/app -w /app node:latest node app.js
```

### Exercício 3: Sessão de desenvolvimento interativa

```bash
# 1. Iniciar contêiner com volume persistente
docker run -it --name dev-node -v $(pwd):/workspace -w /workspace node:latest bash

# 2. Dentro do contêiner, inicializar projeto npm
npm init -y

# 3. Instalar dependências
npm install express

# 4. Criar servidor simples
cat > server.js << EOF
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send('Hello from Docker Node.js Server!');
});

app.listen(3000, () => {
    console.log('Server running on port 3000');
});
EOF

# 5. Executar o servidor
node server.js
```

## Melhores Práticas para Usar Docker Hub

### 1. **Escolha imagens oficiais** quando possível
- Mais seguras e confiáveis
- Melhor documentação
- Atualizações regulares

### 2. **Use tags específicas** em produção
```bash
# ❌ Evite em produção
docker pull node:latest

# ✅ Prefira versões específicas
docker pull node:18.17.0
```

### 3. **Verifique a documentação** antes de usar
- Leia sobre variáveis de ambiente
- Entenda os volumes necessários
- Verifique exemplos de uso

### 4. **Considere o tamanho** das imagens
```bash
# Imagem completa (~900MB)
docker pull node:18

# Imagem Alpine (~170MB)
docker pull node:18-alpine

# Imagem slim (~240MB)
docker pull node:18-slim
```

### 5. **Monitore atualizações** de segurança
- Configure alertas para imagens críticas
- Atualize regularmente suas imagens base

## Comandos de Busca no Docker Hub

```bash
# Buscar imagens relacionadas ao Node.js
docker search node

# Buscar com filtros
docker search --filter stars=50 node

# Buscar imagens oficiais
docker search --filter is-official=true node
```

## Troubleshooting Comum

### Problema: Pull muito lento
```bash
# Usar mirror local (se disponível)
docker pull --platform linux/amd64 node:latest

# Verificar conectividade
curl -I https://registry-1.docker.io
```

### Problema: Erro de autenticação
```bash
# Fazer login no Docker Hub
docker login

# Verificar se está logado
docker info | grep Username
```

### Problema: Espaço em disco
```bash
# Ver uso de espaço
docker system df

# Limpar recursos não utilizados
docker system prune -a
```

## Conclusão

O Docker Hub é um recurso fundamental no ecossistema Docker, oferecendo milhões de imagens prontas para uso. Compreender como navegar, baixar e utilizar essas imagens é essencial para o desenvolvimento eficiente com Docker.

Principais pontos desta aula:
- **Exploração** do Docker Hub como repositório central
- **Download** e uso de imagens oficiais
- **Execução interativa** de contêineres para desenvolvimento
- **Melhores práticas** para seleção e uso de imagens

O Play with Docker oferece uma excelente plataforma para experimentar esses conceitos sem necessidade de instalação local, sendo ideal para aprendizado e testes rápidos.
