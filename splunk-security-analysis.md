# Análise de Logs e Investigação de Segurança com Splunk (Blue Team)

## Objetivo
Este projeto documenta a aplicação prática da linguagem **SPL (Search Processing Language)** para investigar comportamentos anômalos em um ambiente Windows. O foco foi a utilização do Splunk como ferramenta de SIEM para monitoramento, correlação e caça a ameaças (Threat Hunting).

## Cenário
O laboratório consiste em uma infraestrutura simulada contendo máquinas Windows, com o Splunk Universal Forwarder configurado para enviar logs de segurança e Sysmon para um servidor Splunk Enterprise.

---

## Ferramentas e Tecnologias
* **SIEM:** Splunk Enterprise
* **Linguagem:** SPL (Search Processing Language)
* **Fontes de Dados:** Windows Security Logs, Sysmon

---

## Metodologia de Investigação

### 1. Filtragem e Estruturação de Dados
Utilização de comandos de refinação para reduzir o ruído visual e focar em eventos críticos.

**Consulta:**
```splunk
index=windowslogs | table _time, EventID, Hostname, SourceName, User
2. Análise de Execução de Processos
Análise da frequência de binários executados para identificar anomalias (Threat Hunting).

Consulta (Processos mais comuns):

Snippet de código
index=windowslogs | top limit=10 Image
Figura 1: Distribuição de binários executados no index=windowslogs.

3. Análise Temporal (Timechart)
Utilização do comando timechart para criar gráficos de linha do tempo, permitindo visualizar a frequência de execução de binários ao longo do tempo.

Consulta Refinada:

Snippet de código
index=windowslogs | timechart span=1mon count by Image
Figura 2: Linha do tempo de atividade de processos (binários) por tempo.

Conclusão
A correta utilização da SPL permitiu transformar logs brutos em visualizações claras. O uso do timechart foi fundamental para identificar picos de atividade suspeita em meses específicos, demonstrando capacidade de análise forense e monitoramento contínuo.
