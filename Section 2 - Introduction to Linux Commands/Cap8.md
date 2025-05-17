# Comandos Relacionados com Processos no Docker (Ubuntu)

Gerenciar processos em ambientes Docker é fundamental para monitoramento, depuração e otimização de aplicações containerizadas. Este documento apresenta os principais comandos e técnicas para trabalhar com processos em Docker, com foco específico no ambiente Ubuntu.

## Processos no Host vs. Processos em Contêineres

Os processos em contêineres Docker são isolados, mas ainda são processos do kernel Linux no host:

```
┌─────────────────────────────────────┐
│ Host Ubuntu                         │
│  ┌───────────────┐  ┌───────────────┐│
│  │ Contêiner A   │  │ Contêiner B   ││
│  │ PID 1         │  │ PID 1         ││
│  │ PID 2         │  │ PID 2         ││
│  │ PID 3         │  │ PID 3         ││
│  └───────────────┘  └───────────────┘│
│                                      │
│ PID namespace do host                │
│ PID 1, PID 2, ... PID 389, PID 390   │
└─────────────────────────────────────┘
```

## Monitoramento de Processos em Contêineres

### `docker top`

O comando `docker top` permite visualizar processos em execução dentro de um contêiner:

```bash
# Visualizar processos de um contêiner
docker top nome_do_container

# Visualizar com formato personalizado (compatível com ps)
docker top nome_do_container aux

# Visualizar com colunas específicas
docker top nome_do_container -o pid,user,cmd
```

Exemplo de saída:
```
UID    PID    PPID   C   STIME   TTY   TIME       CMD
root   4521   4503   0   14:51   ?     00:00:00   nginx: master process nginx -g daemon off;
101    4556   4521   0   14:51   ?     00:00:00   nginx: worker process
```

### `docker stats`

O comando `docker stats` fornece estatísticas em tempo real sobre o uso de recursos:

```bash
# Estatísticas de todos os contêineres
docker stats

# Estatísticas de contêineres específicos
docker stats container1 container2

# Saída única (não contínua)
docker stats --no-stream

# Formato personalizado
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}"
```

### `docker inspect`

Obter informações detalhadas sobre processos e configurações:

```bash
# Ver PID do processo principal no host
docker inspect --format '{{.State.Pid}}' nome_do_container

# Ver se o contêiner está em execução
docker inspect --format '{{.State.Running}}' nome_do_container

# Ver limites de recursos configurados
docker inspect --format '{{.HostConfig.Resources}}' nome_do_container
```

## Processos Dentro do Contêiner

### Acessando o Contêiner

```bash
# Obter shell no contêiner
docker exec -it nome_do_container bash

# Executar comando único
docker exec nome_do_container ps aux

# Executar como usuário específico
docker exec -u usuario nome_do_container ps aux
```

### Comandos de Processo no Ubuntu

Dentro de um contêiner Ubuntu, você pode usar os comandos padrão para gerenciamento de processos:

#### `ps` - Process Status

```bash
# Listar todos os processos no contêiner
docker exec nome_do_container ps aux

# Listar processos com hierarquia (árvore)
docker exec nome_do_container ps auxf

# Listar processos por usuário
docker exec nome_do_container ps -u root

# Ordenar por uso de memória
docker exec nome_do_container ps aux --sort=-%mem
```

#### `top` - Monitoramento Interativo

```bash
# Iniciar top dentro do contêiner
docker exec -it nome_do_container top

# Versão com cores e recursos adicionais (se disponível)
docker exec -it nome_do_container htop
```

#### `pgrep` e `pkill` - Localizar e Gerenciar Processos por Nome

```bash
# Encontrar PIDs de processos por nome
docker exec nome_do_container pgrep nginx

# Enviar sinal para processos com nome específico
docker exec nome_do_container pkill -HUP nginx
```

#### `kill` - Enviar Sinais para Processos

```bash
# Terminar um processo
docker exec nome_do_container kill 123

# Enviar sinal específico
docker exec nome_do_container kill -SIGTERM 123

# Forçar encerramento
docker exec nome_do_container kill -9 123
```

#### `killall` - Terminar Processos por Nome

```bash
# Terminar todos os processos com determinado nome
docker exec nome_do_container killall nginx

# Terminar com sinal específico
docker exec nome_do_container killall -HUP nginx
```

#### `pidof` - Encontrar PID por Nome do Processo

```bash
# Obter PID do processo pelo nome
docker exec nome_do_container pidof apache2
```

#### `lsof` - Listar Arquivos Abertos

```bash
# Mostrar arquivos abertos por processos
docker exec nome_do_container lsof

# Mostrar portas em uso
docker exec nome_do_container lsof -i

# Mostrar processos usando um arquivo específico
docker exec nome_do_container lsof /var/log/nginx/access.log
```

## Gerenciamento de Recursos e Limites

### Limitação de Recursos na Criação

```bash
# Limitar CPU e memória
docker run -d --name api_limitada \
  --cpus 0.5 \
  --memory 512m \
  --memory-swap 1g \
  imagem_api

# Definir prioridade relativa de CPU
docker run -d --cpu-shares 512 imagem
```

### Atualização de Limites em Contêineres em Execução

```bash
# Atualizar limites de memória
docker update --memory 1G --memory-swap 2G nome_do_container

# Atualizar limites de CPU
docker update --cpus 1.5 nome_do_container
```

### Visualizando Uso de Recursos

```bash
# Ver limites e uso de CPU
docker stats --format "table {{.Name}}\t{{.CPUPerc}}\t{{.MemPerc}}"

# Verificar configurações de limite
docker inspect --format='{{.HostConfig.Resources.Memory}}' nome_do_container
```

## Gerenciamento do Processo Principal (PID 1)

### Importância do PID 1 em Docker

O processo com PID 1 em um contêiner Docker tem responsabilidades especiais:
- Gerenciar sinais do sistema
- Realizar limpeza de processos zumbis
- Determinar o ciclo de vida do contêiner

```bash
# Verificar qual é o processo PID 1 no contêiner
docker exec nome_do_container ps -p 1
```

### Usando Init Systems

```bash
# Usar tini como init system (recomendado para contêineres Ubuntu)
docker run --init -d imagem

# Alternativa: instalar dumb-init no contêiner
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y dumb-init
ENTRYPOINT ["dumb-init", "--"]
CMD ["seu-comando"]
```

## Ciclo de Vida de Processos em Docker

### Sinais e Gerenciamento de Interrupções

```bash
# Parar contêiner enviando SIGTERM (grace period) seguido de SIGKILL
docker stop nome_do_container

# Parar imediatamente (SIGKILL)
docker kill nome_do_container

# Enviar sinal específico
docker kill --signal=SIGHUP nome_do_container
```

### Comportamento de Reinicialização

```bash
# Definir política de reinicialização
docker run -d --restart=on-failure:5 imagem

# Verificar política atual
docker inspect --format='{{.HostConfig.RestartPolicy}}' nome_do_container

# Atualizar política de reinicialização
docker update --restart=always nome_do_container
```

## Processos em Contêineres Multi-serviço

### Supervisor para Gerenciar Múltiplos Processos (Ubuntu)

```bash
# Instalar supervisor em um contêiner Ubuntu
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y supervisor
COPY supervisord.conf /etc/supervisor/conf.d/
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/supervisord.conf"]
```

Exemplo de configuração (`supervisord.conf`):
```ini
[supervisord]
nodaemon=true

[program:nginx]
command=nginx -g "daemon off;"
autostart=true
autorestart=true
stdout_logfile=/dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile=/dev/stderr
stderr_logfile_maxbytes=0

[program:php-fpm]
command=php-fpm7.4 -F
autostart=true
autorestart=true
stdout_logfile=/dev/stdout
stdout_logfile_maxbytes=0
stderr_logfile=/dev/stderr
stderr_logfile_maxbytes=0
```

## Depuração de Processos

### Inspecionar Logs de Processos

```bash
# Ver logs do contêiner
docker logs nome_do_container

# Acompanhar logs em tempo real
docker logs -f nome_do_container

# Ver logs com timestamps
docker logs --timestamps nome_do_container

# Ver logs das últimas N linhas
docker logs --tail 100 nome_do_container
```

### Depuração com `strace`

```bash
# Instalar strace em um contêiner Ubuntu
docker exec -it nome_do_container apt update && apt install -y strace

# Monitorar chamadas de sistema de um processo
docker exec -it nome_do_container strace -p 123

# Monitorar chamadas de sistema de um novo processo
docker exec -it nome_do_container strace comando
```

## Segurança e Isolamento de Processos

### Executar como Usuário Não-Root

```bash
# Executar contêiner como usuário específico
docker run -d --user 1000:1000 imagem

# Criar usuário específico no Dockerfile
FROM ubuntu:22.04
RUN useradd -m -s /bin/bash appuser
USER appuser
```

### Capacidades Linux (Capabilities)

```bash
# Remover capacidades específicas
docker run --cap-drop=NET_ADMIN --cap-drop=SYS_ADMIN imagem

# Adicionar apenas capacidades necessárias
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE imagem

# Executar com privilégios completos (evitar em produção)
docker run --privileged imagem
```

## Ferramentas Avançadas para Ubuntu

### `sysdig` para Análise Profunda

```bash
# Instalar no host Ubuntu
sudo apt-get update && sudo apt-get install -y sysdig

# Monitorar todos os contêineres
sudo sysdig container.id != host

# Monitorar contêiner específico
sudo sysdig -c spy_users container.name=nome_do_container
```

### `cAdvisor` para Monitoramento

```bash
# Executar cAdvisor no Ubuntu host
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:ro \
  --volume=/sys:/sys:ro \
  --volume=/var/lib/docker/:/var/lib/docker:ro \
  --publish=8080:8080 \
  --detach=true \
  --name=cadvisor \
  gcr.io/cadvisor/cadvisor:latest
```

## Docker Compose e Processos

### Gestão de Serviços com Docker Compose

```yaml
version: '3'
services:
  webapp:
    image: ubuntu:22.04
    command: ["bash", "-c", "while true; do echo 'Running'; sleep 10; done"]
    restart: unless-stopped
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
```

### Escalabilidade com Docker Compose

```bash
# Escalar serviço para múltiplas instâncias
docker-compose up -d --scale webapp=3

# Verificar processos em todas as instâncias
docker-compose top
```

## Comandos Específicos do Ubuntu para Diagnóstico

### Instalação de Ferramentas de Diagnóstico

```bash
# Instalar conjunto de ferramentas em contêiner Ubuntu
docker exec -it nome_do_container apt update
docker exec -it nome_do_container apt install -y \
  procps \
  htop \
  lsof \
  net-tools \
  iputils-ping \
  iproute2 \
  strace
```

### `pstree` - Visualizar Árvore de Processos

```bash
# Instalar pstree
docker exec -it nome_do_container apt install -y psmisc

# Visualizar árvore de processos
docker exec -it nome_do_container pstree
```

### `fuser` - Identificar Processos Usando Recursos

```bash
# Instalar psmisc
docker exec -it nome_do_container apt install -y psmisc

# Identificar processos usando uma porta
docker exec -it nome_do_container fuser 80/tcp
```

## Melhores Práticas para Processos em Docker (Ubuntu)

1. **Um processo por contêiner**: Mantenha a filosofia Docker de um processo principal por contêiner
2. **Utilize um sistema init adequado**: Use `--init`, dumb-init ou tini para gerenciar o PID 1
3. **Projetado para terminação limpa**: Garanta que seu aplicativo trate sinais SIGTERM adequadamente
4. **Usuários não-privilegiados**: Execute processos como usuários não-root sempre que possível
5. **Log para stdout/stderr**: Redirecione logs para a saída padrão para captura pelo Docker
6. **Monitore regularmente**: Use `docker stats` e ferramentas como cAdvisor para monitoramento
7. **Limite recursos**: Configure limites de CPU e memória para evitar problemas de contenção
8. **Use healthchecks**: Configure verificações de saúde para ajudar o Docker a gerenciar o ciclo de vida

## Resolução de Problemas Comuns

### Processos Zumbis

```bash
# Verificar processos zumbis
docker exec nome_do_container ps aux | grep Z

# Solução: Use um sistema init como tini
docker run --init imagem
```

### Processos Órfãos

```bash
# Identificar processos sem pai
docker exec nome_do_container ps -elf | awk '$5=="1"'
```

### Processos Consumindo Muita Memória

```bash
# Identificar processos por uso de memória
docker exec nome_do_container ps aux --sort=-%mem | head -5

# Verificar detalhes de memória de um PID específico
docker exec nome_do_container cat /proc/123/status | grep -i mem
```

### Falhas de OOM Killer

```bash
# Verificar logs do kernel para eventos OOM
dmesg | grep -i "out of memory"

# Verificar logs do contêiner
docker logs nome_do_container | grep -i "killed"
```

## Referência Rápida para Ubuntu

| Comando | Descrição |
|---------|-----------|
| `ps aux` | Listar todos os processos |
| `top` | Monitor de processos interativo |
| `htop` | Versão melhorada do top |
| `pgrep nome` | Encontrar PID por nome de processo |
| `pkill nome` | Enviar sinal para processos por nome |
| `kill -TERM 123` | Enviar sinal TERM para PID 123 |
| `killall nome` | Terminar todos os processos com nome |
| `nice -n 10 comando` | Executar com prioridade alterada |
| `renice 10 -p 123` | Alterar prioridade de processo existente |
| `lsof -p 123` | Listar arquivos abertos pelo processo |
| `fuser -v 80/tcp` | Ver processos usando a porta 80 |
| `strace -p 123` | Rastrear chamadas de sistema |
| `cat /proc/123/limits` | Ver limites do processo |
| `cat /proc/123/status` | Ver status do processo |