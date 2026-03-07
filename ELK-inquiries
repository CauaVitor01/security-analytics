# Investigação Forense e Análise de Dados no Kibana (SIEM)

Este repositório documenta a implementação de técnicas avançadas de busca e correlação de eventos utilizando o **Kibana**. O foco é a extração de inteligência de ameaças e a resolução de incidentes complexos através de linguagens de consulta **KQL** e **Lucene**.

---

## 1. Consultas em Dados Aninhados (JSON)
Investigação de campos estruturados sem a necessidade de analisadores externos. Foco no campo `comments` (matriz de objetos).

* **Curinga:** `comments.author:*` (Verifica existência).
* **Filtro Exato:** `comments.author:"Alice"`.
* **Lógica Composta:** `comments.author:"Alice" AND comments.text:attack`.



---

## 2. Análise por Intervalo (Range Queries)
Filtragem baseada em métricas de desempenho e severidade utilizando operadores matemáticos (`>=`, `<=`, `>`, `<`).

* **Métrica de Resposta:** `response_time_seconds >= 100 AND response_time_seconds < 300`.
* **Investigação de Severidade:** `incident_type: "Data Leak" AND severity >= 9`.
* **Filtro Temporal:** `@timestamp < "2023-12-31T23:59:59Z"`.



---

## 3. Busca Aproximada (Fuzzy Search)
Técnica utilizada para mitigar erros de digitação e inconsistências nos logs, baseada na distância de Levenshtein.

* **Configuração:** Requer alternância para a sintaxe **Lucene**.
* **Aplicação:** `host_name:server01~1` (Captura "server01" e "serber01").
* **Caso Prático:** Identificação de erros ortográficos do analista `JLim` em termos críticos como "true" via `incident_comments:true~1`.



---

## 4. Busca por Proximidade (Proximity)
Recuperação de termos relacionados dentro de uma distância específica (**Slop**).

* **Sintaxe:** `log_message:"server error"~4`.
* **Investigação Forense:** Localização de falsos positivos em vazamentos de dados onde os termos "data leak" e "true negative" estão separados por até 3 palavras:
    `incident_comments :"data leak true negative"~3`

---

## 5. Expressões Regulares (Regexp)
Uso de padrões complexos para identificação de vetores de ataque e arquivos específicos.

* **Campos Keyword vs Text:** Diferenciação entre busca exata e busca tokenizada.
* **Detecção de Ransomware:** Localização de incidentes onde arquivos de lista de clientes foram afetados:
    `type:/ransomware/ AND affected_files:/client_list.*/`



---

##  Metodologia Aplicada
Todas as investigações seguiram o Protocolo Operacional Padrão:
1.  **Seleção de Índice:** Alvo nos índices `incidents`, `ranges` e `fuzzy-searches`.
2.  **Configuração Temporal:** Uso de janelas absolutas (Jan 2022 - Now).
3.  **Refinamento de Sintaxe:** Alternância estratégica entre KQL (nativo) e Lucene (avançado).

##  Impacto Operacional
A maestria sobre estas técnicas reduz drasticamente o **MTTR (Mean Time to Respond)**, permitindo que o analista de SOC encontre "a agulha no palheiro" em ambientes com milhões de eventos diários.

---
**Analista Responsável:** [Seu Nome]
**Ferramentas:** Kibana (ELK Stack), Sintaxe Lucene, KQL.
