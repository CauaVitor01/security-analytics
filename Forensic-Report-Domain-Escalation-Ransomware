# Incident Response: Malware Infection & Domain Escalation

Este repositório documenta a investigação detalhada de um incidente de segurança iniciado por **Social Engineering (Phishing)**, que resultou em movimentação lateral, escalonamento de privilégios de Administrador de Domínio e implantação de **Ransomware**.

> 📂 **[ACESSAR RELATÓRIO TÉCNICO COMPLETO (PDF)](./Forensic_Report_Domain_Escalation_Ransomware.pdf)**
>
> ⚠️ **DISCLAIMER:** Este material contém Indicadores de Comprometimento (IoCs) de artefatos maliciosos reais analisados em ambiente controlado.

---

## Resumo da Investigação (Caso Perry)

O incidente teve início em **26 de setembro de 2023**, quando um usuário baixou um software descompactador (`7-zip`) de uma fonte não oficial. A análise via **Elastic SIEM** permitiu rastrear toda a cadeia de ataque (*Kill Chain*).

### Linha do Tempo do Ataque

1.  **Acesso Inicial (T1566):** Download do malware `7z2301-x64.msi` via domínio falso `www.7zipp.org`.
2.  **Persistência (T1543.003):** Criação do serviço `7zService` rodando como `SYSTEM` para manter controle total da máquina infectada.
3.  **Extração de Credenciais (T1003):** Utilização do script `Invoke-PowerExtract` para coleta de hashes e redefinição de senhas de contas locais.
4.  **Movimentação Lateral (T1021):** Pivoteamento para a estação de trabalho `WKSTN-02`.
5.  **Domínio Comprometido (T1484):** Execução do `Invoke-SharpKatz.ps1` para obter o hash **AES256** do administrador do domínio.
6.  **Impacto Final (T1486):** Criptografia de **46 arquivos** sensíveis em múltiplas estações de trabalho.

---

## Tecnologias e Ferramentas Utilizadas

* **Elastic Stack (ELK):** Monitoramento de logs de endpoint e telemetria de rede.
* **Sysmon:** Identificação de criação de processos e conexões de rede suspeitas.
* **PowerShell Logging:** Análise de scripts ofuscados e comandos `iex` (Invoke-Expression).
* **Análise Forense:** Recuperação de senhas redefinidas e mapeamento de hashes AES256.

---

## Principais Indicadores de Comprometimento (IoCs)

| Ativo | Detalhe Identificado |
| :--- | :--- |
| **Malware URL** | `hxxp://www.7zipp.org/a/7z2301-x64.msi` |
| **C2 IP** | `206.189.34.218` |
| **Script Malicioso** | `7z.ps1` (Executado via PowerShell IEX) |
| **Admin Hash** | `f28a16b8d3f5163cb7a7f7ed2c8f2cf0419f0b0c2e28c15f831d050f5edaa534` |

---

## Analista Responsável

* **Nome:** Cauã
* **Data do Relatório:** 14 de Março de 2026
* **Especialidade:** SOC Tier 2 / Threat Hunting
