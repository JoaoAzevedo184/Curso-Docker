# Primeiro Servidor Web with Docker

## Introdução

Nesta aula, vamos criar nosso primeiro servidor web usando Docker com o Nginx. Este exercício prático demonstra conceitos fundamentais como execução em segundo plano, mapeamento de portas, nomenclatura de contêineres, volumes e monitoramento através de logs.

## O Comando Completo

```bash
docker run -d -p 8080:80 --name meu-nginx -v /home/usuario/docker/teste_docker:/usr/share/nginx/html nginx:latest
```

## Anatomia do Comando

Vamos quebrar este comando em partes para entender cada componente:

### `docker run`
Comando base para criar e executar um novo contêiner.

### `-d` (--detach)
**Execução em segundo plano (daemon mode)**
- O contêiner roda em background
- Libera o terminal para outros comandos
- Retorna o ID do contêiner criado
- Sem este parâmetro, o terminal ficaria "preso" mostrando os logs

### `-p 8080:80` (--publish)
**Mapeamento de portas**
- Formato: `porta_host:porta_container`
- `8080`: Porta no seu computador (host)
- `80`: Porta dentro do contêiner onde o Nginx está rodando
- Permite acessar o servidor via `http://localhost:8080`

### `--name meu-nginx`
**Nomenclatura do contêiner**
- Define um nome amigável para o contêiner
- Facilita referenciá-lo em outros comandos
- Sem este parâmetro, Docker gera um nome aleatório
- Exemplo de uso posterior: `docker stop meu-nginx`

### `-v /home/usuario/docker/teste_docker:/usr/share/nginx/html`
**Volume Mount (Bind Mount)**
- Formato: `caminho_host:caminho_container`
- `/home/usuario/docker/teste_docker`: Diretório no seu sistema
- `/usr/share/nginx/html`: Diretório padrão do Nginx para arquivos web
- Permite editar arquivos no host e ver mudanças no contêiner imediatamente

### `nginx:latest`
**Imagem e tag**
- `nginx`: Nome da imagem oficial do Nginx
- `latest`: Tag que indica a versão mais recente
- Poderia ser uma versão específica como `nginx:1.21`

## Preparando o Ambiente

### 1. Criar a estrutura de diretórios

```bash
# Criar o diretório para os arquivos web
mkdir -p /home/usuario/docker/teste_docker

# Navegar para o diretório
cd /home/usuario/docker/teste_docker
```

### 2. Criar um arquivo HTML simples

```bash
# Criar um arquivo index.html
cat > index.html << EOF
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Meu Primeiro Servidor Docker</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 50px auto;
            padding: 20px;
            background-color: #f4f4f4;
        }
        .container {
            background-color: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        h1 { color: #333; }
        .success { color: #28a745; }
    </style>
</head>
<body>
    <div class="container">
        <h1>🐳 Servidor Web Docker</h1>
        <p class="success">✅ Parabéns! Seu servidor Nginx está funcionando perfeitamente!</p>
        <p>Este arquivo está sendo servido de: <code>/home/usuario/docker/teste_docker</code></p>
        <p>Mapeado para: <code>/usr/share/nginx/html</code> dentro do contêiner</p>
        <hr>
        <small>Powered by Docker + Nginx</small>
    </div>
</body>
</html>
EOF
```

## Executando o Servidor

### 1. Executar o comando

```bash
docker run -d -p 8080:80 --name meu-nginx -v /home/usuario/docker/teste_docker:/usr/share/nginx/html nginx:latest
```

### 2. Verificar se o contêiner está rodando

```bash
docker ps
```

Saída esperada:
```
CONTAINER ID   IMAGE          COMMAND                  CREATED         STATUS         PORTS                  NAMES
abc123def456   nginx:latest   "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   0.0.0.0:8080->80/tcp   meu-nginx
```

### 3. Testar o servidor

- **Navegador**: Acesse `http://localhost:8080`
- **Terminal**: `curl http://localhost:8080`

## Conceitos de Logs

Os logs são fundamentais para monitorar, debugar e entender o comportamento dos seus contêineres Docker.

### Visualizando Logs

#### Comando básico
```bash
docker logs meu-nginx
```

#### Logs em tempo real (follow)
```bash
docker logs -f meu-nginx
```

#### Logs com timestamp
```bash
docker logs -t meu-nginx
```

#### Últimas N linhas
```bash
docker logs --tail 50 meu-nginx
```

#### Logs desde um tempo específico
```bash
# Últimos 10 minutos
docker logs --since 10m meu-nginx

# Desde uma data específica
docker logs --since 2024-01-01T00:00:00 meu-nginx
```

### Tipos de Logs do Nginx

#### 1. Access Logs
Registram todas as requisições HTTP:
```
172.17.0.1 - - [21/May/2025:10:30:45 +0000] "GET / HTTP/1.1" 200 612 "-" "Mozilla/5.0..."
```

#### 2. Error Logs
Registram erros e problemas:
```
2025/05/21 10:30:45 [error] 29#29: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory)
```

### Gerando Logs para Teste

```bash
# Fazer várias requisições para gerar logs
for i in {1..5}; do
    curl http://localhost:8080
    sleep 1
done

# Gerar um erro 404
curl http://localhost:8080/pagina-inexistente

# Visualizar os logs gerados
docker logs --tail 10 meu-nginx
```

### Configuração Avançada de Logs

#### Logging Drivers
Docker suporta diferentes drivers de log:

```bash
# Usar driver json-file com limite de tamanho
docker run -d \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  -p 8080:80 \
  --name meu-nginx \
  -v /home/usuario/docker/teste_docker:/usr/share/nginx/html \
  nginx:latest
```

## Comandos Úteis de Gerenciamento

### Controle do contêiner
```bash
# Parar o contêiner
docker stop meu-nginx

# Iniciar novamente
docker start meu-nginx

# Reiniciar
docker restart meu-nginx

# Remover o contêiner (deve estar parado)
docker rm meu-nginx

# Forçar remoção (mesmo que esteja rodando)
docker rm -f meu-nginx
```

### Inspeção do contêiner
```bash
# Informações detalhadas
docker inspect meu-nginx

# Estatísticas em tempo real
docker stats meu-nginx

# Processos rodando no contêiner
docker top meu-nginx
```

### Acesso ao contêiner
```bash
# Executar bash dentro do contêiner
docker exec -it meu-nginx bash

# Executar um comando específico
docker exec meu-nginx ls -la /usr/share/nginx/html
```

## Modificando o Conteúdo

Como configuramos um volume, você pode modificar o conteúdo web diretamente:

```bash
# Adicionar uma nova página
echo "<h1>Nova Página</h1><p>Criada em $(date)</p>" > /home/usuario/docker/teste_docker/nova-pagina.html

# Acessar a nova página
curl http://localhost:8080/nova-pagina.html
```

## Troubleshooting Comum

### Problema: Porta já em uso
```bash
# Erro: bind: address already in use
# Solução: Usar uma porta diferente
docker run -d -p 8081:80 --name meu-nginx2 nginx:latest
```

### Problema: Nome já existe
```bash
# Erro: name is already in use
# Solução: Remover o contêiner existente ou usar outro nome
docker rm meu-nginx
```

### Problema: Volume não funciona
```bash
# Verificar se o caminho existe
ls -la /home/usuario/docker/teste_docker

# Verificar permissões
chmod 755 /home/usuario/docker/teste_docker
```

## Melhores Práticas

1. **Sempre usar volumes** para dados que precisam persistir
2. **Monitorar logs regularmente** para identificar problemas
3. **Usar nomes descritivos** para contêineres
4. **Configurar limites de logs** para evitar uso excessivo de disco
5. **Parar contêineres** quando não estão em uso

## Conclusão

Este exercício demonstrou como criar um servidor web funcional com Docker, cobrindo conceitos essenciais como:
- Execução em background
- Mapeamento de portas
- Nomenclatura de contêineres
- Volumes para persistência
- Monitoramento através de logs

Estes fundamentos são a base para aplicações mais complexas que desenvolveremos nas próximas aulas. O entendimento de logs é particularmente importante para diagnosticar problemas e monitorar aplicações em produção.
