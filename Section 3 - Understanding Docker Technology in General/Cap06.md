# Ambiente de Desenvolvimento: VSCode + WSL2

## Introdução

A combinação VSCode + WSL2 (Windows Subsystem for Linux 2) oferece um ambiente de desenvolvimento poderoso para trabalhar com Docker no Windows. Esta configuração permite que você execute ferramentas Linux nativas, incluindo Docker, enquanto mantém a produtividade do Visual Studio Code no Windows.

## O que é WSL2?

O Windows Subsystem for Linux 2 (WSL2) é uma camada de compatibilidade que permite executar um ambiente Linux completo diretamente no Windows 10/11, sem a sobrecarga tradicional de uma máquina virtual.

### Principais características do WSL2:

- **Kernel Linux completo**: Executa um kernel Linux real
- **Compatibilidade total**: Suporte completo a chamadas do sistema
- **Performance otimizada**: Próxima à performance nativa do Linux
- **Integração com Windows**: Acesso aos arquivos do Windows e vice-versa
- **Recursos de rede**: Suporte completo a funcionalidades de rede

## Por que usar VSCode + WSL2 para Docker?

### Vantagens desta combinação:

1. **Ambiente Linux nativo**: Docker funciona nativamente no Linux
2. **Performance superior**: Melhor performance que Docker Desktop em alguns cenários
3. **Compatibilidade**: Scripts e ferramentas Linux funcionam sem modificações
4. **Desenvolvimento híbrido**: Edite no Windows, execute no Linux
5. **Integração perfeita**: VSCode se conecta transparentemente ao WSL2

## Pré-requisitos

### Requisitos do sistema:
- Windows 10 versão 2004 ou superior (Build 19041+)
- Windows 11 (qualquer versão)
- Arquitetura x64 ou ARM64
- Recurso de virtualização habilitado no BIOS/UEFI

## Instalação e Configuração

### 1. Instalando WSL2

#### Método rápido (Windows 11 e versões recentes do Windows 10):
```powershell
wsl --install
```

#### Método manual:

1. **Habilitar WSL**:
```powershell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

2. **Habilitar recurso de máquina virtual**:
```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

3. **Reiniciar o computador**

4. **Definir WSL2 como versão padrão**:
```powershell
wsl --set-default-version 2
```

5. **Instalar uma distribuição Linux**:
   - Abra a Microsoft Store
   - Procure por "Ubuntu" (ou sua distribuição preferida)
   - Clique em "Instalar"

### 2. Configurando o Ubuntu no WSL2

Após a instalação, abra o Ubuntu e:

1. **Configure usuário e senha**
2. **Atualize o sistema**:
```bash
sudo apt update && sudo apt upgrade -y
```

3. **Instale ferramentas essenciais**:
```bash
sudo apt install -y curl wget git build-essential
```

### 3. Instalando Docker no WSL2

```bash
# Remover versões antigas do Docker
sudo apt-get remove docker docker-engine docker.io containerd runc

# Instalar dependências
sudo apt-get update
sudo apt-get install -y \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

# Adicionar chave GPG oficial do Docker
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# Configurar repositório
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Instalar Docker Engine
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Adicionar usuário ao grupo docker
sudo usermod -aG docker $USER

# Reiniciar para aplicar as mudanças de grupo
exit
```

### 4. Configurando VSCode

#### Instalar VSCode no Windows:
1. Baixe o VSCode do site oficial
2. Execute a instalação normalmente

#### Instalar extensões essenciais:
- **WSL**: Conecta VSCode ao WSL2
- **Docker**: Gerenciamento de contêineres e imagens
- **Remote - Containers**: Desenvolvimento dentro de contêineres
- **GitLens**: Melhor integração com Git

## Usando VSCode com WSL2

### Conectando ao WSL2

1. **Pelo VSCode**:
   - Abra o VSCode
   - Pressione `Ctrl+Shift+P`
   - Digite "WSL: Connect to WSL"
   - Selecione sua distribuição

2. **Pelo terminal WSL**:
```bash
code .
```

### Estrutura de arquivos

Quando conectado ao WSL2, você verá:
- **Sistema de arquivos Linux**: `/home/username/`
- **Arquivos Windows**: `/mnt/c/Users/YourName/`

### Melhores práticas

1. **Mantenha projetos no sistema de arquivos Linux** para melhor performance
2. **Use o terminal integrado** do VSCode que automaticamente usa o WSL2
3. **Clone repositórios diretamente no WSL2**

## Workflow típico de desenvolvimento

### 1. Criando um novo projeto

```bash
# No terminal WSL2 (dentro do VSCode)
mkdir meu-projeto-docker
cd meu-projeto-docker
code .
```

### 2. Exemplo de Dockerfile

Crie um arquivo `Dockerfile`:
```dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

### 3. Construindo e executando

```bash
# Construir a imagem
docker build -t meu-app .

# Executar o contêiner
docker run -p 3000:3000 meu-app
```

## Comandos úteis

### WSL2
```bash
# Listar distribuições instaladas
wsl -l -v

# Definir distribuição padrão
wsl --set-default Ubuntu

# Parar WSL2
wsl --shutdown

# Verificar status
wsl --status
```

### Docker no WSL2
```bash
# Verificar se Docker está rodando
sudo service docker status

# Iniciar Docker
sudo service docker start

# Habilitar Docker para iniciar automaticamente
sudo systemctl enable docker
```

## Solução de problemas comuns

### Docker não inicia automaticamente

Adicione ao arquivo `~/.bashrc`:
```bash
# Iniciar Docker automaticamente
if ! service docker status > /dev/null; then
    sudo service docker start
fi
```

### Performance lenta

1. **Mantenha arquivos no sistema Linux**: `/home/` ao invés de `/mnt/c/`
2. **Configure exclusões no Windows Defender** para pastas do WSL2
3. **Use .dockerignore** para evitar sincronização desnecessária

### Problemas de permissão

```bash
# Verificar se usuário está no grupo docker
groups $USER

# Se não estiver, adicionar e reiniciar
sudo usermod -aG docker $USER
```

## Vantagens da configuração VSCode + WSL2

1. **Desenvolvimento nativo Linux**: Ferramentas e scripts funcionam como esperado
2. **Performance otimizada**: Melhor que Docker Desktop em muitos casos
3. **Integração perfeita**: Editor Windows com ambiente Linux
4. **Flexibilidade**: Pode alternar entre ambientes facilmente
5. **Recursos familiares**: Interface do VSCode que você já conhece

## Conclusão

A combinação VSCode + WSL2 oferece o melhor dos dois mundos: a familiaridade e produtividade do ambiente Windows com a potência e compatibilidade do Linux. Para desenvolvimento com Docker, esta configuração proporciona um ambiente robusto, performático e próximo ao que você encontraria em servidores de produção Linux.

Esta configuração estabelece uma base sólida para os próximos tópicos do curso, onde exploraremos conceitos mais avançados do Docker em um ambiente otimizado para desenvolvimento.
