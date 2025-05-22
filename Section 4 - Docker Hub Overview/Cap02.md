# Como Instalar o Editor Micro via Terminal

## Introdução

O Micro é um editor de texto moderno e intuitivo para terminal, projetado para ser fácil de usar e altamente configurável. É uma excelente alternativa aos editores tradicionais como vim e nano, especialmente para quem está começando com linha de comando.

### Por que usar o Micro?

- ✅ Interface intuitiva com atalhos familiares (Ctrl+C, Ctrl+V, etc.)
- ✅ Suporte a syntax highlighting para diversas linguagens
- ✅ Sistema de plugins extensível
- ✅ Multiplataforma (Linux, macOS, Windows)
- ✅ Leve e rápido
- ✅ Não requer configuração inicial complexa

## Instalação do Micro

### 1. Método Oficial (Recomendado)

O método mais simples e atualizado:

```bash
# Download e instalação automática
curl https://getmic.ro | bash

# Mover para diretório do sistema (opcional)
sudo mv micro /usr/local/bin/
```

### 2. Via Gerenciadores de Pacotes

#### Ubuntu/Debian
```bash
# Atualizar repositórios
sudo apt update

# Instalar micro
sudo apt install micro
```

#### CentOS/RHEL/Fedora
```bash
# Fedora
sudo dnf install micro

# CentOS/RHEL com EPEL
sudo yum install epel-release
sudo yum install micro
```

#### macOS
```bash
# Via Homebrew
brew install micro

# Via MacPorts
sudo port install micro
```

#### Arch Linux
```bash
# Via pacman
sudo pacman -S micro

# Via AUR
yay -S micro-git
```

### 3. Download Manual

```bash
# Verificar arquitetura do sistema
uname -m

# Baixar versão específica (exemplo para Linux x64)
wget https://github.com/zyedidia/micro/releases/download/v2.0.11/micro-2.0.11-linux64.tar.gz

# Extrair arquivo
tar -xzf micro-2.0.11-linux64.tar.gz

# Mover para diretório executável
sudo mv micro-2.0.11/micro /usr/local/bin/

# Dar permissão de execução
sudo chmod +x /usr/local/bin/micro
```

### 4. Instalação em Container Docker

```dockerfile
# Em um Dockerfile
FROM ubuntu:20.04

RUN apt-get update && \
    apt-get install -y curl && \
    curl https://getmic.ro | bash && \
    mv micro /usr/local/bin/ && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

Ou via comando direto:
```bash
# Instalar em container em execução
docker exec -it container-name bash
curl https://getmic.ro | bash
mv micro /usr/local/bin/
```

## Verificando a Instalação

```bash
# Verificar se foi instalado corretamente
micro --version

# Verificar localização do executável
which micro

# Informações detalhadas
micro --help
```

## Uso Básico do Micro

### Iniciando o Editor

```bash
# Abrir micro vazio
micro

# Abrir arquivo específico
micro arquivo.txt

# Criar novo arquivo
micro novo-arquivo.py

# Abrir múltiplos arquivos
micro arquivo1.txt arquivo2.py arquivo3.md
```

### Atalhos Essenciais

| Atalho | Função |
|--------|--------|
| `Ctrl + S` | Salvar arquivo |
| `Ctrl + Q` | Sair do editor |
| `Ctrl + O` | Abrir arquivo |
| `Ctrl + W` | Fechar aba atual |
| `Ctrl + C` | Copiar |
| `Ctrl + V` | Colar |
| `Ctrl + X` | Recortar |
| `Ctrl + Z` | Desfazer |
| `Ctrl + Y` | Refazer |
| `Ctrl + F` | Buscar |
| `Ctrl + H` | Buscar e substituir |
| `Ctrl + G` | Ir para linha |
| `Ctrl + A` | Selecionar tudo |
| `Ctrl + E` | Executar comando |

### Navegação

| Atalho | Função |
|--------|--------|
| `Ctrl + ←/→` | Mover palavra por palavra |
| `Home/End` | Início/fim da linha |
| `Ctrl + Home/End` | Início/fim do arquivo |
| `Page Up/Down` | Rolar página |
| `Ctrl + L` | Ir para linha específica |

## Configuração e Personalização

### Arquivo de Configuração

O Micro cria automaticamente seu arquivo de configuração:

```bash
# Localização do arquivo de configuração
~/.config/micro/settings.json

# Editar configurações
micro ~/.config/micro/settings.json
```

### Configurações Úteis

```json
{
    "colorscheme": "monokai",
    "tabsize": 4,
    "tabstospaces": true,
    "ruler": true,
    "softwrap": true,
    "scrollmargin": 3,
    "scrollspeed": 2,
    "colorcolumn": 80,
    "cursorline": true,
    "statusline": true,
    "savecursor": true,
    "saveundo": true,
    "syntax": true,
    "autoindent": true,
    "autosave": false,
    "basename": false,
    "fileformat": "unix"
}
```

### Temas (Colorschemes)

```bash
# Ver temas disponíveis
micro -colorscheme help

# Alguns temas populares:
# - monokai
# - solarized
# - darcula
# - atom-dark
# - zenburn
# - gruvbox

# Alterar tema temporariamente
micro -colorscheme monokai arquivo.txt

# Alterar permanentemente
micro ~/.config/micro/settings.json
# Adicionar: "colorscheme": "monokai"
```

## Plugins

### Gerenciamento de Plugins

```bash
# Listar plugins disponíveis
micro -plugin available

# Instalar plugin
micro -plugin install nome-do-plugin

# Remover plugin
micro -plugin remove nome-do-plugin

# Atualizar plugins
micro -plugin update

# Listar plugins instalados
micro -plugin list
```

### Plugins Úteis

1. **filemanager** - Navegador de arquivos
2. **fzf** - Busca fuzzy de arquivos
3. **linter** - Verificação de sintaxe
4. **autoclose** - Fecha automaticamente parênteses
5. **comment** - Comentários rápidos
6. **jump** - Navegação rápida
7. **snippets** - Snippets de código

```bash
# Instalar plugins essenciais
micro -plugin install filemanager
micro -plugin install fzf
micro -plugin install linter
micro -plugin install autoclose
```

## Exemplo Prático: Editando Dockerfile

```bash
# Criar e editar um Dockerfile
micro Dockerfile
```

No editor, você pode digitar:

```dockerfile
FROM ubuntu:20.04

LABEL maintainer="seu-email@exemplo.com"

RUN apt-get update && apt-get install -y \
    curl \
    vim \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY . .

CMD ["bash"]
```

Usar `Ctrl + S` para salvar e `Ctrl + Q` para sair.

## Comandos Internos

Pressione `Ctrl + E` para abrir o prompt de comandos:

```
# Comandos úteis no micro
> help           # Ajuda
> save           # Salvar arquivo
> quit           # Sair
> find           # Buscar texto
> replace        # Substituir texto
> goto 50        # Ir para linha 50
> set tabsize 2  # Definir tamanho da tab
> plugin install nome  # Instalar plugin
> reload         # Recarregar arquivo
```

## Integração com Git

O Micro detecta automaticamente repositórios Git e pode mostrar:

- Status de modificações nas linhas
- Branch atual na barra de status
- Arquivos ignorados pelo .gitignore

## Comparação com Outros Editores

| Recurso | Micro | Nano | Vim |
|---------|-------|------|-----|
| Curva de aprendizado | Baixa | Baixa | Alta |
| Atalhos intuitivos | ✅ | ❌ | ❌ |
| Syntax highlighting | ✅ | ✅ | ✅ |
| Plugins | ✅ | ❌ | ✅ |
| Múltiplas abas | ✅ | ❌ | ✅ |
| Mouse support | ✅ | ✅ | ✅ |
| Tamanho | Pequeno | Muito pequeno | Médio |

## Dicas e Truques

### 1. Edição em Múltiplas Linhas
- Segure `Alt` e clique para criar múltiplos cursores
- Use `Ctrl + Shift + ↑/↓` para selecionar linhas

### 2. Dividir Tela
```bash
# Abrir arquivo em nova aba
Ctrl + T

# Dividir tela horizontalmente
Ctrl + Shift + E → hsplit arquivo.txt

# Dividir tela verticalmente
Ctrl + Shift + E → vsplit arquivo.txt
```

### 3. Macros
```bash
# Iniciar gravação de macro
Ctrl + U

# Executar macro gravado
Ctrl + J
```

## Solução de Problemas

### Problema: Comando não encontrado
```bash
# Verificar se está no PATH
echo $PATH

# Adicionar ao PATH se necessário
echo 'export PATH="$PATH:/usr/local/bin"' >> ~/.bashrc
source ~/.bashrc
```

### Problema: Permissões
```bash
# Dar permissão de execução
chmod +x /caminho/para/micro
```

### Problema: Configurações não salvam
```bash
# Criar diretório de configuração
mkdir -p ~/.config/micro

# Verificar permissões
ls -la ~/.config/micro
```

## Recursos Avançados

### Scripts e Automação

```bash
# Abrir arquivo em linha específica
micro +25 arquivo.txt

# Executar comando e editar resultado
ls -la | micro

# Editar múltiplos arquivos com padrão
micro *.py
```

### Integração com Docker

```bash
# Editar arquivos em container
docker exec -it container-name micro /app/config.json

# Montar volume para persistir configurações
docker run -v ~/.config/micro:/root/.config/micro -it ubuntu bash
```

## Próximos Passos

- Explorar plugins avançados
- Configurar temas personalizados
- Integrar com ferramentas de desenvolvimento
- Aprender comandos avançados de busca e substituição

## Recursos Adicionais

- [Site oficial do Micro](https://micro-editor.github.io/)
- [Repositório no GitHub](https://github.com/zyedidia/micro)
- [Documentação completa](https://github.com/zyedidia/micro/tree/master/runtime/help)
- [Lista de plugins](https://github.com/micro-editor/plugin-channel)