# 🛡️ Threat Hunting & Incident Response: Boss of the SOC (BOTS) v2

> **Investigação de Segurança (Blue Team) baseada na série Blue Primer e no conjunto de dados BOTSv2 do Splunk.**

Este repositório contém a documentação técnica de uma investigação progressiva em um cenário de incidente simulado na empresa "Frothly". A análise cobre o ciclo completo de um ataque cibernético, desde o mapeamento inicial de ameaças até a identificação de táticas avançadas de persistência (APTs) utilizando o **Splunk** como principal ferramenta SIEM.

---

## 🎯 Objetivo do Projeto
Demonstrar a aplicação prática de técnicas de **Threat Hunting** e **Resposta a Incidentes**, transformando dados brutos de telemetria em inteligência acionável. O escopo abrange o rastreamento de navegação anônima, ataques a aplicações web, *spear phishing*, infecções via dispositivos físicos (USB) e comunicação de malwares com infraestruturas de Comando e Controle (C2).

## 🛠️ Tecnologias e Arsenal Utilizado
* **SIEM:** Splunk (Search Processing Language - SPL)
* **Endpoint Telemetry:** Osquery
* **Threat Intelligence & Sandboxing:** VirusTotal, Hybrid Analysis, Any.run
* **Análise de Redes:** Logs SMTP, Tráfego HTTP/HTTPS, Fluxos TCP e metadados SSL
* **Frameworks:** MITRE ATT&CK, Cyber Kill Chain

---

## 📂 Estrutura da Investigação

### 1. Reconhecimento e Rastreio de Comunicação Corporativa
* Uso do comando `metadata` e `eval` (conversão de tempo EPOCH) para mapear a visibilidade do ambiente.
* Rastreio de atividades internas (usuária Amber Turing) envolvendo espionagem corporativa, cruzando logs de tráfego de firewall (`pan:traffic`), navegação web (`stream:http`) e exfiltração de dados por e-mail (`stream:smtp`).

### 2. Série 200: Rastreamento de Ataques Web e Phishing
* Identificação de instalação de softwares de anonimato (Tor).
* Mapeamento de escaneamento de vulnerabilidades web e injeção de função SQL maliciosa (`updatexml`).
* Investigação de roubo de cookies via payload **XSS** e identificação de contas criadas maliciosamente via *spear phishing*.

### 3. Série 300: Ransomware e Infecção via Hardware
* Investigação de criptografia de arquivos corporativos por ransomware.
* Análise de logs do sistema operacional via **Osquery** para mapear o vetor inicial: um *pen drive* (USB) malicioso.
* Correlação temporal (janelas de tempo adjacentes) para identificar o hardware anômalo e rastreio de destinos DNS dinâmicos utilizados pelo malware para C&C.

### 4. Série 400: Análise de APT (Taedonggang)
* Rastreio de campanhas de *Spear Phishing* utilizando arquivos zip protegidos por senha.
* Interceptação e análise de fluxos TCP para identificação do "Emissor SSL" utilizado nas comunicações criptografadas do grupo criminoso.
* Investigação profunda utilizando Sandboxes externas (Any.run, VirusTotal) para extração de metadados e comportamento pós-exploração (ex: *PowerShell Empire*).
* Descoberta de mecanismos de **Persistência** via *Scheduled Tasks* (`schtasks.exe`) configurados para *beacons* constantes de C2.

---

## 📊 Considerações Finais
A resolução do conjunto de dados BOTSv2 proporcionou uma imersão técnica no fluxo de trabalho de um analista SOC. O laboratório consolidou a habilidade de correlacionar logs de múltiplas fontes, utilizar operadores booleanos avançados e compreender TTPs complexos. 

Identificar IOCs cruciais — como um emissor SSL específico ou caminhos anômalos de persistência — prova que a união entre **telemetria interna (SIEM)** e **inteligência de ameaças externa** é o pilar para uma Defesa Cibernética eficiente.
