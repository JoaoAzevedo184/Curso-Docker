# Vulnerabilidades de Imagens Docker

## Introdução

A segurança de contêineres é uma preocupação crítica no desenvolvimento e produção de aplicações. As imagens Docker podem conter vulnerabilidades de segurança que representam riscos significativos para suas aplicações e infraestrutura. Compreender como identificar, avaliar e mitigar essas vulnerabilidades é essencial para manter um ambiente seguro.

## O que são Vulnerabilidades em Imagens Docker?

Vulnerabilidades são falhas de segurança encontradas nos componentes de uma imagem Docker, incluindo:

- **Sistema operacional base** (Ubuntu, Alpine, CentOS, etc.)
- **Bibliotecas e dependências** (OpenSSL, glibc, etc.)
- **Linguagens de programação** (Python, Node.js, Java, etc.)
- **Aplicações e ferramentas** instaladas na imagem
- **Configurações inadequadas** de segurança

### Tipos Comuns de Vulnerabilidades

1. **CVE (Common Vulnerabilities and Exposures)**
   - Identificadores únicos para vulnerabilidades conhecidas
   - Exemplo: CVE-2021-44228 (Log4Shell)

2. **Vulnerabilidades de Dependências**
   - Bibliotecas desatualizadas com falhas conhecidas
   - Dependências transitivas vulneráveis

3. **Configurações Inseguras**
   - Usuários privilegiados desnecessários
   - Portas expostas inadequadamente
   - Segredos hardcoded no código

4. **Malware e Backdoors**
   - Código malicioso em imagens não confiáveis
   - Backdoors intencionalmente inseridos

## Por que as Vulnerabilidades são Perigosas?

### Riscos Potenciais

1. **Escalação de Privilégios**
   - Atacantes podem ganhar acesso root
   - Escape do contêiner para o host

2. **Exfiltração de Dados**
   - Acesso não autorizado a dados sensíveis
   - Violação de informações confidenciais

3. **Ataques de Negação de Serviço (DoS)**
   - Indisponibilidade de serviços críticos
   - Consumo excessivo de recursos

4. **Propagação Lateral**
   - Movimento através da rede interna
   - Comprometimento de outros sistemas

5. **Compliance e Regulamentações**
   - Violação de normas como GDPR, HIPAA
   - Penalidades legais e financeiras

## Ferramentas para Análise de Vulnerabilidades

### 1. Docker Scout (Oficial Docker)

Docker Scout é a ferramenta oficial da Docker para análise de vulnerabilidades.

#### Instalação e Configuração
```bash
# Docker Scout já vem integrado com Docker Desktop
# Para CLI standalone:
curl -sSfL https://raw.githubusercontent.com/docker/scout-cli/main/install.sh | sh -s --

# Login no Docker Hub (necessário para alguns recursos)
docker login
```

#### Comandos Básicos
```bash
# Analisar uma imagem local
docker scout cves nginx:latest

# Análise detalhada com recomendações
docker scout recommendations nginx:latest

# Comparar duas imagens
docker scout compare nginx:1.20 --to nginx:latest

# Analisar imagem específica por plataforma
docker scout cves --platform linux/amd64 node:16
```

### 2. Trivy (Aqua Security)

Trivy é um scanner de vulnerabilidades abrangente e fácil de usar.

#### Instalação
```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy

# Via Docker
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image nginx:latest
```

#### Comandos Práticos
```bash
# Análise básica
trivy image nginx:latest

# Apenas vulnerabilidades HIGH e CRITICAL
trivy image --severity HIGH,CRITICAL nginx:latest

# Salvar resultado em arquivo JSON
trivy image --format json --output results.json nginx:latest

# Analisar filesystem local
trivy fs /path/to/project

# Analisar Dockerfile
trivy config Dockerfile
```

### 3. Snyk

Snyk oferece análise avançada com foco em dependências de aplicações.

```bash
# Instalar Snyk CLI
npm install -g snyk

# Autenticar
snyk auth

# Analisar imagem Docker
snyk container test nginx:latest

# Monitorar imagem continuamente
snyk container monitor nginx:latest --project-name=my-nginx
```

### 4. Clair (CoreOS)

Scanner open-source focado em análise de camadas de imagens.

```bash
# Executar Clair via Docker Compose
# (Configuração mais complexa, ideal para ambientes empresariais)
```

## Exemplo Prático: Analisando Vulnerabilidades

### Cenário: Comparando Imagens Node.js

```bash
# 1. Analisar imagem Node.js padrão
docker scout cves node:16

# 2. Analisar versão Alpine (geralmente mais segura)
docker scout cves node:16-alpine

# 3. Comparar as duas imagens
docker scout compare node:16 --to node:16-alpine

# 4. Análise detalhada com Trivy
trivy image --severity HIGH,CRITICAL node:16
trivy image --severity HIGH,CRITICAL node:16-alpine
```

### Interpretando os Resultados

#### Exemplo de Saída do Docker Scout:
```
## Overview

                    │           Analyzed Image            
────────────────────┼─────────────────────────────────────
  Target            │  node:16                           
  Digest            │  sha256:abc123...                  
  Platform          │  linux/amd64                       
  Vulnerabilities   │    2C     8H    45M    123L        
  Base image        │  ubuntu:20.04                      

## Vulnerabilities

### Critical
  CVE-2023-1234  │ OpenSSL Buffer Overflow
  CVE-2023-5678  │ glibc Memory Corruption

### High  
  CVE-2023-9012  │ Node.js Path Traversal
  ...
```

#### Níveis de Severidade:
- **Critical (C)**: Vulnerabilidades extremamente graves
- **High (H)**: Vulnerabilidades de alto risco
- **Medium (M)**: Vulnerabilidades de risco moderado
- **Low (L)**: Vulnerabilidades de baixo risco

## Estratégias de Mitigação

### 1. Escolha de Imagens Base Seguras

#### Prefira Imagens Distroless
```dockerfile
# ❌ Imagem completa com muitos componentes
FROM node:16

# ✅ Imagem distroless (apenas o necessário)
FROM gcr.io/distroless/nodejs:16
```

#### Use Imagens Alpine
```dockerfile
# ✅ Alpine é minimalista e frequentemente atualizada
FROM node:16-alpine
FROM python:3.9-alpine
FROM nginx:alpine
```

### 2. Multi-stage Builds

```dockerfile
# Build stage
FROM node:16-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

# Production stage
FROM node:16-alpine AS production
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
USER node
EXPOSE 3000
CMD ["node", "app.js"]
```

### 3. Atualizações Regulares

```dockerfile
# Sempre atualize pacotes durante o build
FROM ubuntu:20.04
RUN apt-get update && \
    apt-get upgrade -y && \
    apt-get install -y \
        curl \
        nginx && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

### 4. Usuários Não-privilegiados

```dockerfile
FROM node:16-alpine

# Criar usuário não-root
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nextjs -u 1001

# Definir ownership dos arquivos
COPY --chown=nextjs:nodejs . .

# Usar usuário não-root
USER nextjs

EXPOSE 3000
CMD ["node", "server.js"]
```

### 5. Secrets Management

```dockerfile
# ❌ Nunca faça isso
ENV DATABASE_PASSWORD=supersecret123

# ✅ Use secrets externos
# Docker Swarm
docker service create --secret db_password my-app

# Kubernetes
kubectl create secret generic db-secret --from-literal=password=supersecret123
```

## Integração em CI/CD

### GitHub Actions

```yaml
name: Security Scan
on: [push, pull_request]

jobs:
  security-scan:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Build Docker image
        run: docker build -t my-app:${{ github.sha }} .
      
      - name: Run Trivy vulnerability scanner
        uses: aquasecurity/trivy-action@master
        with:
          image-ref: 'my-app:${{ github.sha }}'
          format: 'sarif'
          output: 'trivy-results.sarif'
      
      - name: Upload Trivy scan results
        uses: github/codeql-action/upload-sarif@v2
        with:
          sarif_file: 'trivy-results.sarif'
      
      - name: Fail on high vulnerabilities
        run: |
          trivy image --exit-code 1 --severity HIGH,CRITICAL my-app:${{ github.sha }}
```

### GitLab CI

```yaml
stages:
  - build
  - security-scan
  - deploy

security_scan:
  stage: security-scan
  image: aquasec/trivy:latest
  script:
    - trivy image --exit-code 1 --severity HIGH,CRITICAL $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
  only:
    - merge_requests
    - main
```

## Monitoramento Contínuo

### 1. Runtime Security

```bash
# Usar ferramentas como Falco para monitoramento em runtime
# Detecta comportamentos anômalos em contêineres em execução
```

### 2. Policy as Code

```yaml
# Exemplo com Open Policy Agent (OPA)
package docker.security

deny[msg] {
    input.User == "root"
    msg := "Container should not run as root user"
}

deny[msg] {
    input.ExposedPorts["22/tcp"]
    msg := "SSH port should not be exposed"
}
```

### 3. Admission Controllers (Kubernetes)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-policy
spec:
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
  containers:
  - name: app
    image: my-app:latest
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
      capabilities:
        drop:
        - ALL
```

## Melhores Práticas de Segurança

### 1. **Princípio do Menor Privilégio**
- Execute contêineres como usuário não-root
- Remova capabilities desnecessárias
- Use read-only filesystem quando possível

### 2. **Imagens Minimalistas**
- Prefira imagens distroless ou Alpine
- Remova ferramentas desnecessárias
- Use multi-stage builds

### 3. **Atualizações Regulares**
- Mantenha imagens base atualizadas
- Monitore advisories de segurança
- Automatize atualizações quando possível

### 4. **Scanning Contínuo**
- Integre scanning no pipeline CI/CD
- Configure alertas para novas vulnerabilidades
- Estabeleça políticas de remediação

### 5. **Secrets e Configuração**
- Nunca inclua secrets nas imagens
- Use gestores de secrets adequados
- Configure adequadamente variáveis de ambiente

## Exemplo de Dockerfile Seguro

```dockerfile
# Use imagem base específica e atualizada
FROM node:18.17.0-alpine3.18

# Instalar apenas dependências necessárias e limpar cache
RUN apk add --no-cache \
    dumb-init \
    && rm -rf /var/cache/apk/*

# Criar usuário não-privilegiado
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nextjs -u 1001 -G nodejs

# Definir diretório de trabalho
WORKDIR /app

# Copiar arquivos de dependências
COPY --chown=nextjs:nodejs package*.json ./

# Instalar dependências de produção apenas
RUN npm ci --only=production && npm cache clean --force

# Copiar código da aplicação
COPY --chown=nextjs:nodejs . .

# Mudar para usuário não-root
USER nextjs

# Expor porta de aplicação
EXPOSE 3000

# Usar dumb-init para gerenciamento adequado de processos
ENTRYPOINT ["dumb-init", "--"]

# Comando de inicialização
CMD ["node", "server.js"]
```

## Conclusão

A segurança de imagens Docker é um aspecto fundamental que não pode ser negligenciado. A implementação de práticas adequadas de scanning, mitigação e monitoramento de vulnerabilidades é essencial para manter um ambiente de contêineres seguro.

### Pontos-chave desta aula:

1. **Identificação** de vulnerabilidades usando ferramentas especializadas
2. **Avaliação** de riscos baseada em severidade e contexto
3. **Mitigação** através de boas práticas de construção de imagens
4. **Integração** de security scanning em pipelines CI/CD
5. **Monitoramento contínuo** de vulnerabilidades em produção

A segurança é um processo contínuo que requer vigilância constante e atualização regular das práticas e ferramentas utilizadas. Investir em segurança desde o início do desenvolvimento é muito mais eficiente do que remediar problemas posteriormente.
