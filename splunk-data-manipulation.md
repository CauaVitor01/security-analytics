# Splunk Data Engineering & Manipulation Project

Este repositório documenta procedimentos técnicos avançados para manipulação, estruturação e segurança de dados dentro da plataforma **Splunk**. O objetivo é demonstrar competência na administração de pipelines de dados, garantindo conformidade, performance e visibilidade para equipes de SOC (Security Operations Center).

##  Tecnologias e Conceitos

* **Plataforma:** Splunk Enterprise / Universal Forwarder
* **Linguagens:** Python 3 (geração de logs), RegEx (Expressões Regulares)
* **Arquivos de Configuração (.conf):** `props.conf`, `transforms.conf`, `inputs.conf`, `indexes.conf`, `fields.conf`
* **Conceitos:** Sourcetyping, Event Breaking, Field Extraction, Mascaramento de Dados (Anonymization).

---

## 📋 Conteúdo Técnico

### 1. Gestão de Parsing e Extração de Campos (`props.conf`)
[cite_start]Demonstração de como estruturar logs brutos e criar extrações de campos customizadas para otimizar buscas e correlações[cite: 2, 4].

* [cite_start]**Procedimento:** Mapeamento de `sourcetype`, implementação de Regex para captura de dados e validação via SPL[cite: 8, 9, 11].
* [cite_start]**Exemplo:** `EXTRACT-user_id=userID=(?<user_id>\d+)`[cite: 10].

### 2. Controle do Ciclo de Vida dos Dados
[cite_start]Administração avançada do fluxo de dados, da coleta ao armazenamento[cite: 19, 20].

* [cite_start]**Inputs:** `inputs.conf` (coleta)[cite: 28].
* [cite_start]**Parsing:** `props.conf` (interpretação)[cite: 29].
* [cite_start]**Transformação:** `transforms.conf` (mascaramento/roteamento)[cite: 29].
* [cite_start]**Retenção:** `indexes.conf` (armazenamento)[cite: 31].
* [cite_start]**Encaminhamento:** `outputs.conf` (envio para Indexers)[cite: 32].

### 3. Desenvolvimento de Aplicação Customizada
[cite_start]Criação de um Splunk App (`DataApp`) para automatizar a ingestão de dados via script Python[cite: 43, 46].

* [cite_start]**Estrutura:** `bin/`, `default/`, `local/`, `metadata/`[cite: 54].
* [cite_start]**Automação:** Uso de `inputs.conf` tipo script para execução periódica[cite: 46, 58].

### 4. Solução de Problemas de Parsing (Event Breaking)
[cite_start]Correção de logs onde múltiplos eventos são incorretamente indexados como um único registro ou onde eventos multilinha são separados[cite: 78, 117].

* [cite_start]**Técnicas:** Uso de `LINE_BREAKER`, `SHOULD_LINEMERGE = true`, `MUST_BREAK_AFTER` e `BREAK_ONLY_BEFORE`[cite: 35, 101, 143].

### 5. Segurança e Mascaramento de Dados (Compliance)
[cite_start]Implementação de controles para proteger dados sensíveis (PCI DSS/HIPAA)[cite: 157, 159].

* [cite_start]**Técnica:** Uso de `SEDCMD` no `props.conf` para anonimizar números de cartão de crédito (CC) antes da indexação[cite: 197, 203].

### 6. Extração de Campos Customizados (`transforms.conf`)
[cite_start]Transformação de logs não estruturados em campos pesquisáveis, melhorando a performance de dashboards e relatórios[cite: 217, 220].

* [cite_start]**Técnica:** Vinculação de `transforms.conf` a `props.conf` e uso de `fields.conf` para criar campos indexados[cite: 230, 232, 233].

---

##  Conclusão

[cite_start]O domínio dos arquivos de configuração (`.conf`) é a base da engenharia de dados no Splunk[cite: 41]. [cite_start]Esta competência garante visibilidade total sobre fontes de dados proprietárias, aumenta a capacidade de detecção de ameaças e assegura a conformidade com políticas de retenção e privacidade[cite: 16, 42, 216].

---
*Documentação baseada no manual de procedimentos internos de manipulação de dados Splunk.*
