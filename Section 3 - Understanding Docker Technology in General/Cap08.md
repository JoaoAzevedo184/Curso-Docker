# Como Criar Imagens Docker via Terminal

## Introdução

Criar imagens Docker é uma habilidade fundamental para qualquer desenvolvedor que trabalha com containerização. As imagens são a base dos containers e podem ser criadas de diferentes formas via terminal.

## Métodos para Criar Imagens

### 1. Usando Dockerfile (Método Recomendado)

O Dockerfile é um arquivo de texto que contém instruções para construir uma imagem Docker de forma automatizada e reproduzível.

#### Estrutura Básica de um Dockerfile

```dockerfile
# Imagem base
FROM ubuntu:20.04

# Informações do mantenedor
LABEL maintainer="seu-email@exemplo.com"

# Atualizar pacotes e instalar dependências
RUN apt-get update && apt-get install -y \
    python3 \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Definir diretório de trabalho
WORKDIR /app

# Copiar arquivos
COPY . /app

# Instalar dependências da aplicação
RUN pip3 install -r requirements.txt

# Expor porta
EXPOSE 8080

# Comando padrão
CMD ["python3", "app.py"]
```

#### Construindo a Imagem

```bash
# Sintaxe básica
docker build -t nome-da-imagem:tag .

# Exemplo prático
docker build -t minha-app:1.0 .

# Especificar arquivo Dockerfile diferente
docker build -f Dockerfile.prod -t minha-app:prod .

# Construir sem usar cache
docker build --no-cache -t minha-app:1.0 .
```

### 2. Usando docker commit (Não Recomendado para Produção)

Este método cria uma imagem a partir de um container existente:

```bash
# 1. Executar um container
docker run -it ubuntu:20.04 /bin/bash

# 2. Fazer modificações no container
apt-get update
apt-get install -y nginx

# 3. Em outro terminal, fazer commit
docker commit container-id minha-imagem:1.0

# Ou com mais opções
docker commit -m "Adicionado nginx" -a "Seu Nome" container-id minha-imagem:1.0
```

## Principais Instruções do Dockerfile

### FROM
Define a imagem base:
```dockerfile
FROM ubuntu:20.04
FROM node:16-alpine
FROM scratch  # imagem vazia
```

### RUN
Executa comandos durante a construção:
```dockerfile
RUN apt-get update && apt-get install -y curl
RUN npm install
```

### COPY e ADD
Copia arquivos para a imagem:
```dockerfile
COPY src/ /app/src/
ADD arquivo.tar.gz /app/  # ADD também extrai arquivos
```

### WORKDIR
Define o diretório de trabalho:
```dockerfile
WORKDIR /app
```

### EXPOSE
Documenta portas que serão expostas:
```dockerfile
EXPOSE 8080
EXPOSE 3000 80
```

### ENV
Define variáveis de ambiente:
```dockerfile
ENV NODE_ENV=production
ENV PATH="/app/bin:${PATH}"
```

### CMD e ENTRYPOINT
Define comando padrão:
```dockerfile
# CMD - pode ser sobrescrito
CMD ["python3", "app.py"]

# ENTRYPOINT - sempre executado
ENTRYPOINT ["python3"]
CMD ["app.py"]  # argumentos padrão
```

## Boas Práticas

### 1. Otimização de Layers
```dockerfile
# ❌ Ruim - múltiplos layers
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y vim

# ✅ Bom - layer único
RUN apt-get update && apt-get install -y \
    curl \
    vim \
    && rm -rf /var/lib/apt/lists/*
```

### 2. Usar .dockerignore
Crie um arquivo `.dockerignore` para excluir arquivos desnecessários:
```
node_modules
npm-debug.log
.git
.gitignore
README.md
.env
coverage/
.nyc_output
```

### 3. Multi-stage Builds
Para otimizar tamanho da imagem final:
```dockerfile
# Stage 1: Build
FROM node:16 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Stage 2: Runtime
FROM node:16-alpine
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
CMD ["node", "server.js"]
```

### 4. Usar Imagens Alpine
Imagens Alpine são menores e mais seguras:
```dockerfile
FROM node:16-alpine  # ~110MB
# ao invés de
FROM node:16         # ~350MB
```

## Comandos Úteis

### Listar Imagens
```bash
# Listar todas as imagens
docker images

# Listar com filtros
docker images --filter "dangling=true"
docker images --format "table {{.Repository}}\t{{.Tag}}\t{{.Size}}"
```

### Inspecionar Imagem
```bash
# Ver detalhes da imagem
docker inspect minha-imagem:1.0

# Ver histórico de layers
docker history minha-imagem:1.0
```

### Remover Imagens
```bash
# Remover imagem específica
docker rmi minha-imagem:1.0

# Remover imagens não utilizadas
docker image prune

# Remover todas as imagens
docker rmi $(docker images -q)
```

### Tagear Imagens
```bash
# Criar nova tag
docker tag minha-imagem:1.0 minha-imagem:latest

# Tag para registry
docker tag minha-imagem:1.0 registry.com/usuario/minha-imagem:1.0
```

## Exemplo Prático Completo

### Aplicação Python Flask

**app.py**
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello Docker!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080)
```

**requirements.txt**
```
Flask==2.3.3
```

**Dockerfile**
```dockerfile
FROM python:3.9-alpine

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 8080

CMD ["python", "app.py"]
```

**Construir e executar:**
```bash
# Construir imagem
docker build -t flask-app:1.0 .

# Executar container
docker run -p 8080:8080 flask-app:1.0

# Testar
curl http://localhost:8080
```

## Troubleshooting

### Problemas Comuns

1. **Erro de contexto muito grande:**
   ```bash
   # Usar .dockerignore ou especificar contexto menor
   docker build -t app:1.0 ./src
   ```

2. **Cache não funcionando:**
   ```bash
   # Forçar rebuild sem cache
   docker build --no-cache -t app:1.0 .
   ```

3. **Permissões no container:**
   ```dockerfile
   # Criar usuário não-root
   RUN addgroup -g 1001 -S nodejs
   RUN adduser -S nextjs -u 1001
   USER nextjs
   ```

## Próximos Passos

- Aprender sobre Docker Registry e push de imagens
- Explorar Docker Compose para múltiplos containers
- Estudar segurança em imagens Docker
- Implementar CI/CD para construção automática de imagens

## Recursos Adicionais

- [Documentação oficial do Dockerfile](https://docs.docker.com/engine/reference/builder/)
- [Best practices for writing Dockerfiles](https://docs.docker.com/develop/dev-best-practices/)
- [Docker Hub](https://hub.docker.com/) para imagens prontas