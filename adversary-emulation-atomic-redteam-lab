# Adversary Emulation & Atomic Detection Lab

![MITRE ATT&CK](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red)
![Purple Team](https://img.shields.io/badge/Focus-Detection%20Engineering-blue)
![Lab](https://img.shields.io/badge/Environment-TryHackMe-green)

---

## Visão Geral

Este projeto documenta a execução de testes de **Emulação de Adversários** utilizando o **Atomic Red Team**, com foco em **detecção, visibilidade e resposta**.

A proposta é simular comportamentos reais de atacantes (TTPs) com base no framework **MITRE ATT&CK**, validando a eficácia de ferramentas como:

- Sysmon (telemetria avançada)
- Aurora EDR (correlação e detecção)
- Windows Event Logs (investigação)

---

## Objetivos

- Validar capacidade de detecção (SOC / Blue Team)
- Identificar gaps de visibilidade
- Simular ataques reais de forma controlada
- Gerar insights práticos de DFIR e Threat Hunting

---

## Metodologia

A abordagem segue princípios de:

- 🔴 Red Team → Simulação de ataque
- 🔵 Blue Team → Monitoramento e detecção
- 🟣 Purple Team → Validação e melhoria contínua

---

## Ferramentas Utilizadas

- Atomic Red Team
- Sysmon
- Aurora EDR
- Event Viewer
- PowerShell
- THM-Utils

---

## Matriz de Testes

| ID  | Tática                | Técnica MITRE | Descrição |
|-----|----------------------|--------------|----------|
| #2  | Credenciais          | T1552.001 / T1078.003 | Busca de credenciais e criação de conta clone |
| #4  | Execução / Descoberta| T1056.002 / T1082 | Enumeração e captura de entrada |
| #5  | Movimentação Lateral | T1091        | Manipulação de arquivos SMB |
| #6  | Coleção              | T1115        | Dump de dados e clipboard |

---

## Arquitetura de Investigação

### Visibilidade
- Sysmon → Processos, conexões, arquivos
- Event Viewer → Logs brutos
- Aurora EDR → Correlação com regras Sigma

### Abordagem
- Análise baseada em artefatos
- Observação de comportamento (behavior-based)
- Correlação de eventos

---

## Estrutura do Laboratório
