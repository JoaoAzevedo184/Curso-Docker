# Histórico de Comandos no Docker

O gerenciamento eficiente do histórico de comandos é uma habilidade essencial para trabalhar em ambientes Docker, permitindo acessar, reutilizar e automatizar comandos executados anteriormente. Este documento explora as ferramentas e técnicas para manipular o histórico de comandos tanto no host quanto dentro de contêineres Docker.

## Comando `history`

O comando `history` é a ferramenta principal para visualizar os comandos executados anteriormente no shell.

### Uso Básico

```bash
# Visualizar o histórico de comandos
history

# Visualizar um número específico de comandos recentes
history 10

# Buscar no histórico (usando grep)
history | grep docker
```

### Opções Úteis do `history`

| Opção | Descrição |
|-------|-----------|
| `-c` | Limpa todo o histórico atual |
| `-d` | Remove uma entrada específica do histórico |
| `-a` | Adiciona linhas do histórico atual ao arquivo de histórico |
| `-r` | Lê o arquivo de histórico e adiciona ao histórico atual |
| `-w` | Escreve o histórico atual no arquivo de histórico |

## Configuração do Histórico

O comportamento do histórico de comandos é controlado por variáveis de ambiente:

```bash
# Número de comandos armazenados no histórico
export HISTSIZE=1000

# Número de comandos armazenados no arquivo de histórico
export HISTFILESIZE=2000

# Arquivo que armazena o histórico
export HISTFILE=~/.bash_history

# Formato de timestamp para o histórico (disponível em alguns shells)
export HISTTIMEFORMAT="%F %T "

# Não armazena comandos duplicados consecutivos
export HISTCONTROL=ignoredups

# Não armazena comandos que começam com espaço
export HISTCONTROL=ignorespace

# Combina as duas opções acima
export HISTCONTROL=ignoreboth
```

## Reutilização de Comandos

### Atalhos de Teclado e Expansões

| Comando | Função |
|---------|--------|
| `↑` / `↓` | Navega pelo histórico de comandos |
| `Ctrl+R` | Busca interativa no histórico |
| `!!` | Repete o último comando |
| `!n` | Executa o comando número 'n' do histórico |
| `!-n` | Executa o comando 'n' posições atrás no histórico |
| `!string` | Executa o comando mais recente que começa com 'string' |
| `!?string` | Executa o comando mais recente que contém 'string' |
| `^string1^string2` | Repete o último comando substituindo 'string1' por 'string2' |

### Exemplos Práticos

```bash
# Repetir o último comando
!!

# Repetir o último comando com sudo
sudo !!

# Executar o comando número 42 do histórico
!42

# Executar o comando mais recente que começa com 'docker'
!docker

# Repetir o último comando substituindo 'app1' por 'app2'
^app1^app2^

# Buscar interativamente um comando que contenha 'volume'
# (Pressione Ctrl+R e digite 'volume')
```

## Histórico em Ambientes Docker

### Persistência do Histórico em Contêineres

Por padrão, o histórico de comandos em contêineres não é persistente entre reinicializações. Para manter o histórico:

```bash
# Criar um volume para o histórico de comandos
docker volume create bash_history

# Executar um contêiner com histórico persistente
docker run -it --name meu_container \
  -v bash_history:/root/.bash_history \
  ubuntu:latest bash
```

### Visualizando Comandos Executados em Contêineres

```bash
# Ver histórico de comandos dentro de um contêiner em execução
docker exec -it meu_container bash -c "history"

# Extrair o arquivo de histórico de um contêiner
docker cp meu_container:/root/.bash_history ./container_history.txt
```

## Docker Command History no Host

O comando `docker history` (diferente do `history` do shell) mostra o histórico de camadas de uma imagem:

```bash
# Ver as camadas de uma imagem Docker
docker history ubuntu:latest

# Mostrar tamanhos completos (não truncados)
docker history --no-trunc nginx:alpine

# Exibir sem cabeçalhos
docker history --quiet redis:latest
```

## Histórico de Execução do Docker

O Docker não mantém um histórico nativo de comandos `docker run`, mas há técnicas para rastrear:

```bash
# Listar contêineres em execução com detalhes de criação
docker ps --no-trunc

# Listar todos os contêineres (inclusive os parados)
docker ps -a

# Ver logs de um contêiner específico
docker logs meu_container
```

## Melhores Práticas para Gerenciamento de Histórico

1. **Configuração Pessoal**: Configure variáveis como `HISTSIZE` e `HISTCONTROL` em `~/.bashrc` ou `~/.zshrc`

2. **Segurança**:
   ```bash
   # Evitar que comandos com senhas sejam armazenados
   export HISTIGNORE="*password*:*secret*:*token*"
   ```

3. **Timestamps**:
   ```bash
   # Adicionar timestamps ao histórico para melhor rastreabilidade
   export HISTTIMEFORMAT="%Y-%m-%d %H:%M:%S "
   ```

4. **Sincronização Imediata**:
   ```bash
   # Gravar cada comando no histórico imediatamente
   export PROMPT_COMMAND="history -a; $PROMPT_COMMAND"
   ```

5. **Histórico Compartilhado**:
   ```bash
   # Compartilhar histórico entre sessões (bash)
   shopt -s histappend
   ```

## Ferramentas Avançadas

### Script - Gravação de Sessão

A ferramenta `script` permite gravar uma sessão completa de terminal:

```bash
# Iniciar gravação de sessão
script -a sessao_docker.log

# Realizar operações Docker...

# Encerrar gravação (Ctrl+D ou 'exit')
exit

# Visualizar a sessão gravada
less sessao_docker.log
```

### Alias para Comandos de Histórico

```bash
# Alias úteis para histórico (adicionar ao ~/.bashrc)
alias h='history'
alias hg='history | grep'
alias hc='history -c'
```

## Automação com Histórico

### Criação de Scripts a partir do Histórico

```bash
# Extrair comandos do histórico para um script
history | grep docker | cut -c 8- > docker_commands.sh

# Tornar o script executável
chmod +x docker_commands.sh
```

### Integração com CI/CD

O histórico de comandos pode ser usado para:

1. Gerar scripts de provisionamento
2. Documentar procedimentos operacionais
3. Criar Dockerfiles a partir de sequências de comandos
4. Auditar operações em ambientes de produção

## Depuração e Solução de Problemas

O histórico de comandos é uma ferramenta valiosa para depuração:

```bash
# Encontrar o último comando que causou erro
history | grep -B 5 -A 5 "error"

# Identificar quem executou um determinado comando (em sistemas multi-usuário)
history | grep "rm -rf" | grep -i "$(who)"
```

## Recuperação e Backup

```bash
# Fazer backup do histórico de comandos
cp ~/.bash_history ~/bash_history_backup_$(date +%Y%m%d)

# Restaurar histórico de um backup
cp ~/bash_history_backup_20230315 ~/.bash_history
```