# Relatorio de Investigacao SOC: Analise de Incidente via Splunk

## Informacoes do Caso
* **Plataforma:** TryHackMe
* **Alvo:** Wayne Enterprises (imreallynotbatman.com)
* **Conceito Base:** Cyber Kill Chain e Investigacao de Incidentes
* **Status:** Investigacao Concluida
* **Resultado:** Ataque direcionado com sucesso na aplicacao web, seguido de desfiguracao (defacement) e identificacao de infraestrutura externa do adversario (Poison Ivy).

---

## 1. Visao Geral do Incidente
A Wayne Enterprises sofreu um ataque cibernetico onde o site corporativo `www.imreallynotbatman.com` foi desfigurado (defacement), exibindo a mensagem "SEU SITE FOI DESFIGURADO". O objetivo tecnico desta investigacao foi utilizar o Splunk SIEM para centralizar, correlacionar e mapear todas as atividades do atacante atraves das fases da Cyber Kill Chain.

### Mapeamento de Fontes de Dados (Data Summary)
Para a execucao da analise, o Splunk centralizou logs de multiplos ativos. As principais fontes de registros (sourcetypes) utilizadas foram:

* **Atividades Centradas no Host:**
  * `XmlWinEventLog`: Registros do Sysmon (crucial para criacao de processos e comandos).
  * `iis`: Logs do servidor web IIS (identificacao de requisicoes HTTP, codigos de status e IPs).
  * `winRegistry` / `registry_win`: Modificacoes e criacoes no Registro do Windows.

* **Atividades Centradas na Rede:**
  * `Suricata`: Alertas gerados pelo IDS (identificacao de assinaturas de ataques conhecidos).
  * `fortigate_utm`: Logs do firewall Fortinet (fluxo de trafego permitido/bloqueado).
  * `stream:http`: Fluxo de rede detalhado do trafego HTTP.
  * `stream:dns`: Requisicoes e respostas DNS.

* **Vulnerabilidades:**
  * `Nessus:scan`: Resultados e relatorios do scanner de vulnerabilidades automatizado.

---

## 2. Fase de Reconhecimento
* **Etapa da Kill Chain:** Reconhecimento (Reconnaissance)
* **Objetivo:** Identificar o endereco IP do atacante, as ferramentas de varredura utilizadas e o software do servidor alvo.

### Estrategia de Busca no Splunk
Para rastrear a atividade inicial contra o dominio, a busca concentrou-se no indice principal utilizando filtros de trafego web e assinaturas de IDS.

```spl
index=botsv1 imreallynotbatman.com sourcetype=stream:http

```

Apos filtrar pelo tráfego HTTP, dois enderecos IP de origem principais foram identificados no campo `src_ip`: `40.80.148.42` (com maior volume de requisicoes) e `23.22.63.114`. Cruzando o IP de maior volume com a fonte do IDS Suricata, validou-se o alerta de varredura automatizada:

```spl
index=botsv1 imreallynotbatman.com src=40.80.148.42 sourcetype=suricata

```

### Descobertas Criticas

1. **Scanner Web do Atacante:** Identificado como `acunetix` atraves da inspecao de padroes no campo User-Agent e alertas de automacao.
2. **Assinatura de Vulnerabilidade (CVE):** Detectado o alerta associado a vulnerabilidade `CVE-2014-6271` (Shellshock).
3. **Fingerprinting do Alvo:** O servidor alvo opera o CMS `joomla` sob o endereco IP interno `192.168.250.70`.

### Licoes Aprendidas

* **Identificacao de Ruido vs. Ataque:** Scanners de vulnerabilidade geram um volume massivo de requisicoes em um curto espaco de tempo, tornando o pico de contagem de eventos no campo `src_ip` um forte indicador inicial de reconhecimento.
* **Uso de IDS para Contexto:** Cruzar o IP suspeito com logs do Suricata permite transformar uma simples requisicao HTTP em um alerta correlacionado com uma vulnerabilidade critica.

---

## 3. Fase de Exploracao

* **Etapa da Kill Chain:** Exploracao (Exploitation)
* **Objetivo:** Analisar a atividade de forca bruta contra o painel administrativo do CMS Joomla e verificar se o atacante obteve sucesso.

### Estrategia de Busca e Extracao no Splunk

Para identificar a tentativa de invasao, foram filtradas requisicoes do tipo `POST` direcionadas a pagina de administracao do CMS, utilizando expressoes regulares (`rex`) para expor as credenciais testadas em texto claro.

```spl
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" http_method=POST form_data=*username*passwd* | rex field=form_data "passwd=(?<creds>\w+)" 
| table _time src_ip uri http_user_agent creds

```

### Analise do Ataque (Forca Bruta)

Os logs revelaram um ataque automatizado de dicionario focado no usuario `admin`. Foram identificados dois comportamentos distintos com base no campo `http_user_agent`:

1. **Ataque Automatizado:** Alto volume de tentativas sequenciais (totalizando 412 senhas unicas testadas) vindas de um script com a assinatura `Python-urllib/2.7`.
2. **Acesso Manual:** Mudanca repentina de comportamento para um navegador legitimo (`Mozilla/5.0`) originada do mesmo IP.

### Descobertas Criticas

* **IP do Atacante (Forca Bruta):** `23.22.63.114`
* **Indicador de Sucesso:** O script em Python cessou logo apos testar uma credencial especifica. Na sequencia, o atacante realizou o login manual com sucesso.
* **Senha Correta Identificada:** `batman`

### Licoes Aprendidas

* **Normalizacao de Dados:** Dados criticos (como senhas em requisicoes POST) frequentemente ficam ocultos dentro de strings longas (`form_data`). O uso de comandos como `rex` (Regex) no Splunk e indispensavel para triagem rapida.
* **Mudanca de Assinatura (User-Agent):** Monitorar a alteracao repentina de agentes de usuario vindos de um mesmo IP externo e um forte indicador de sucesso de invasao.

---

## 4. Fase de Instalacao

* **Etapa da Kill Chain:** Instalacao (Installation)
* **Objetivo:** Identificar o upload de artefatos maliciosos (payloads) no servidor comprometido e comprovar sua execucao utilizando telemetria de host (Sysmon).

### Estrategia de Busca e Analise Forense

A investigacao migrou da camada de rede para a analise centrada no host (Endpoint Detection). Primeiro, buscou-se por transferencias de arquivos executaveis via HTTP e, em seguida, correlacionou-se o binario com os eventos do Sysmon.

```spl
index=botsv1 sourcetype=stream:http dest_ip="192.168.250.70" *.exe

```

```spl
index=botsv1 "3791.exe" sourcetype="XmlWinEventLog" EventCode=1

```

### Descoberta e Analise do Payload

A analise do campo `part_filename{}` no trafego HTTP revelou que o atacante carregou um script PHP (`agent.php`) e um executavel binario (`3791.exe`). Ao aplicar o filtro pelo `EventCode=1` do Sysmon (Process Creation), confirmou-se de forma inequivoca que o binario foi executado no sistema operacional.

### Descobertas Criticas e Enriquecimento (CTI)

1. **Contexto de Execucao no Servidor:** O malware rodou sob o contexto de privilegios do usuario `NT AUTHORITY\IUSR`.
2. **Assinatura e OSINT (VirusTotal):** O hash MD5 extraido do binario foi `AAE3F5A29935E6ABCC2C2754D12A9AF0`. Ao cruzar esse hash com a plataforma VirusTotal, a ameaca foi identificada pela comunidade de seguranca sob o nome de `ab.exe` (vinculada a ferramentas de Command & Control).

### Licoes Aprendidas

* **A Importancia do Sysmon:** Logs de servidores web ou firewalls apenas provam que um arquivo foi transferido. A confirmacao absoluta de um comprometimento de host (Execution) so e obtida atraves de logs de criacao de processos.
* **Enriquecimento com OSINT:** Extrair hashes criptograficos do SIEM e consulta-los em bases de CTI agiliza drasticamente a classificacao e a resposta a incidentes.

---

## 5. Comando e Controle (C2) e Acoes nos Objetivos

* **Etapas da Kill Chain:** Comando e Controle (C2) e Acoes nos Objetivos (Actions on Objectives)
* **Objetivo:** Identificar o dominio de C2 utilizado para persistencia e o artefato final que causou a desfiguracao do site.

### Estrategia de Busca e Inversao de Fluxo

A investigacao identificou uma anomalia critica: o servidor web atuou como origem de trafego de saida (egress traffic), iniciando conexoes externas para buscar recursos.

```spl
index=botsv1 src=192.168.250.70 sourcetype=suricata

```

```spl
index=botsv1 url="*poisonivy*" | table _time src dest_ip http.hostname url

```

### Analise do Impacto (O Defacement)

Os logs do `fortigate_utm` e do `stream:http` registraram o momento exato em que o servidor web comprometido efetuou uma requisicao de saida para o IP `23.22.63.114` na porta `1337`. A conexao realizou o download de um arquivo de imagem hospedado em um servico de DNS Dinamico, que substituiu a interface legitima da empresa.

### Descobertas Criticas

* **Dominio de C2 (FQDN):** `prankglassinebracket.jumpingcrab.com`
* **Artefato de Desfiguracao:** `/poisonivy-is-coming-for-you-batman.jpeg`
* **Alerta do Firewall Perimetral:** O IPS do firewall Fortigate gerou o alerta historico da regra `HTTP.URI.SQL.Injection` vindo do IP de reconhecimento primario (`40.80.148.42`).

---

## 6. Inteligencia de Ameacas Avancada (Weaponization & Delivery)

Fora do SIEM, utilizando analise reversa e consultas na aba *Relations* de ferramentas OSINT (como Robtex e VirusTotal) a partir do IP do C2 (`23.22.63.114`), foram mapeados os passos de preparacao do adversario:

### Atribuicao de Infraestrutura Previa

* **Typosquatting/Spoofing:** O atacante havia registrado multiplos dominios falsos imitando a identidade da Wayne Enterprises para campanhas de Phishing.
* **Conexao Historica:** O grupo associou o IP ao dominio malicioso `www.po1s0n1vy.com`.
* **Vetor de Contingencia (Delivery):** Foi identificado em sandbox (Hybrid Analysis) o hash de um artefato secundario (`c99131e0169...`), demonstrando que o grupo mantinha um plano B de infeccao via e-mail/phishing caso a exploracao web falhasse.

---

## 7. Linha do Tempo Cronologica do Incidente

| Fase da Kill Chain | Indicador / Evidencia | Descricao Tecnica |
| --- | --- | --- |
| **1. Reconhecimento** | IP `40.80.148.42` | Varredura automatizada com o scanner Acunetix e geracao de alertas Shellshock (CVE-2014-6271). |
| **2. Armamentizacao** | IP `23.22.63.114` | Registro de dominios maliciosos de infraestrutura e preparacao do payload de contingencia (`c99131e0169`). |
| **3. Entrega** | Dominio Falsificado | Campanha paralela de mapeamento e entrega direcionada contra ativos da organizacao. |
| **4. Exploracao** | IP `23.22.63.114` | Ataque de forca bruta (412 tentativas) contra o CMS Joomla. Autenticacao bem-sucedida com a senha `batman`. |
| **5. Instalacao** | Arquivo `3791.exe` | Upload dos artefatos `agent.php` e `3791.exe`. Execucao confirmada via Sysmon Event ID 1 (IUSR). |
| **6. Comando & Controle** | Porta `1337` | Servidor web inicia conexao de saida para o FQDN `prankglassinebracket.jumpingcrab.com`. |
| **7. Acoes nos Objetivos** | Imagem `.jpeg` Maliciosa | Download do arquivo `poisonivy-is-coming-for-you-batman.jpeg` para execucao do defacement do site. |

---

## Conclusao Tecnica

O incidente confirmou uma falha na postura de seguranca da organizacao envolvendo o gerenciamento de credenciais administrativas do CMS Joomla (vulneravel a forca bruta) e a ausencia de politicas de controle de trafego de saida (Egress Filtering), o que permitiu ao servidor DMZ comunicar-se livremente com a internet para baixar o payload.

A centralizacao de dados no Splunk, permitindo a correlacao rapida entre logs de rede (Suricata/Fortigate), telemetria de endpoint (Sysmon Event ID 1) e bases de Inteligencia de Ameacas externas, foi o fator determinante para o mapeamento completo, contencao e mitigacao do incidente.
