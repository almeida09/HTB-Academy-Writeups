# Coleta de Informações - Edição Web

Aqui estão minhas anotações e comandos de como cheguei em cada resposta neste módulo do HTB Academy.

## Seção: DNS e Subdomínios (Transferência de Zona)

O objetivo era encontrar subdomínios escondidos tentando uma Transferência de Zona (AXFR) no servidor DNS do alvo.

1.  **Gerei o Alvo:** O HTB me deu o IP: `10.129.42.195`.
2.  **Comando:** Usei o `dig` para pedir a transferência de zona (AXFR) usando o IP do alvo como servidor (`@10.129.42.195`).
    ```bash
    dig axfr @10.129.42.195 inlanefreight.htb
    ```
3.  **Conclusões (Respostas):**
    * **Total de Registros:** A saída do comando mostrou `XFR size: 22 records`.
    * **IP do `ftp.admin.inlanefreight.htb`:** Olhando a lista, encontrei a linha `ftp.admin.inlanefreight.htb. ... IN A 10.10.34.2`.
    * **Maior IP em `10.10.200.x`:** Analisando a lista, o maior IP nesse range era `10.10.200.14`.

## Seção: Hosts Virtuais (VHost Fuzzing)

O objetivo era encontrar subdomínios que o servidor web conhecia, mas o DNS não.

1.  **Gerei o Alvo:** O HTB me deu o IP e Porta: `http://94.237.57.221:48732`.
2.  **Comando:** Usei o `gobuster` no modo `vhost`. Tive que usar as flags `--domain` (para dizer o domínio base) e `--append-domain` (para ativar a junção da palavra com o domínio).
    ```bash
    gobuster vhost -u [http://94.237.57.221:48732](http://94.237.57.221:48732) -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-110000.txt --domain inlanefreight.htb --append-domain
    ```
3.  **Conclusões (Respostas):**
    * **"web":** `web17611.inlanefreight.htb` (o scan encontrou um outro "web" antes, mas este era o correto).
    * **"vm":** `vm5.inlanefreight.htb`
    * **"br":** `browse.inlanefreight.htb`
    * **"a":** `admin.inlanefreight.htb`
    * **"su":** `support.inlanefreight.htb`

## Seção: Impressão Digital (Fingerprinting)

O objetivo era descobrir as tecnologias (Servidor, CMS, SO) rodando nos alvos `.local`.

1.  **Gerei o Alvo:** O HTB me deu o IP: `10.129.238.221`.
2.  **Comando (Configuração):** O "pulo do gato" foi editar o arquivo `/etc/hosts` para o Kali saber quem eram os domínios `.local`.
    ```bash
    echo "10.129.238.221   app.inlanefreight.local   dev.inlanefreight.local" | sudo tee -a /etc/hosts
    ```
3.  **Conclusões (Respostas):**
    * **Versão do Apache (Questão 1):** Rodei `curl -I http://app.inlanefreight.local` e o cabeçalho `Server:` mostrou `Apache/2.4.41 (Ubuntu)`.
    * **CMS (Questão 2):** Rodei `nikto -h http://app.inlanefreight.local -Tuning b` e ele listou pastas como `/administrator/` e `/components/`, que são a estrutura clássica do `Joomla`.
    * **SO (Questão 3):** Rodei `curl -I http://dev.inlanefreight.local` e o cabeçalho `Server:` também mostrou `(Ubuntu)`.
