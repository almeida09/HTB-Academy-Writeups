# Módulo: Network Enumeration with Nmap

Este repositório documenta meu aprendizado e o "passo a passo" do módulo de Nmap do HTB Academy.

## Conceitos Principais que Aprendi

* **TCP/IP:** Entendi como funciona o "Three-Way Handshake" (SYN, SYN/ACK, ACK).
* **Tipos de Scan:** A diferença entre um scan TCP Connect (`-sT`), que é barulhento, e um scan SYN (`-sS`), que é stealth.
* **Enumeração:** O que significa enumerar serviços (`-sV`) e rodar scripts (`-sC`).

## Meu Processo de Raciocínio (O "Como Eu Cheguei Lá")


### Desafio 1: Scan Básico

**O Problema:** Identificar portas abertas na máquina X.

**Minha 1ª Tentativa (O que eu fiz):**
```bash
nmap 10.10.10.X
Resultado: Demorou muito e não me deu a versão do serviço.

Minha 2ª Tentativa (O que eu aprendi e melhorei): Usei flags para ser mais rápido e obter mais informações.

Bash

# -sS: Scan SYN (mais rápido e stealth)
# -T4: Acelera o tempo
# -p-: Escaneia todas as 65535 portas
# -sV: Tenta descobrir a versão do serviço
nmap -sS -T4 -p- -sV 10.10.10.X
Resultado: Descobri que a porta 80 estava rodando Apache 2.4.x e a 22 estava rodando OpenSSH.

Comandos Mais Úteis (Meu Cheatsheet)

nmap -sS -sV -sC <ip>

nmap --script=vuln <ip>: Rodar scripts de vulnerabilidade.

nmap -Pn <ip>: Usar quando a máquina não responde a ping (ICMP).



