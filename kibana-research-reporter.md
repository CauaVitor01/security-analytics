# Project CyberHunt: Full-Spectrum Incident Investigation with Kibana

Este repositório contém a documentação técnica de uma investigação de segurança de ponta a ponta. O projeto simula o fluxo de trabalho de um **Analista de SOC / Threat Hunter**, utilizando o **Elastic Stack (Kibana)** para identificar, correlacionar e mitigar ameaças baseadas no framework **MITRE ATT&CK**.

---

## Escopo e Objetivos
O objetivo principal foi realizar o "Hunting" de atividades maliciosas ocorridas em **03/07/2023**, cobrindo todo o ciclo de vida de um ataque (*Kill Chain*), desde a entrada inicial até o estabelecimento do canal de Comando e Controle (C2).

---

## Stack Tecnológica
* **SIEM:** Elastic Stack (Kibana)
* **Logs de Endpoint:** Winlogbeat & Sysmon (Event IDs 1, 3, 8, 11, 13, 4104, 4698)
* **Logs de Rede:** Packetbeat (DNS, HTTP, ICMP)
* **Logs de Autenticação:** Filebeat (SSH/Auth logs)
* **Framework de Referência:** MITRE ATT&CK

---

## Resumo da Investigação por Tática

### 1. Acesso Inicial ([TA0001](https://attack.mitre.org/tactics/TA0001/))
Identificação dos pontos de ruptura do perímetro:
* **Força Bruta SSH:** Detecção de ataque de dicionário no *Jumphost* originado do IP `167.71.198.43`.
* **Web RCE:** Exploração de vulnerabilidade PHP no endpoint `/gila` do servidor `Web01`.
* **Phishing:** Execução de arquivos `.hta` e anexos `.zip` maliciosos via Outlook e Chrome.

### 2. Execução ([TA0002](https://attack.mitre.org/tactics/TA0002/))
Análise de como o código malicioso operou no ambiente (**LOLBAS**):
* Uso de **PowerShell** para carregar o agente C2 Empire.
* Execução de **Shell Reverso em Python** (`dev.py`) para controle remoto.
* Uso de utilitários nativos (`Certutil`, `Mshta`) para baixar e rodar payloads.

### 3. Evasão Defensiva ([TA0005](https://attack.mitre.org/tactics/TA0005/))
Manobras para evitar detecção e neutralizar a equipe de resposta:
* **Antivírus:** Desativação do Windows Defender via `Set-MpPreference`.
* **Log Clearing:** Limpeza dos registros de eventos do Windows (**Event ID 1102**).
* **Process Injection:** Injeção de shellcode do `chrome.exe` no `explorer.exe` (**Sysmon ID 8**).

### 4. Persistência ([TA0003](https://attack.mitre.org/tactics/TA0003/))
Garantia de que o acesso sobreviva a reinicializações ou logouts:
* **Scheduled Tasks:** Criação da tarefa "Windows Update" disparando scripts maliciosos a cada minuto.
* **Registry Run Keys:** Modificação da chave `RunOnceEx` para carregar o binário `installer.exe`.

### 5. Comando e Controle - C2 ([TA0011](https://attack.mitre.org/tactics/TA0011/))
Canais de comunicação utilizados para exfiltração de dados e recebimento de ordens:
* **DNS Tunneling:** Uso de consultas DNS para o domínio `golge.xyz` para trafegar dados ocultos.
* **Cloud C2:** Abuso da infraestrutura do **Discord** para camuflar o tráfego malicioso.
* **HTTP Beaconing:** Requisições recorrentes para `cdn.golge.xyz` utilizando criptografia customizada.

---
**Documentação produzida como parte de treinamento prático em SOC e Threat Hunting.**
**Feito por: Cauã**
