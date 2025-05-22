# Comandos para Gerenciamento de Usuários no Linux

## Introdução

O gerenciamento de usuários é uma tarefa fundamental na administração de sistemas Linux, incluindo distribuições como Ubuntu. Este documento aborda os principais comandos para criar, modificar, excluir e gerenciar usuários e seus privilégios.

## Comandos Básicos para Usuários

### Criação de Usuários

#### 1. Comando `useradd`

O `useradd` é um utilitário de baixo nível para criar novos usuários.

```bash
# Sintaxe básica
useradd [opções] nome_usuario

# Exemplo: criar um usuário simples
sudo useradd joao

# Criar usuário com diretório home
sudo useradd -m joao

# Criar usuário com shell específico
sudo useradd -m -s /bin/bash joao

# Criar usuário com UID específico
sudo useradd -u 1500 -m joao

# Criar usuário com diretório home específico
sudo useradd -m -d /home/personalizado/joao joao
```

**Opções comuns:**
- `-m` : Cria o diretório home do usuário
- `-d diretório` : Especifica o diretório home personalizado
- `-s shell` : Define o shell padrão do usuário
- `-u UID` : Especifica o ID do usuário
- `-g grupo` : Define o grupo primário
- `-G grupos` : Define grupos adicionais (separados por vírgula)
- `-c comentário` : Adiciona um comentário (geralmente o nome completo)
- `-e data` : Define data de expiração da conta (formato YYYY-MM-DD)

#### 2. Comando `adduser`

O `adduser` é um script mais amigável (frontend) para `useradd`, que faz perguntas interativas e configura valores padrão.

```bash
# Criar usuário interativamente
sudo adduser maria

# Adicionar usuário a um grupo existente
sudo adduser maria docker
```

### Definição ou Alteração de Senha

#### Comando `passwd`

```bash
# Definir senha para um usuário (como root)
sudo passwd joao

# Usuário alterando sua própria senha
passwd

# Forçar usuário a alterar senha no próximo login
sudo passwd -e joao

# Bloquear uma conta de usuário
sudo passwd -l joao

# Desbloquear uma conta de usuário
sudo passwd -u joao
```

### Modificação de Usuários

#### Comando `usermod`

```bash
# Alterar o shell do usuário
sudo usermod -s /bin/zsh joao

# Adicionar usuário a grupos suplementares
sudo usermod -aG docker,sudo joao

# Renomear usuário (alterar login)
sudo usermod -l joao_novo joao

# Alterar diretório home
sudo usermod -d /home/novo_home joao

# Bloquear uma conta
sudo usermod -L joao

# Desbloquear uma conta
sudo usermod -U joao
```

**Opções importantes:**
- `-aG` : Adicionar a grupos suplementares sem remover dos grupos existentes
- `-g` : Alterar grupo primário
- `-l` : Alterar o nome de login
- `-L` : Bloquear conta (não pode logar)
- `-U` : Desbloquear conta

### Exclusão de Usuários

#### 1. Comando `userdel`

```bash
# Remover usuário (mantém o diretório home)
sudo userdel joao

# Remover usuário e seu diretório home
sudo userdel -r joao

# Forçar remoção mesmo se o usuário estiver logado
sudo userdel -f joao
```

#### 2. Comando `deluser`

Frontend amigável para `userdel`:

```bash
# Remover usuário
sudo deluser joao

# Remover usuário e seu diretório home
sudo deluser --remove-home joao

# Remover usuário e todos os arquivos de sua propriedade no sistema
sudo deluser --remove-all-files joao
```

## Visualização de Informações de Usuários

### Comando `id`

Mostra informações de identidade do usuário:

```bash
# Informações sobre o usuário atual
id

# Informações sobre um usuário específico
id joao
```

### Comando `whoami`

Mostra o nome do usuário atual:

```bash
whoami
```

### Comando `who` e `w`

Mostra quem está logado no sistema:

```bash
# Usuários logados (básico)
who

# Usuários logados (informações detalhadas)
w
```

### Comando `last`

Mostra os últimos logins no sistema:

```bash
# Exibir histórico de logins
last

# Exibir últimos logins de um usuário específico
last joao
```

### Comando `finger`

Exibe informações detalhadas sobre usuários (nem sempre instalado por padrão):

```bash
# Instalar finger
sudo apt install finger

# Verificar informações de usuário
finger joao
```

## Gerenciamento de Grupos

### Criação de Grupos

```bash
# Criar novo grupo
sudo groupadd desenvolvedores

# Criar grupo com GID específico
sudo groupadd -g 2000 desenvolvedores
```

### Modificação de Grupos

```bash
# Renomear grupo
sudo groupmod -n novo_nome antigo_nome

# Alterar GID do grupo
sudo groupmod -g 2500 desenvolvedores
```

### Exclusão de Grupos

```bash
# Remover grupo
sudo groupdel desenvolvedores
```

### Adicionar/Remover Usuários de Grupos

```bash
# Adicionar usuário a um grupo
sudo gpasswd -a joao desenvolvedores

# Remover usuário de um grupo
sudo gpasswd -d joao desenvolvedores

# Definir usuário como administrador do grupo
sudo gpasswd -A joao desenvolvedores
```

### Listar Grupos

```bash
# Listar todos os grupos do sistema
getent group

# Listar grupos aos quais o usuário pertence
groups joao
```

## Arquivos de Configuração Importantes

### `/etc/passwd`

Contém informações básicas sobre as contas de usuários.

Formato: `nome:senha:UID:GID:comentário:diretório_home:shell`

```bash
# Visualizar arquivo passwd
cat /etc/passwd

# Buscar usuário específico
grep joao /etc/passwd
```

### `/etc/shadow`

Contém senhas criptografadas dos usuários e informações de expiração.

```bash
# Visualizar arquivo shadow (apenas root)
sudo cat /etc/shadow
```

### `/etc/group`

Contém informações sobre grupos.

```bash
# Visualizar arquivo group
cat /etc/group
```

### `/etc/gshadow`

Contém senhas criptografadas dos grupos e administradores.

```bash
# Visualizar arquivo gshadow (apenas root)
sudo cat /etc/gshadow
```

## Permissões e Propriedade

### Comandos `chown` e `chgrp`

Alteram o proprietário e grupo de arquivos e diretórios:

```bash
# Alterar proprietário
sudo chown joao arquivo.txt

# Alterar proprietário e grupo
sudo chown joao:desenvolvedores arquivo.txt

# Alterar recursivamente (diretórios e subdiretórios)
sudo chown -R joao:desenvolvedores /pasta
```

### Comando `chmod`

Altera as permissões de acesso:

```bash
# Formato numérico
sudo chmod 755 arquivo.txt

# Formato simbólico
sudo chmod u+x arquivo.txt
```

## Gerenciamento de Acessos Avançados

### Comando `sudo`

Permite executar comandos como outro usuário (geralmente root):

```bash
# Executar comando como root
sudo apt update

# Editar arquivo de configuração do sudo
sudo visudo
```

### Grupo `sudo` e `wheel`

Usuários nesses grupos têm permissões administrativas:

```bash
# Adicionar usuário ao grupo sudo
sudo usermod -aG sudo joao

# Verificar quem está no grupo sudo
getent group sudo
```

## Boas Práticas de Segurança

1. **Princípio do Menor Privilégio**: Conceda aos usuários apenas as permissões necessárias para suas tarefas.

2. **Senhas Fortes**: Configure políticas de senha robustas.

3. **Auditoria Regular**: Monitore regularmente as contas de usuário ativas.

4. **Remoção de Contas Inativas**: Desative ou remova contas que não são mais necessárias.

5. **Uso de SSH por Chave**: Prefira autenticação por chave em vez de senha para conexões SSH.

6. **Configuração de expiração de senhas**:
   ```bash
   sudo chage -M 90 joao  # Expira a senha em 90 dias
   sudo chage -l joao     # Visualiza configurações de expiração
   ```

## Exemplos Práticos

### Configurar um Novo Usuário Administrativo

```bash
# Criar usuário com diretório home
sudo useradd -m -s /bin/bash admin_user

# Definir senha
sudo passwd admin_user

# Adicionar ao grupo sudo
sudo usermod -aG sudo admin_user
```

### Configurar um Usuário de Serviço (System User)

```bash
# Criar usuário de sistema sem diretório home
sudo useradd --system --no-create-home --shell /usr/sbin/nologin app_service

# Configurar diretório da aplicação
sudo mkdir -p /var/app
sudo chown app_service:app_service /var/app
```

### Configurar Múltiplos Usuários para um Projeto

```bash
# Criar grupo do projeto
sudo groupadd projeto_x

# Criar diretório do projeto
sudo mkdir -p /var/projetos/projeto_x
sudo chown :projeto_x /var/projetos/projeto_x
sudo chmod 2775 /var/projetos/projeto_x  # SGID bit para manter o grupo

# Adicionar usuários ao grupo
sudo usermod -aG projeto_x joao
sudo usermod -aG projeto_x maria
```

## Conclusão

O gerenciamento eficaz de usuários é fundamental para a segurança e organização de sistemas Linux. Os comandos abordados neste documento permitem criar um ambiente multiusuário seguro e bem administrado, adequado tanto para servidores quanto para estações de trabalho.