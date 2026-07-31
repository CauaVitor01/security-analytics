# Fase 2: Identificacao e Definicao de Escopo

> **Nota de Documentacao:** Este arquivo Markdown apresenta o detalhamento do processo de detecao, validacao e analise da extensao de incidentes de seguranca, utilizando uma abordagem orientada por inteligencia. Para a analise completa e diagramas visuais, consulte o arquivo PDF (`Identification_and_scoping.pdf`) disponivel neste repositorio.

---

## 1. Introducao e Valor Estrategico

A Identificacao e o ponto de transicao onde um evento digital e validado como um incidente real.

* **Detecao Celere:** A rapidez nesta fase e o fator determinante para limitar danos e reduzir o tempo de recuperacao (MTTR - *Mean Time to Recovery*).

---

## 2. A Triade da Identificacao

A eficacia da detecao depende da harmonia entre tres pilares fundamentais:

| Pilar | Descricao | Ferramentas/Acoes |
| :--- | :--- | :--- |
| **Tecnologia** | Geracao de alertas e visibilidade tecnica. | Aurora EDR, Sysmon, Snort. |
| **Processos** | Fluxo de triagem e escalonamento. | Triagem -> Validacao -> Notificacao CSIRT. |
| **Pessoas** | Interpretacao humana e tomada de decisao. | Analistas de SOC (N1/N2/N3). |

---

## 3. Visibilidade Tecnica e Ferramental

Para garantir que nenhum ponto cego persista na infraestrutura, as seguintes solucoes foram implementadas e dominadas:

* **EDR (Endpoint Detection & Response):** Aurora EDR e Wazuh para monitorizacao profunda de hosts.
* **IDPS (Intrusion Detection/Prevention):** Snort para analise de trafego de rede e assinaturas maliciosas.
* **SIEM (Security Information & Event Management):** Splunk e ELK Stack para correlacao de logs centralizada.

---

## 4. Definicao de Escopo e Analise de Impacto

Utilizamos o Inventario de Ativos e a Planilha da Perdicao (SoD) para correlacionar Indicadores de Comprometimento (IoCs) com ativos criticos.

### Inventario de Ativos (Alvos Monitorados)

| Ativo | Endereco IP | OS | Proprietario |
| :--- | :--- | :--- | :--- |
| **DC-01** | 172.16.1.10 | Windows Server 2019 | Derick Marshall |
| **MAILSVR-01** | 172.16.1.15 | Windows Server 2019 | Stan Simon |
| **WKSTN-02** | 172.16.1.151 | Windows 10 Pro | Michael Ascot |

### Enriquecimento de Artefatos (Planilha da Perdicao - SoD)

A SoD acelera a resposta ao identificar Indicadores de Comprometimento (IoCs) conhecidos:

* **IP Malicioso:** `188.40.75.132` (Hospedagem de Malware).
* **Dominio Phishing:** `b24b-158-62-19-6.ngrok-free.app` (Infraestrutura Ngrok).
* **Hash (SHA1):** `75ec7d0d1b6b2b4c816cbc1b71cd0f8f06bd8c1b` (Malware Payload).

---

## 5. Ciclo de Feedback Orientado por Inteligencia

A investigacao nao e linear. Ela segue um ciclo iterativo de refinamento constante:

1. **Notificacao:** Gatilho inicial do incidente.
2. **Documentacao:** Registo detalhado para base da investigacao.
3. **Coleta de Evidencias:** Extracao de logs, trafego e artefatos volateis.
4. **Identificacao de Artefatos:** Isolamento de IoCs.
5. **Ponto de Pivo:** Descoberta de novas areas (ex: movimento lateral), forcando o retorno a fase de documentacao para expandir o escopo da investigacao original.

---

## 6. Conclusao

A identificacao precisa permite que a expertise tecnica seja aplicada no momento certo, assegurando que o incidente seja gerido com precisao e que as partes interessadas sejam notificadas prontamente. O sucesso da triagem inicial define diretamente a eficacia da proxima fase do ciclo de vida de resposta: a Contencao.
