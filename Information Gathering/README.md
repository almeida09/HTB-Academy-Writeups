 O Grande Objetivo: O que é Reconhecimento Web?

Você aprendeu que o reconhecimento é a primeira e mais crucial fase de um teste de penetração. O objetivo é mapear a "superfície de ataque" do alvo.

Você viu duas abordagens principais:

Reconhecimento Passivo (OSINT): Coletar informações sem "tocar" no alvo. Você usa fontes públicas como o Google, whois e arquivos da web. É furtivo e seguro.

Reconhecimento Ativo: Interagir diretamente com o alvo. Isso inclui escanear portas, fazer brute-force de subdomínios e "cutucar" o servidor. É mais barulhento, mas revela muito mais.

1. A Primeira Pista: "Quem é o Dono?" (WHOIS)
O primeiro passo em qualquer alvo público é o whois. Você aprendeu que ele funciona como um "cartório" da internet.

O que você aprendeu: A usar o comando whois para descobrir informações de registro de um domínio.

Informações Chave:

Registrador: A empresa onde o domínio foi comprado (ex: Amazon).

Datas: Quando foi criado, quando expira.

Contatos: Nomes e e-mails (administrativo, técnico).

ID IANA: O número de identificação do registrador (a resposta da Questão 1).

2. O Mapa do Tesouro: "Onde Fica?" (DNS e Subdomínios)
Esta foi a maior e mais importante parte do módulo. Você aprendeu que o DNS é a "lista telefônica" da internet, mas que ela esconde muitos números.

A. O Básico do DNS
Você aprendeu a usar a ferramenta dig para consultar registros DNS.

A: O endereço IPv4 do servidor.

MX: O servidor de e-mail.

NS: Os servidores que guardam os registros DNS.

TXT: Registros de texto (usados para verificação, segurança, etc.).

B. Encontrando "Portas Escondidas" (Enumeração de Subdomínios)
Você aprendeu três formas principais de encontrar subdomínios (como mail.dominio.com ou dev.dominio.com):

Logs de Certificado (CT Logs): Uma técnica passiva. Você usou curl e jq para consultar o crt.sh. Isso mostra todos os subdomínios que já tiveram um certificado SSL (HTTPS), sendo uma fonte de informação incrível que o alvo não pode esconder.

DNS Zone Transfer (AXFR): Uma técnica ativa. Você aprendeu a usar dig axfr @servidor.dns dominio.com. Se o servidor estiver mal configurado, ele "cospe" a lista inteira de todos os subdomínios de uma vez.

Brute-Force: A técnica ativa mais comum. Você usou dnsenum ou gobuster com uma wordlist (lista de palavras) para "adivinhar" subdomínios comuns (dev, api, admin, etc.).

C. A Grande Pegadinha: Virtual Hosts (VHosts)
Esta foi a descoberta crucial para resolver o desafio. Você aprendeu que um único servidor (um IP) pode hospedar vários sites diferentes.

A Analogia: Pense no IP como o endereço de um prédio. O nginx (que vimos) é o porteiro.

O Problema: Quando você acessa pelo IP (curl http://[IP_DO_ALVO]), o porteiro não sabe para qual "apartamento" (site) você quer ir, então ele te mostra um 404.

A Solução: Você precisa dizer o nome do site que quer visitar. Você aprendeu a fazer isso de duas formas:

Editando /etc/hosts: Você "ensina" seu computador que web1337.inlanefreight.htb mora naquele IP.

Usando gobuster vhost: A ferramenta que faz o brute-force de VHosts, testando [palavra].dominio.com até que um dos "apartamentos" responda (como o web1337 fez).

3. O Diagnóstico: "O que Roda Lá?" (Fingerprinting)
Depois de encontrar um site, você aprendeu a identificar as tecnologias que ele usa.

curl -I: Para pegar os Cabeçalhos (Headers) e ver o Server: (foi assim que resolvemos a Questão 2 e vimos o Apache).

wafw00f: Para detectar se existe um Web Application Firewall (WAF) protegendo o site.

nikto: Um scanner que procura por arquivos, configurações e softwares desatualizados.

4. A Exploração: "O que Tem Dentro?" (Crawling)
Você aprendeu a "vasculhar" o conteúdo de um site que você encontrou.

robots.txt: O primeiro lugar para olhar. É um arquivo onde o dono do site pede aos robôs (como o Google) para não visitarem certas pastas. Para um pentester, é um mapa de coisas interessantes, como o /admin_h1dd3n/ (que levou à Questão 3).

.well-known/: Um diretório padronizado que às vezes contém informações de segurança ou configuração (como security.txt).

Crawler/Spider (ReconSpider.py): Você aprendeu a usar uma ferramenta (baseada em Scrapy) que navega automaticamente por todos os links de um site e extrai dados valiosos, como e-mails e comentários (o que resolveu as Questões 4 e 5).

5. A Espionagem: "O que a Internet Sabe?" (OSINT)
Finalmente, você aprendeu a usar ferramentas públicas para encontrar informações que o alvo "esqueceu" na internet.

Google Dorking: Usar filtros de busca avançados no Google (como site:, filetype:, inurl:) para encontrar arquivos sensíveis, painéis de login ou PDFs confidenciais.

Wayback Machine (Web Archives): O "museu da internet". Você aprendeu a usá-lo para ver versões antigas de um site, o que pode revelar páginas ou informações que já foram deletadas.

Você aprendeu que a coleta de informações não é um único comando, mas um processo investigativo onde cada pequena descoberta é uma pista para a próxima.
