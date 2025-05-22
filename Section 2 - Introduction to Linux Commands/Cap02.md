# Criando e Removendo Diretórios no Linux

O gerenciamento de diretórios é uma habilidade fundamental para trabalhar em sistemas Linux. Esta seção aborda as operações básicas de criação e remoção de diretórios, incluindo opções, exemplos práticos e melhores práticas.

## Criando Diretórios

### O comando `mkdir` (Make Directory)

O comando `mkdir` é usado para criar novos diretórios (pastas) no sistema de arquivos.

#### Sintaxe básica:
```bash
mkdir [opções] nome_do_diretório [nome_do_diretório2 ...]
```

#### Exemplos simples:

1. **Criar um único diretório**:
```bash
mkdir projetos
```

2. **Criar múltiplos diretórios de uma vez**:
```bash
mkdir docs imagens videos
```
Este comando cria três diretórios separados no local atual.

### Opções importantes

#### 1. Criação de estruturas de diretórios com `-p` (parents)

A opção `-p` permite criar uma estrutura hierárquica completa de diretórios em um único comando:

```bash
mkdir -p projetos/website/css
```

Este comando cria o diretório `projetos`, dentro dele cria `website`, e dentro de `website` cria `css`, mesmo que os diretórios pais não existam previamente.

Sem a opção `-p`, você receberia um erro se tentasse criar um subdiretório dentro de um diretório que ainda não existe:
```
mkdir: cannot create directory 'projetos/website/css': No such file or directory
```

#### 2. Definindo permissões com `-m` (mode)

A opção `-m` permite definir as permissões do diretório no momento da criação:

```bash
mkdir -m 755 scripts
```

Este comando cria um diretório `scripts` com permissões 755 (rwxr-xr-x), o que significa:
- Proprietário: leitura (r), escrita (w), execução (x)
- Grupo: leitura (r), execução (x)
- Outros: leitura (r), execução (x)

#### 3. Visualizando operações com `-v` (verbose)

A opção `-v` exibe cada diretório à medida que é criado:

```bash
mkdir -v projeto backend frontend
```

Saída:
```
mkdir: created directory 'projeto'
mkdir: created directory 'backend'
mkdir: created directory 'frontend'
```

### Exemplos práticos

#### Criando uma estrutura de projeto web:
```bash
mkdir -p projeto/src/{css,js,images}
mkdir -p projeto/docs
mkdir -p projeto/tests/unit
```

Este comando cria uma estrutura completa de projeto com múltiplos subdiretórios em uma única operação:
```
projeto/
├── src/
│   ├── css/
│   ├── js/
│   └── images/
├── docs/
└── tests/
    └── unit/
```

#### Criando um diretório com data:
```bash
mkdir backup_$(date +%Y%m%d)
```

Este comando cria um diretório com nome baseado na data atual, como `backup_20250516`.

## Removendo Diretórios

### O comando `rmdir` (Remove Directory)

O comando `rmdir` é usado para remover diretórios vazios.

#### Sintaxe básica:
```bash
rmdir [opções] nome_do_diretório [nome_do_diretório2 ...]
```

#### Exemplos simples:

1. **Remover um diretório vazio**:
```bash
rmdir temp
```

2. **Remover múltiplos diretórios vazios**:
```bash
rmdir dir1 dir2 dir3
```

### Limitações do `rmdir`

O `rmdir` só pode remover diretórios que estejam vazios. Se você tentar remover um diretório que contém arquivos ou subdiretórios, receberá um erro:
```
rmdir: failed to remove 'projeto': Directory not empty
```

### Opções do `rmdir`

#### 1. Remover hierarquias com `-p` (parents)

A opção `-p` remove diretórios e seus pais, se ficarem vazios:

```bash
rmdir -p projeto/src/css
```

Este comando remove o diretório `css`, depois tenta remover `src` e, por fim, `projeto` (apenas se cada um estiver vazio após a remoção do anterior).

#### 2. Ignorar não-existentes com `--ignore-fail-on-non-empty`

Esta opção faz com que `rmdir` não mostre erros quando tenta remover diretórios não vazios:

```bash
rmdir --ignore-fail-on-non-empty projeto
```

### O comando `rm` (Remove)

Para remover diretórios não vazios, o comando `rm` com a opção `-r` (recursive) é mais apropriado.

#### Sintaxe básica:
```bash
rm -r [opções] nome_do_diretório
```

#### Exemplos:

1. **Remover um diretório e todo seu conteúdo**:
```bash
rm -r projeto
```

2. **Remover com confirmação (mais seguro)**:
```bash
rm -ri projeto
```
Com a opção `-i` (interactive), o sistema pedirá confirmação para cada arquivo e diretório.

3. **Forçar a remoção**:
```bash
rm -rf projeto
```
Com a opção `-f` (force), o sistema remove tudo sem perguntar, mesmo arquivos protegidos contra escrita.

### ⚠️ Cuidados ao usar o `rm -rf`

**AVISO: O comando `rm -rf` é muito poderoso e potencialmente perigoso.**

- Ele remove diretórios e arquivos sem confirmação
- Não há "lixeira" ou possibilidade de recuperação fácil
- Nunca use `rm -rf /` ou `rm -rf /*` (pode destruir todo o sistema)
- Sempre verifique duas vezes o que está removendo

## Boas práticas e dicas

### Verificação antes da remoção

1. **Verificar o conteúdo antes de remover**:
```bash
ls -la diretório/
```

2. **Verificar o que será removido (simulação)**:
```bash
rm -rvi diretório/
```
Com a opção `-v` (verbose), você vê o que será removido, e com `-i` pode cancelar o processo a qualquer momento.

### Lidando com nomes especiais

1. **Diretórios com espaços ou caracteres especiais**:
```bash
mkdir "Meus Documentos"
rmdir "Meus Documentos"
```
Ou use escape:
```bash
mkdir Meus\ Documentos
```

2. **Diretórios que começam com traço**:
```bash
mkdir -- -estranho
rmdir -- -estranho
```
Ou use caminho relativo ou absoluto:
```bash
mkdir ./-estranho
rmdir ./-estranho
```

### Permissões e propriedade

Se você não tiver permissões para criar ou remover um diretório, use `sudo`:

```bash
sudo mkdir /opt/aplicacao
sudo rm -r /opt/aplicacao-antiga
```

## Exemplos de cenários comuns

### Cenário 1: Preparando um ambiente de desenvolvimento

```bash
# Criar estrutura do projeto
mkdir -p ~/projetos/app-nova/{src,tests,docs,config}
mkdir -p ~/projetos/app-nova/src/{components,assets,utils}
touch ~/projetos/app-nova/README.md
```

### Cenário 2: Limpeza de diretórios temporários

```bash
# Verificar diretórios antigos
find /tmp -type d -name "temp_*" -mtime +7

# Remover diretórios com mais de 7 dias
find /tmp -type d -name "temp_*" -mtime +7 -exec rm -rf {} \;
```

### Cenário 3: Backup antes de remover

```bash
# Criar backup antes de remover
tar -czf projeto_backup.tar.gz projeto/
rm -rf projeto/
```

## Comandos avançados relacionados

### Comando `find` para encontrar e remover diretórios

```bash
# Encontrar e remover diretórios vazios
find . -type d -empty -delete

# Encontrar e remover diretórios com nome específico
find . -type d -name "temp" -exec rm -rf {} \;
```

### Comando `mv` para renomear ou mover diretórios

```bash
# Renomear diretório
mv antigo_nome novo_nome

# Mover diretório
mv diretorio /novo/local/
```

## Resolução de problemas comuns

### Problema: "Permission denied"

```
mkdir: cannot create directory 'diretorio': Permission denied
```

**Solução**: Verifique as permissões do diretório pai ou use `sudo`.

### Problema: "Directory not empty"

```
rmdir: failed to remove 'diretorio': Directory not empty
```

**Solução**: Use `rm -r diretorio` para remover diretórios não vazios.

### Problema: "No such file or directory"

```
mkdir: cannot create directory 'diretorio/subdir': No such file or directory
```

**Solução**: Use a opção `-p` para criar diretórios pais: `mkdir -p diretorio/subdir`.

## Conclusão

Dominar a criação e remoção de diretórios é fundamental para gerenciar eficientemente o sistema de arquivos Linux. Com os comandos `mkdir`, `rmdir` e `rm -r`, você pode organizar seus arquivos em estruturas lógicas e manter seu sistema limpo.

Lembre-se sempre:
- Use `mkdir -p` para criar estruturas complexas
- Use `rmdir` apenas para diretórios vazios
- Use `rm -r` com cautela para diretórios com conteúdo
- Verifique duas vezes antes de remover diretórios importantes
- Faça backups de dados importantes
