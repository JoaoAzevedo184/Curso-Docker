# Utilizando Disco Rígido no Docker

O gerenciamento de armazenamento é um aspecto crucial em ambientes Docker, permitindo persistência de dados, compartilhamento entre contêineres e otimização de desempenho. Este documento explora as diversas técnicas e melhores práticas para trabalhar com armazenamento em disco no contexto de Docker.

## Sistema de Armazenamento do Docker

### Arquitetura de Storage

O Docker utiliza uma arquitetura em camadas para gerenciar armazenamento:

```
┌─────────────────────────────┐
│     Camada de escrita       │ ← Container layer (temporária)
├─────────────────────────────┤
│     Camada da imagem 3      │ 
├─────────────────────────────┤ ← Image layers (somente leitura)
│     Camada da imagem 2      │
├─────────────────────────────┤
│     Camada da imagem 1      │
└─────────────────────────────┘
```

### Storage Drivers

O Docker suporta diferentes drivers de armazenamento, cada um com características específicas:

| Driver | Descrição | Sistemas Compatíveis |
|--------|-----------|----------------------|
| overlay2 | Recomendado para Linux moderno | Kernel Linux 4.0+ |  
| devicemapper | Funciona no nível de blocos | CentOS, RHEL |
| zfs | Oferece snapshots, compressão | Ubuntu, FreeBSD |
| btrfs | Sistema de arquivos avançado | SUSE, Fedora |
| aufs | Driver legado | Ubuntu antigos |

```bash
# Verificar driver de armazenamento atual
docker info | grep "Storage Driver"
```

## Persistência de Dados com Volumes

Os volumes Docker são o mecanismo preferencial para persistência de dados.

### Criação e Gerenciamento de Volumes

```bash
# Criar um volume
docker volume create meu_volume

# Listar volumes
docker volume ls

# Inspecionar um volume
docker volume inspect meu_volume

# Remover um volume
docker volume rm meu_volume

# Remover volumes não utilizados
docker volume prune
```

### Uso de Volumes em Contêineres

```bash
# Executar um contêiner com um volume
docker run -d --name db \
  -v meu_volume:/var/lib/mysql \
  mysql:8.0

# Especificar opções do volume
docker run -d \
  -v meu_volume:/app/data:ro \  # somente leitura
  nginx:latest
```

### Tipos de Volumes

1. **Volumes nomeados**: Gerenciados pelo Docker
   ```bash
   docker run -v meu_volume:/app/data imagem
   ```

2. **Bind mounts**: Mapeia diretórios do host para o contêiner
   ```bash
   docker run -v /caminho/host:/caminho/container imagem
   ```

3. **tmpfs mounts**: Armazenamento em memória (não persiste no disco)
   ```bash
   docker run --tmpfs /app/temp imagem
   ```

## Bind Mounts - Usando Diretório do Host

Permite mapear diretórios existentes no host para contêineres.

### Sintaxes para Bind Mounts

```bash
# Sintaxe antiga (ainda funcional)
docker run -v /host/path:/container/path imagem

# Sintaxe moderna (recomendada)
docker run --mount type=bind,source=/host/path,target=/container/path imagem
```

### Exemplos Práticos de Bind Mounts

```bash
# Mapear diretório de configuração
docker run -d --name web \
  -v $(pwd)/configs:/etc/nginx/conf.d \
  nginx:latest

# Mapear com permissões somente leitura
docker run -v /dados:/app/dados:ro imagem

# Mapear arquivo específico
docker run -v /host/config.json:/app/config.json imagem
```

## Otimização de Desempenho

### Considerações sobre Performance

1. **Localização dos dados**: Volumes locais vs. remotos
2. **Driver de armazenamento**: Impacto na velocidade de I/O
3. **Método de montagem**: Volume vs. bind mount vs. tmpfs

### Benchmark e Monitoramento

```bash
# Monitorar I/O de um contêiner
docker stats container_id

# Estatísticas detalhadas de I/O
docker stats --all --format "table {{.Name}}\t{{.BlockIO}}"

# Benchmark usando ferramenta de teste
docker run --rm \
  -v test_volume:/test \
  busybox dd if=/dev/zero of=/test/testfile bs=1M count=100
```

## Docker Storage e Docker Compose

### Definindo Volumes no Docker Compose

```yaml
version: '3'
services:
  db:
    image: postgres:13
    volumes:
      - postgres_data:/var/lib/postgresql/data
      - ./init-scripts:/docker-entrypoint-initdb.d
  web:
    build: .
    volumes:
      - ./src:/app
      - /app/node_modules

volumes:
  postgres_data:
    driver: local
    driver_opts:
      type: 'none'
      o: 'bind'
      device: '/data/postgres'
```

## Gerenciamento Avançado de Volume

### Volume Drivers e Plugins

```bash
# Criar volume com driver específico
docker volume create --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.10,rw \
  --opt device=:/path/to/dir \
  nfs_volume
```

### Volumes Compartilhados entre Contêineres

```bash
# Criar volume compartilhado
docker volume create shared_data

# Contêiner 1 - Escreve dados
docker run -it --name writer \
  -v shared_data:/shared \
  ubuntu bash -c "echo 'Teste' > /shared/test.txt"

# Contêiner 2 - Lê dados
docker run -it --name reader \
  -v shared_data:/shared \
  ubuntu bash -c "cat /shared/test.txt"
```

## Backup e Restauração

### Backup de Volumes

```bash
# Backup usando um contêiner temporário
docker run --rm \
  -v meu_volume:/source:ro \
  -v $(pwd):/backup \
  ubuntu tar czf /backup/volume_backup.tar.gz -C /source .
```

### Restauração de Volumes

```bash
# Restaurar usando um contêiner temporário
docker run --rm \
  -v meu_volume:/target \
  -v $(pwd):/backup \
  ubuntu bash -c "tar xzf /backup/volume_backup.tar.gz -C /target"
```

## Gerenciamento de Espaço em Disco

### Limpeza de Recursos Docker

```bash
# Remover contêineres parados
docker container prune

# Remover imagens não utilizadas
docker image prune

# Remover volumes não utilizados
docker volume prune

# Limpeza completa (cuidado!)
docker system prune -a --volumes
```

### Monitorando Uso de Disco

```bash
# Verificar espaço usado pelo Docker
docker system df

# Informações detalhadas
docker system df -v

# Detalhes de um volume específico
du -sh $(docker volume inspect meu_volume --format '{{ .Mountpoint }}')
```

## Soluções para Armazenamento em Produção

### Volume Plugins para Armazenamento Distribuído

1. **Rexray**: Integração com sistemas de armazenamento empresarial
2. **Flocker**: Migração de volumes entre hosts
3. **Portworx**: Armazenamento definido por software para Kubernetes
4. **NFS**: Compartilhamento em rede via plugin

```bash
# Instalar plugin de volume
docker plugin install rexray/ebs

# Criar volume usando plugin
docker volume create --driver rexray/ebs my_ebs_volume
```

## Limitações e Controle de Recursos

### Limitar Uso de Disco

```bash
# Limitar armazenamento para um contêiner
docker run -d --name limited_storage \
  --storage-opt size=10G \
  ubuntu:latest
```

### Verificar Quotas

```bash
# Verificar limites de disco (depende do storage driver)
docker inspect limited_storage --format '{{.HostConfig.StorageOpt}}'
```

## Melhores Práticas

1. **Use volumes para dados persistentes**: Evite armazenar dados na camada de escrita do contêiner
2. **Planeje sua estratégia de backup**: Volumes Docker podem ser perdidos
3. **Defina políticas de limpeza**: Evite acúmulo de dados não utilizados
4. **Monitore o uso de disco**: Problemas de espaço podem derrubar aplicações
5. **Documente sua arquitetura de armazenamento**: Facilita manutenção e migração

## Solução de Problemas Comuns

### Problemas de Permissão

```bash
# Corrigir problemas de permissão em bind mounts
docker run -v /host/path:/container/path:z imagem  # SELinux

# Definir proprietário explicitamente
docker run -u $(id -u):$(id -g) -v /host/path:/container/path imagem
```

### Volume não Encontrado

```bash
# Verificar se o volume existe
docker volume ls | grep meu_volume

# Verificar caminho do volume
docker volume inspect meu_volume

# Criar volume caso não exista
docker volume create meu_volume
```

### Dados Inacessíveis

```bash
# Verificar permissões no contêiner
docker exec -it container ls -la /path/to/data

# Verificar montagem do volume
docker inspect container | grep -A 10 Mounts
```

## Referência Rápida

| Comando | Descrição |
|---------|-----------|
| `docker volume create` | Cria um novo volume |
| `docker volume ls` | Lista todos os volumes |
| `docker volume inspect` | Exibe detalhes de um volume |
| `docker volume rm` | Remove um volume |
| `docker volume prune` | Remove volumes não utilizados |
| `docker system df` | Mostra uso de espaço pelo Docker |
| `-v` ou `--volume` | Flag para definir volumes em contêineres |
| `--mount` | Flag alternativa com sintaxe mais explícita |