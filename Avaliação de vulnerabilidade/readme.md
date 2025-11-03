# HTB Academy: Avaliação de Habilidades (Skills Assessment) - OpenVAS

## 🎯 Objetivo

O objetivo deste desafio foi utilizar o OpenVAS (Greenbone Vulnerability Manager - GVM) para analisar um scan de vulnerabilidades pré-existente de um servidor Linux da empresa "Inlanefreight". A tarefa consistia em navegar pela interface do GVM, localizar o relatório correto e extrair informações específicas para responder a quatro perguntas.

## Methodologia e Passos

1.  **Acesso ao GVM:** Conectei à interface web do OpenVAS (GVM) no IP fornecido  com as credenciais de estudante.
2.  **Localização do Relatório:** O desafio informava que os dados do scan já estavam "pré-populados". Naveguei até **Scans > Reports**.
3.  **Seleção do Relatório Correto:** Na lista de relatórios, identifiquei o scan relevante.
    * **Alvo:** `Linux Server host`
    * **Tipo de Scan:** Autenticado (`run as an authenticated user`)
    * **Relatório escolhido:** `Linux_basic_authed`
4.  **Análise dos Resultados:** Dentro do relatório `Linux_basic_authed`, abri a aba **Results** (Resultados) para analisar as 42 vulnerabilidades e informações encontradas.

## 💡 Principais Aprendizados

* **Navegação no GVM (OpenVAS):** Este laboratório foi essencial para entender a estrutura da interface do Greenbone, diferenciando "Tasks" (Tarefas de scan) de "Reports" (Relatórios gerados).
* **Análise de Relatórios:** Aprendi a ler a lista de "Results" de um scan, filtrar por severidade, serviço ou porta, e clicar em cada item para obter uma descrição detalhada.
* **Interpretação de Resultados:** Consegui identificar não apenas vulnerabilidades críticas, mas também resultados informativos (Log) que são cruciais para o reconhecimento, como a detecção precisa do Sistema Operacional.
* **Conexão entre Portas e Serviços:** Reforcei a importância de investigar portas específicas (como 21 para FTP e 80 para HTTP) ao procurar por vulnerabilidades em serviços conhecidos.
