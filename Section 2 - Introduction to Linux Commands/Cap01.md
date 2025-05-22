# Introdução aos Comandos Linux

## Comandos Básicos de Navegação e Gerenciamento de Arquivos

1. **ls** - Lista o conteúdo de um diretório (arquivos e pastas).
   - Opções comuns: `ls -l` (formato detalhado), `ls -a` (mostra arquivos ocultos), `ls -h` (tamanhos legíveis)

2. **pwd** - Print Working Directory. Mostra o caminho completo do diretório atual.

3. **cd** - Change Directory. Muda para outro diretório.
   - Exemplos: `cd /etc` (caminho absoluto), `cd documentos` (caminho relativo), `cd ..` (diretório pai), `cd ~` (diretório home)

4. **mkdir** - Make Directory. Cria um novo diretório/pasta.
   - Exemplo: `mkdir projetos` ou `mkdir -p projetos/website/css` (cria estrutura completa)

5. **mv** - Move ou renomeia arquivos e diretórios.
   - Exemplos: `mv arquivo.txt /home/user/documentos/` (mover) ou `mv arquivo.txt novo_nome.txt` (renomear)

6. **cp** - Copy. Copia arquivos ou diretórios.
   - Exemplos: `cp arquivo.txt copia.txt` ou `cp -r pasta/ backup/` (copia recursivamente)

7. **rm** - Remove. Exclui arquivos ou diretórios.
   - Exemplos: `rm arquivo.txt` ou `rm -r diretorio/` (remove recursivamente) - **Cuidado: não há lixeira!**

8. **touch** - Cria um arquivo vazio ou atualiza a data de modificação de um arquivo existente.
   - Exemplo: `touch novo_arquivo.txt`

9. **ln** - Link. Cria links simbólicos ou físicos entre arquivos.
   - Exemplo: `ln -s arquivo_original.txt link_simbolico.txt`

10. **clear** - Limpa a tela do terminal.

## Visualização e Manipulação de Conteúdo

11. **cat** - Concatenate. Exibe o conteúdo de arquivos de texto.
    - Exemplo: `cat arquivo.txt` ou `cat arquivo1.txt arquivo2.txt` (exibe múltiplos arquivos)

12. **echo** - Exibe texto na tela ou redireciona para um arquivo.
    - Exemplos: `echo "Olá mundo"` ou `echo "Texto" > arquivo.txt` (sobrescreve) ou `echo "Mais texto" >> arquivo.txt` (anexa)

13. **less** - Visualizador de arquivos com paginação (permite navegar com setas).
    - Exemplo: `less arquivo_grande.log`

14. **man** - Manual. Exibe o manual de um comando.
    - Exemplo: `man ls` (mostra todas as opções do comando ls)

15. **uname** - Unix Name. Mostra informações sobre o sistema.
    - Exemplo: `uname -a` (todas as informações do sistema)

16. **whoami** - Mostra o nome do usuário atual.

17. **tar** - Tape Archive. Compacta/descompacta arquivos.
    - Exemplos: `tar -cvf arquivo.tar diretorio/` (cria) ou `tar -xvf arquivo.tar` (extrai)

18. **grep** - Busca padrões em texto utilizando expressões regulares.
    - Exemplo: `grep "palavra" arquivo.txt` ou `ls -la | grep "jan"` (busca em saída de outro comando)

19. **head** - Exibe as primeiras linhas de um arquivo (padrão: 10 linhas).
    - Exemplo: `head arquivo.txt` ou `head -n 5 arquivo.txt` (primeiras 5 linhas)

20. **tail** - Exibe as últimas linhas de um arquivo (padrão: 10 linhas).
    - Exemplo: `tail arquivo.log` ou `tail -f arquivo.log` (acompanha atualizações em tempo real)

## Comparação e Análise de Arquivos

21. **diff** - Difference. Compara o conteúdo de dois arquivos linha por linha.
    - Exemplo: `diff arquivo1.txt arquivo2.txt`

22. **cmp** - Compare. Compara dois arquivos byte a byte.
    - Exemplo: `cmp arquivo1.bin arquivo2.bin`

23. **comm** - Compare. Compara duas listas ordenadas linha por linha.
    - Exemplo: `comm arquivo1.txt arquivo2.txt`

24. **sort** - Ordena as linhas de um arquivo de texto.
    - Exemplo: `sort lista.txt` ou `sort -r lista.txt` (ordem reversa)

25. **export** - Define variáveis de ambiente.
    - Exemplo: `export PATH=$PATH:/novo/caminho`

## Compactação e Transferência

26. **zip** - Compacta arquivos no formato ZIP.
    - Exemplo: `zip -r arquivo.zip diretorio/`

27. **unzip** - Extrai arquivos de um ZIP.
    - Exemplo: `unzip arquivo.zip`

28. **ssh** - Secure Shell. Conecta a um servidor remoto de forma segura.
    - Exemplo: `ssh usuario@servidor.com`

29. **service** - Controla serviços do sistema (iniciar, parar, reiniciar).
    - Exemplo: `service apache2 restart` ou `systemctl restart apache2` (em sistemas modernos)

30. **ps** - Process Status. Lista processos em execução.
    - Exemplo: `ps aux` (lista todos os processos detalhadamente)

## Gerenciamento de Processos e Sistema

31. **kill / killall** - Envia sinais para processos (geralmente para encerrá-los).
    - Exemplos: `kill 1234` (encerra o processo com PID 1234) ou `killall firefox` (encerra todos os processos firefox)

32. **df** - Disk Free. Mostra o espaço livre em disco.
    - Exemplo: `df -h` (formato legível para humanos)

33. **mount** - Monta sistemas de arquivos.
    - Exemplo: `mount /dev/sdb1 /mnt/usb`

34. **chmod** - Change Mode. Altera as permissões de arquivos e diretórios.
    - Exemplo: `chmod 755 script.sh` ou `chmod +x script.sh` (torna executável)

35. **chown** - Change Owner. Altera o proprietário de arquivos e diretórios.
    - Exemplo: `chown usuario:grupo arquivo.txt`

36. **ifconfig** - Interface Configuration. Configura interfaces de rede (comando legado, substituído por `ip`).
    - Exemplo: `ifconfig eth0` ou `ip addr show`

37. **traceroute** - Traça a rota que os pacotes seguem até um destino.
    - Exemplo: `traceroute google.com`

38. **wget** - Faz download de arquivos da web.
    - Exemplo: `wget https://site.com/arquivo.zip`

39. **ufw** - Uncomplicated Firewall. Gerencia o firewall.
    - Exemplo: `ufw allow 22` (libera porta SSH)

40. **iptables** - Configura regras de firewall de baixo nível.
    - Exemplo: `iptables -A INPUT -p tcp --dport 80 -j ACCEPT`

## Administração do Sistema

41. **apt, pacman, yum, rpm** - Gerenciadores de pacotes (variam conforme a distribuição Linux).
    - Exemplos: `apt update && apt upgrade` (Debian/Ubuntu), `yum update` (RHEL/CentOS), `pacman -Syu` (Arch)

42. **sudo** - Super User DO. Executa comandos com privilégios de superusuário.
    - Exemplo: `sudo apt update`

43. **cal** - Calendar. Mostra um calendário no terminal.
    - Exemplo: `cal` ou `cal 2023`

44. **alias** - Cria atalhos para comandos.
    - Exemplo: `alias ll='ls -la'`

45. **dd** - Data Duplicator. Copia e converte arquivos, incluindo dispositivos de bloco.
    - Exemplo: `dd if=/dev/sda of=disco.img` (cria imagem de disco)

46. **wheris** - Localiza o binário, fonte e página de manual de um comando.
    - Exemplo: `whereis python`

47. **whatis** - Exibe uma breve descrição de um comando.
    - Exemplo: `whatis ls`

48. **top** - Table of Processes. Mostra processos em execução em tempo real.
    - Alternativa moderna: `htop`

49. **useradd** - Adiciona um novo usuário ao sistema.
    - Exemplo: `sudo useradd -m novousuario`

50. **passwd** - Altera a senha de um usuário.
    - Exemplo: `passwd` (própria senha) ou `sudo passwd usuario` (senha de outro usuário)

## Dicas para o uso eficiente do terminal Linux

- Use a tecla TAB para autocompletar comandos e nomes de arquivos
- Use as setas ↑↓ para navegar no histórico de comandos
- Use Ctrl+R para buscar em comandos anteriores
- Combine comandos com pipe (|): `comando1 | comando2`
- Redirecione saída com > (sobrescreve) ou >> (anexa): `comando > arquivo.txt`
- Use caracteres curinga: * (qualquer sequência), ? (qualquer caractere)
- Interrompa comandos com Ctrl+C
- Suspenda processos com Ctrl+Z (retome com `fg`)
- Use `&` ao final de um comando para executá-lo em segundo plano

Dominar estes comandos fundamentais proporcionará uma base sólida para trabalhar eficientemente em ambientes Linux, seja para administração de sistemas, desenvolvimento de software ou uso diário.

