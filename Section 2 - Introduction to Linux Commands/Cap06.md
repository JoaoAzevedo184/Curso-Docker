# Edição de Arquivos no Docker

A manipulação e edição de arquivos dentro de contêineres Docker é uma habilidade essencial para qualquer desenvolvedor ou administrador de sistemas. Este documento apresenta as principais ferramentas disponíveis para visualizar e editar conteúdo de arquivos em ambientes Docker.

## Ferramentas de Visualização e Edição

### cat - Exibição Rápida de Conteúdo

O comando `cat` (concatenate) é uma das formas mais simples de visualizar o conteúdo completo de um arquivo:

```bash
# Visualiza o conteúdo completo de um arquivo
cat arquivo.txt

# Combina múltiplos arquivos
cat arquivo1.txt arquivo2.txt

# Exibe conteúdo com números de linha
cat -n arquivo.txt

# Redireciona conteúdo para um novo arquivo
cat arquivo1.txt > arquivo2.txt
```

### head - Visualização do Início do Arquivo

O comando `head` exibe as primeiras linhas de um arquivo, útil para arquivos grandes:

```bash
# Mostra as primeiras 10 linhas (padrão)
head arquivo.log

# Especifica o número de linhas a exibir
head -n 5 arquivo.log

# Visualiza múltiplos arquivos
head arquivo1.log arquivo2.log
```

### tail - Visualização do Final do Arquivo

O `tail` exibe as últimas linhas de um arquivo, extremamente útil para logs:

```bash
# Mostra as últimas 10 linhas (padrão)
tail arquivo.log

# Especifica o número de linhas a exibir
tail -n 20 arquivo.log

# Monitora alterações em tempo real (muito usado para logs)
tail -f arquivo.log
```

### echo - Criação e Manipulação Rápida de Conteúdo

O comando `echo` permite criar ou adicionar conteúdo a arquivos de forma simples:

```bash
# Cria um novo arquivo com conteúdo
echo "Olá Docker" > arquivo.txt

# Adiciona conteúdo ao final de um arquivo existente
echo "Nova linha" >> arquivo.txt

# Exibe variáveis de ambiente 
echo $VARIABLE_NAME

# Uso em scripts para feedback
echo "Comando executado com sucesso"
```

### vim - Editor de Texto Avançado

O `vim` é um editor poderoso com curva de aprendizado acentuada, mas muito eficiente:

```bash
# Abre um arquivo para edição
vim arquivo.txt
```

**Modos básicos do vim:**

1. **Modo normal**: Navegação e comandos (modo padrão)
2. **Modo inserção**: Edição de texto (pressione `i` para entrar)
3. **Modo comando**: Execução de comandos (pressione `:`)

**Comandos essenciais do vim:**
- `i` - Entra no modo inserção
- `Esc` - Volta ao modo normal
- `:w` - Salva o arquivo
- `:q` - Sai do editor
- `:wq` - Salva e sai
- `:q!` - Sai sem salvar (forçado)
- `/texto` - Busca por "texto"
- `dd` - Deleta linha atual
- `yy` - Copia linha atual
- `p` - Cola o conteúdo copiado

### nano - Editor de Texto Simplificado

O `nano` é mais amigável para iniciantes, com interface intuitiva:

```bash
# Abre um arquivo para edição
nano arquivo.txt
```

**Comandos básicos do nano:**
- `Ctrl+O` - Salvar arquivo
- `Ctrl+X` - Sair do editor
- `Ctrl+K` - Recortar linha
- `Ctrl+U` - Colar linha
- `Ctrl+W` - Buscar no texto
- `Ctrl+G` - Exibir ajuda

## Uso Prático no Docker

Dentro de um contêiner Docker, você pode usar esses comandos para:

```bash
# Entrar em um contêiner em execução
docker exec -it nome_do_container bash

# Visualizar arquivos de configuração
cat /etc/nginx/nginx.conf

# Monitorar logs em tempo real
tail -f /var/log/apache2/error.log

# Editar um arquivo de configuração
nano /app/config.json
```

## Considerações para Ambientes Docker

- Contêineres frequentemente não têm editores instalados por padrão
- Para uso permanente, considere adicionar ferramentas de edição na imagem (Dockerfile)
- Para edições temporárias, use `docker cp` para copiar arquivos entre host e contêiner
- Considere usar volumes para editar arquivos diretamente do host

## Instalação de Editores em Contêineres

```bash
# Para contêineres baseados em Debian/Ubuntu
apt-get update && apt-get install -y vim nano

# Para contêineres baseados em Alpine
apk add --no-cache vim nano
```