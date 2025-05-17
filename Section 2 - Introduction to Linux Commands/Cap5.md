# Copiar e Mover Arquivos no Docker

A manipulação eficiente de arquivos dentro de contêineres Docker é essencial para administração, deploy e solução de problemas. Este documento aborda dois comandos fundamentais para gerenciamento de arquivos: `cp` e `mv`.

## Comando `cp` - Copiar Arquivos e Diretórios

O comando `cp` (copy) permite duplicar arquivos e diretórios dentro de um contêiner ou entre o sistema host e contêineres.

### Uso Básico do `cp`

```bash
# Sintaxe básica
cp [opções] origem destino
```

### Opções Comuns do `cp`

| Opção | Descrição |
|-------|-----------|
| `-r` ou `-R` | Copia diretórios recursivamente |
| `-p` | Preserva permissões, timestamps e atributos |
| `-v` | Modo verboso (mostra progresso) |
| `-i` | Modo interativo (pede confirmação antes de sobrescrever) |
| `-f` | Força sobrescrever sem confirmação |
| `-u` | Modo update (copia apenas se origem for mais recente) |

### Exemplos Práticos do `cp`

```bash
# Copiar um arquivo
cp arquivo.txt backup/arquivo.txt

# Copiar um arquivo e renomeá-lo
cp config.json config.json.bak

# Copiar múltiplos arquivos para um diretório
cp arquivo1.log arquivo2.log logs/

# Copiar um diretório inteiro e seu conteúdo
cp -r /app/config/ /app/config_backup/

# Copiar preservando metadados
cp -p script.sh /bin/script.sh

# Copiar apenas arquivos mais recentes
cp -u *.log /var/log/backup/
```

## Comando `mv` - Mover ou Renomear Arquivos

O comando `mv` (move) permite mover arquivos e diretórios ou renomeá-los.

### Uso Básico do `mv`

```bash
# Sintaxe básica
mv [opções] origem destino
```

### Opções Comuns do `mv`

| Opção | Descrição |
|-------|-----------|
| `-i` | Modo interativo (pede confirmação antes de sobrescrever) |
| `-f` | Força sobrescrever sem confirmação |
| `-v` | Modo verboso (mostra operações) |
| `-u` | Modo update (move apenas se origem for mais recente) |
| `-n` | Não sobrescreve arquivos existentes |
| `-b` | Cria backup de arquivos existentes antes de sobrescrever |

### Exemplos Práticos do `mv`

```bash
# Renomear um arquivo
mv arquivo.txt novo_nome.txt

# Mover um arquivo para outro diretório
mv data.json /app/data/

# Mover e renomear simultaneamente
mv config.old /etc/app/config.json

# Mover múltiplos arquivos para um diretório
mv *.log /var/log/arquivados/

# Mover um diretório inteiro
mv /app/temp/ /app/processados/

# Mover com confirmação antes de sobrescrever
mv -i importante.conf /etc/
```

## Uso em Ambientes Docker

### Copiar e Mover Dentro de Contêineres

Para manipular arquivos dentro de um contêiner em execução:

```bash
# Acessar shell do contêiner
docker exec -it nome_do_container bash

# Usar cp e mv normalmente dentro do contêiner
cp /tmp/arquivo.log /var/log/
mv /app/config.json.old /app/config.json
```

### Copiar Entre Host e Contêiner com `docker cp`

O Docker fornece um comando específico para copiar arquivos entre o sistema host e contêineres:

```bash
# Copiar do host para o contêiner
docker cp arquivo_local.txt container_name:/caminho/destino/

# Copiar do contêiner para o host
docker cp container_name:/app/logs/application.log ./logs/

# Copiar diretório inteiro
docker cp ./config/ container_name:/etc/app/

# Copiar conteúdo de um diretório (usando ponto no final)
docker cp ./config/. container_name:/etc/app/
```

### Considerações Importantes para `docker cp`

- O contêiner não precisa estar em execução para usar `docker cp`
- Não preserva links simbólicos por padrão
- As permissões de arquivo podem ser alteradas durante a cópia
- Não há comando `docker mv` equivalente 

## Estratégias Eficientes para Gerenciamento de Arquivos

### Volumes e Bind Mounts vs. `cp`/`mv`

Em muitos casos, usar volumes Docker é mais eficiente que copiar/mover arquivos:

```bash
# Montar diretório do host em um contêiner
docker run -v /caminho/host:/caminho/container imagem

# Usando volumes nomeados
docker volume create meu_volume
docker run -v meu_volume:/app/data imagem
```

### Casos de Uso Recomendados

| Operação | Ferramenta Recomendada |
|----------|------------------------|
| Configuração inicial | Dockerfile (COPY) |
| Dados temporários | `cp`/`mv` dentro do contêiner |
| Logs e dados persistentes | Volumes Docker |
| Migração de dados | `docker cp` |
| Atualização de configuração | `docker cp` ou volumes |

## Melhores Práticas

1. **Imutabilidade**: Evite modificar arquivos em contêineres em produção
2. **Persistência**: Use volumes para dados que precisam sobreviver ao ciclo de vida do contêiner
3. **Versionamento**: Mantenha backups antes de substituir arquivos críticos
4. **Automação**: Use scripts para operações repetitivas de cópia/movimentação
5. **Permissões**: Tenha cuidado com mudanças nas permissões de arquivos durante operações de cópia

## Exemplo de Fluxo de Trabalho de Desenvolvimento

```bash
# 1. Editar arquivo localmente
vim ./app/config.json

# 2. Copiar para o contêiner para teste
docker cp ./app/config.json app_container:/app/

# 3. Verificar se funciona no contêiner
docker exec -it app_container bash -c "cd /app && ./test.sh"

# 4. Mover arquivo para local definitivo no contêiner
docker exec -it app_container mv /app/config.json /etc/app/config.json

# 5. Reiniciar serviço para aplicar alterações
docker exec -it app_container service app restart
```