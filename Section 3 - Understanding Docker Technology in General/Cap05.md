# Executando Containers Persistentes no Docker

## Introdução

Um container persistente é aquele que mantém seus dados e configurações mesmo após ser parado ou reiniciado. Por padrão, os containers Docker são efêmeros - quando são removidos, todos os dados são perdidos. Para criar containers verdadeiramente persistentes, precisamos implementar estratégias de persistência de dados usando volumes, bind mounts e outras técnicas.

## Conceitos Fundamentais

### Container Efêmero vs Persistente

**Container Efêmero:**
- Dados são perdidos quando o container é removido
- Adequado para aplicações stateless
- Fácil de recriar e substituir

**Container Persistente:**
- Dados são mantidos entre execuções
- Essencial para bancos de dados, aplicações web com uploads
- Requer configuração adicional de volumes

### Estratégias de Persistência

1. **Volumes Docker**: Gerenciados pelo Docker Engine
2. **Bind Mounts**: Mapeamento direto para diretórios do host
3. **tmpfs Mounts**: Armazenamento temporário na memória RAM
4. **Named Volumes**: Volumes com nomes específicos para fácil gerenciamento

## Volumes Docker

### Criando e Gerenciando Volumes

```bash
# Criar um volume nomeado
docker volume create meu-volume

# Listar volumes existentes
docker volume ls

# Inspecionar detalhes de um volume
docker volume inspect meu-volume

# Remover um volume (cuidado: dados serão perdidos)
docker volume rm meu-volume

# Remover volumes não utilizados
docker volume prune
```

### Executando Containers com Volumes

```bash
# Executar container com volume nomeado
docker run -d \
  --name meu-app \
  -v meu-volume:/app/data \
  nginx:alpine

# Executar com múltiplos volumes
docker run -d \
  --name web-server \
  -v config-vol:/etc/nginx/conf.d \
  -v data-vol:/usr/share/nginx/html \
  -v logs-vol:/var/log/nginx \
  -p 8080:80 \
  nginx:alpine

# Volume anônimo (Docker cria automaticamente)
docker run -d \
  --name temp-app \
  -v /app/data \
  alpine:latest sleep 3600
```

### Exemplo Prático: Banco de Dados MySQL Persistente

```bash
# Criar volume para dados do MySQL
docker volume create mysql-data

# Executar MySQL com dados persistentes
docker run -d \
  --name mysql-server \
  -e MYSQL_ROOT_PASSWORD=senha_segura \
  -e MYSQL_DATABASE=minha_aplicacao \
  -e MYSQL_USER=usuario \
  -e MYSQL_PASSWORD=senha_usuario \
  -v mysql-data:/var/lib/mysql \
  -p 3306:3306 \
  mysql:8.0

# Verificar se o container está rodando
docker ps

# Conectar ao MySQL (em outro terminal)
docker exec -it mysql-server mysql -u root -p

# Parar e reiniciar - dados permanecem
docker stop mysql-server
docker start mysql-server
```

## Bind Mounts

### Conceito e Uso

Bind mounts mapeiam diretórios ou arquivos específicos do host para o container.

```bash
# Sintaxe básica
docker run -v /caminho/host:/caminho/container imagem

# No Windows (PowerShell)
docker run -v ${PWD}:/app alpine:latest

# No Linux/macOS
docker run -v $(pwd):/app alpine:latest

# Especificar permissões (read-only)
docker run -v /host/path:/container/path:ro alpine:latest

# Especificar permissões (read-write - padrão)
docker run -v /host/path:/container/path:rw alpine:latest
```

### Exemplo Prático: Desenvolvimento Web

```bash
# Estrutura do projeto
mkdir meu-projeto-web
cd meu-projeto-web
echo "<h1>Minha Aplicação</h1>" > index.html

# Executar servidor web com bind mount
docker run -d \
  --name dev-server \
  -v ${PWD}:/usr/share/nginx/html:ro \
  -p 8080:80 \
  nginx:alpine

# Agora você pode editar arquivos localmente e ver mudanças imediatamente
echo "<h1>Aplicação Atualizada</h1>" > index.html
```

### Exemplo: Ambiente de Desenvolvimento Node.js

```bash
# Criar projeto Node.js
mkdir node-app
cd node-app

# Criar package.json
cat > package.json << EOF
{
  "name": "minha-app",
  "version": "1.0.0",
  "main": "app.js",
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js"
  },
  "dependencies": {
    "express": "^4.18.0"
  },
  "devDependencies": {
    "nodemon": "^2.0.0"
  }
}
EOF

# Criar aplicação simples
cat > app.js << EOF
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello Docker!');
});

app.listen(port, '0.0.0.0', () => {
  console.log(\`App rodando em http://localhost:\${port}\`);
});
EOF

# Executar container de desenvolvimento
docker run -d \
  --name node-dev \
  -v ${PWD}:/usr/src/app \
  -w /usr/src/app \
  -p 3000:3000 \
  node:18-alpine \
  sh -c "npm install && npm run dev"
```

## Containers com Restart Policy

### Políticas de Reinicialização

Para garantir que containers persistentes sejam reiniciados automaticamente:

```bash
# Sempre reiniciar
docker run -d --restart always \
  --name app-sempre-ativo \
  -v app-data:/data \
  alpine:latest sleep infinity

# Reiniciar apenas se parar com erro
docker run -d --restart on-failure \
  --name app-reinicio-erro \
  -v app-data:/data \
  alpine:latest

# Reiniciar com limite de tentativas
docker run -d --restart on-failure:3 \
  --name app-limite-reinicio \
  -v app-data:/data \
  alpine:latest

# Reiniciar a menos que seja parado manualmente
docker run -d --restart unless-stopped \
  --name app-persistente \
  -v app-data:/data \
  alpine:latest sleep infinity
```

### Exemplo: Aplicação Web Persistente Completa

```bash
# Criar volumes necessários
docker volume create app-data
docker volume create app-logs
docker volume create app-config

# Executar aplicação com alta disponibilidade
docker run -d \
  --name webapp-producao \
  --restart unless-stopped \
  -v app-data:/app/data \
  -v app-logs:/app/logs \
  -v app-config:/app/config \
  -e ENV=production \
  -p 80:8080 \
  --health-cmd="curl -f http://localhost:8080/health || exit 1" \
  --health-interval=30s \
  --health-timeout=10s \
  --health-retries=3 \
  minha-webapp:latest
```

## Docker Compose para Containers Persistentes

### Arquivo docker-compose.yml Básico

```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine
    container_name: web-server
    restart: unless-stopped
    ports:
      - "80:80"
    volumes:
      - ./site:/usr/share/nginx/html:ro
      - web-logs:/var/log/nginx
    depends_on:
      - db

  db:
    image: mysql:8.0
    container_name: mysql-db
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: senha_root
      MYSQL_DATABASE: aplicacao
      MYSQL_USER: app_user
      MYSQL_PASSWORD: app_password
    volumes:
      - mysql-data:/var/lib/mysql
      - ./backup:/backup
    ports:
      - "3306:3306"

  redis:
    image: redis:alpine
    container_name: redis-cache
    restart: unless-stopped
    volumes:
      - redis-data:/data
    ports:
      - "6379:6379"

volumes:
  mysql-data:
    driver: local
  redis-data:
    driver: local
  web-logs:
    driver: local

networks:
  default:
    name: app-network
```

### Comandos Docker Compose para Persistência

```bash
# Inicializar serviços
docker-compose up -d

# Parar serviços (mantém volumes)
docker-compose stop

# Parar e remover containers (mantém volumes)
docker-compose down

# Parar, remover containers E volumes (CUIDADO!)
docker-compose down -v

# Reiniciar serviços específicos
docker-compose restart web

# Ver status dos serviços
docker-compose ps

# Ver logs persistentes
docker-compose logs db
```

## Estratégias Avançadas de Persistência

### Backup e Restauração de Volumes

```bash
# Criar backup de um volume
docker run --rm \
  -v mysql-data:/data \
  -v ${PWD}:/backup \
  alpine:latest \
  tar czf /backup/mysql-backup-$(date +%Y%m%d_%H%M%S).tar.gz -C /data .

# Restaurar backup para um volume
docker run --rm \
  -v mysql-data:/data \
  -v ${PWD}:/backup \
  alpine:latest \
  tar xzf /backup/mysql-backup-20231201_120000.tar.gz -C /data

# Script automatizado de backup
#!/bin/bash
BACKUP_DIR="/backups/docker"
DATE=$(date +%Y%m%d_%H%M%S)

# Criar diretório de backup
mkdir -p "$BACKUP_DIR"

# Backup do volume MySQL
docker run --rm \
  -v mysql-data:/data:ro \
  -v "$BACKUP_DIR":/backup \
  alpine:latest \
  tar czf "/backup/mysql-$DATE.tar.gz" -C /data .

# Manter apenas últimos 7 backups
find "$BACKUP_DIR" -name "mysql-*.tar.gz" -mtime +7 -delete
```

### Sincronização entre Ambientes

```bash
# Exportar volume para arquivo
docker run --rm \
  -v source-volume:/data:ro \
  -v ${PWD}:/backup \
  alpine:latest \
  tar czf /backup/volume-export.tar.gz -C /data .

# Importar volume de arquivo (em outro ambiente)
docker volume create target-volume
docker run --rm \
  -v target-volume:/data \
  -v ${PWD}:/backup \
  alpine:latest \
  tar xzf /backup/volume-export.tar.gz -C /data
```

## Monitoramento de Containers Persistentes

### Verificação de Saúde

```bash
# Container com health check
docker run -d \
  --name app-monitorada \
  --restart unless-stopped \
  -v app-data:/data \
  --health-cmd="curl -f http://localhost:8080/health || exit 1" \
  --health-interval=30s \
  --health-timeout=10s \
  --health-retries=3 \
  --health-start-period=30s \
  minha-app:latest

# Verificar status de saúde
docker inspect --format='{{.State.Health.Status}}' app-monitorada
```

### Monitoramento de Recursos

```bash
# Monitorar uso de recursos
docker stats app-monitorada

# Definir limites de recursos
docker run -d \
  --name app-limitada \
  --restart unless-stopped \
  --memory="512m" \
  --cpus="0.5" \
  -v app-data:/data \
  minha-app:latest
```

### Logs Persistentes

```bash
# Configurar logging driver
docker run -d \
  --name app-logs \
  --restart unless-stopped \
  --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  -v app-data:/data \
  minha-app:latest

# Rotação de logs com logrotate
# /etc/logrotate.d/docker-containers
/var/lib/docker/containers/*/*.log {
    daily
    missingok
    rotate 7
    compress
    notifempty
    create 0644 root root
    postrotate
        docker kill --signal=USR1 $(docker ps -q) 2>/dev/null || true
    endscript
}
```

## Boas Práticas para Containers Persistentes

### 1. Separação de Dados e Aplicação

```bash
# ❌ Não faça isso
docker run -d myapp:latest

# ✅ Faça isso
docker run -d \
  -v app-data:/app/data \
  -v app-config:/app/config \
  myapp:latest
```

### 2. Use Volumes Nomeados para Produção

```bash
# ❌ Volume anônimo
docker run -v /data myapp:latest

# ✅ Volume nomeado
docker run -v app-data:/data myapp:latest
```

### 3. Implemente Health Checks

```dockerfile
# No Dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8080/health || exit 1
```

```bash
# Ou no comando docker run
docker run -d \
  --health-cmd="curl -f http://localhost:8080/health || exit 1" \
  --health-interval=30s \
  myapp:latest
```

### 4. Configure Restart Policies Apropriadas

```bash
# Para produção
docker run -d --restart unless-stopped myapp:latest

# Para desenvolvimento  
docker run -d --restart on-failure myapp:latest
```

### 5. Monitore e Faça Backup Regularmente

```bash
# Script de monitoramento
#!/bin/bash
CONTAINER_NAME="meu-app"

if ! docker ps | grep -q $CONTAINER_NAME; then
    echo "Container $CONTAINER_NAME não está rodando!"
    docker start $CONTAINER_NAME
fi

# Verificar saúde
HEALTH=$(docker inspect --format='{{.State.Health.Status}}' $CONTAINER_NAME)
if [ "$HEALTH" != "healthy" ]; then
    echo "Container $CONTAINER_NAME não está saudável: $HEALTH"
fi
```

## Exemplo Completo: Sistema Blog Persistente

### Estrutura do Projeto

```
blog-persistente/
├── docker-compose.yml
├── nginx/
│   └── nginx.conf
├── wordpress/
│   └── wp-content/
└── scripts/
    ├── backup.sh
    └── restore.sh
```

### docker-compose.yml Completo

```yaml
version: '3.8'

services:
  nginx:
    image: nginx:alpine
    container_name: blog-nginx
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - wordpress-data:/var/www/html:ro
      - nginx-logs:/var/log/nginx
    depends_on:
      - wordpress
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  wordpress:
    image: wordpress:php8.1-fpm
    container_name: blog-wordpress
    restart: unless-stopped
    environment:
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_NAME: blog
      WORDPRESS_DB_USER: blog_user
      WORDPRESS_DB_PASSWORD: senha_segura
    volumes:
      - wordpress-data:/var/www/html
      - wordpress-uploads:/var/www/html/wp-content/uploads
    depends_on:
      - mysql
    healthcheck:
      test: ["CMD-SHELL", "php-fpm-healthcheck || exit 1"]
      interval: 30s
      timeout: 10s
      retries: 3

  mysql:
    image: mysql:8.0
    container_name: blog-mysql
    restart: unless-stopped
    environment:
      MYSQL_ROOT_PASSWORD: root_senha_segura
      MYSQL_DATABASE: blog
      MYSQL_USER: blog_user
      MYSQL_PASSWORD: senha_segura
    volumes:
      - mysql-data:/var/lib/mysql
      - ./backups:/backups
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 30s
      timeout: 10s
      retries: 3

  redis:
    image: redis:alpine
    container_name: blog-redis
    restart: unless-stopped
    volumes:
      - redis-data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 30s
      timeout: 10s
      retries: 3

volumes:
  mysql-data:
    driver: local
  wordpress-data:
    driver: local
  wordpress-uploads:
    driver: local
  redis-data:
    driver: local
  nginx-logs:
    driver: local

networks:
  default:
    name: blog-network
```

### Script de Backup Automatizado

```bash
#!/bin/bash
# scripts/backup.sh

BACKUP_DIR="/backups/blog"
DATE=$(date +%Y%m%d_%H%M%S)

# Criar diretório de backup
mkdir -p "$BACKUP_DIR"

echo "Iniciando backup do blog em $DATE"

# Backup do banco de dados
docker exec blog-mysql mysqldump -u blog_user -psenha_segura blog > "$BACKUP_DIR/blog-db-$DATE.sql"

# Backup dos volumes
docker run --rm \
  -v blog-persistente_mysql-data:/mysql-data:ro \
  -v blog-persistente_wordpress-data:/wordpress-data:ro \
  -v "$BACKUP_DIR":/backup \
  alpine:latest \
  sh -c "
    tar czf /backup/mysql-volume-$DATE.tar.gz -C /mysql-data . &&
    tar czf /backup/wordpress-volume-$DATE.tar.gz -C /wordpress-data .
  "

# Manter apenas últimos 14 backups
find "$BACKUP_DIR" -name "*.sql" -mtime +14 -delete
find "$BACKUP_DIR" -name "*.tar.gz" -mtime +14 -delete

echo "Backup concluído!"
```

### Comandos de Operação

```bash
# Inicializar o sistema
docker-compose up -d

# Verificar status
docker-compose ps

# Ver logs
docker-compose logs -f

# Backup
./scripts/backup.sh

# Parar sistema (mantém dados)
docker-compose stop

# Reiniciar sistema
docker-compose restart

# Atualizar imagens
docker-compose pull
docker-compose up -d
```

## Solução de Problemas Comuns

### Container Não Inicia Após Reinicialização

```bash
# Verificar logs
docker logs meu-container

# Verificar configuração de restart
docker inspect meu-container | grep -i restart

# Corrigir política de restart
docker update --restart unless-stopped meu-container
```

### Dados Perdidos Após Atualização

```bash
# Verificar se volumes estão configurados
docker inspect meu-container | grep -A 10 "Mounts"

# Listar volumes
docker volume ls

# Verificar integridade do volume
docker run --rm -v meu-volume:/data alpine ls -la /data
```

### Problemas de Permissão

```bash
# Verificar proprietário dos arquivos
docker exec -it meu-container ls -la /data

# Corrigir permissões
docker exec -it meu-container chown -R app:app /data
```

## Conclusão

Executar containers persistentes é essencial para aplicações em produção que precisam manter dados entre execuções. As principais estratégias incluem:

1. **Uso de volumes Docker** para dados críticos
2. **Configuração de restart policies** apropriadas
3. **Implementação de health checks** para monitoramento
4. **Backup regular** dos dados persistentes
5. **Separação clara** entre aplicação e dados

Com essas práticas, você pode criar infraestruturas containerizadas robustas e confiáveis que mantêm dados seguros e aplicações sempre disponíveis.

## Recursos Adicionais

- [Documentação oficial sobre volumes](https://docs.docker.com/storage/volumes/)
- [Guia de restart policies](https://docs.docker.com/config/containers/start-containers-automatically/)
- [Health checks no Docker](https://docs.docker.com/engine/reference/builder/#healthcheck)
- [Docker Compose volumes](https://docs.docker.com/compose/compose-file/compose-file-v3/#volumes)
