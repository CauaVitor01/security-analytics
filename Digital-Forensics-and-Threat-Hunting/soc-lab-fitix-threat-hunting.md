# Engenharia de Dados e Normalização de Logs com Splunk

## Objetivo
Expertise no gerenciamento do pipeline de dados do Splunk, atuando na normalização de logs não estruturados e no desenvolvimento de extrações de campos (Parsing) para maximizar a eficiência de buscas analíticas e correlações de eventos.

## Descrição do Projeto
Este projeto apresenta a resolução de desafios críticos de engenharia de dados dentro de um ambiente SIEM. O foco principal foi a manipulação de logs proprietários e a transformação de dados brutos em inteligência acionável, utilizando arquivos de configuração do backend do Splunk e expressões regulares avançadas.

---

## Tecnologias e Ferramentas
* **SIEM:** Splunk Enterprise
* **Linguagem:** SPL (Search Processing Language)
* **Arquivos de Configuração:** `props.conf`, `transforms.conf`, `fields.conf`
* **Processamento:** Regex (Expressões Regulares), Event Breaking, Data Anonymization

---

## Implementação Técnica: Desafio FIXIT

### 1. Correção de Limites de Eventos (Event Breaking)
Identificação e correção de falhas na ingestão de dados onde o Splunk não reconhecia o início de novos logs, agrupando múltiplos registros de forma incorreta. A solução foi aplicada via `props.conf` para isolar cada entrada baseada no padrão específico dos logs de rede.

**Configuração Técnica (`props.conf`):**
```ini
[network_logs]
SHOULD_LINEMERGE = false
LINE_BREAKER = ([\r\n]+)\[Network-log\]
TIME_PREFIX = at:\s
2. Extração de Campos Customizados (Parsing)
Desenvolvimento de regras de extração para converter texto bruto em metadados estruturados. Essa etapa é fundamental para permitir que a equipe de segurança realize filtros granulares por usuários, departamentos e endereços IP.

Configuração Técnica (transforms.conf):

Ini, TOML
[network_fields_extraction]
REGEX = User\snamed\s(?<username>.*?)\sfrom\s(?<department>.*?)\sdepartment.*?source\sIP\s(?<source_ip>[\d\.]+)\sand\scountry\s(?<country>.*?)\sat:
FORMAT = username::$1 department::$2 source_ip::$3 country::$4
WRITE_META = true
3. Otimização de Performance (Indexed Fields)
Para garantir que as buscas em grandes volumes de dados sejam executadas com alta performance, os campos críticos foram configurados como campos indexados.

Configuração Técnica (fields.conf):

Ini, TOML
[username]
INDEXED = true

[source_ip]
INDEXED = true
Automação e Monitoramento Proativo
Detecção de Força Bruta e Alertas
Além da estruturação dos dados, foi implementado um fluxo de monitoramento automático para identificar comportamentos anômalos em tempo real.

Lógica de Detecção: Identificação de volumes excessivos de tentativas de login (mais de 5 ocorrências por hora) para usuários específicos.

Ação de Resposta: Configuração de alertas agendados com notificações automáticas via e-mail para a equipe de resposta a incidentes (SOC), reduzindo a necessidade de verificação manual.

Segurança e Conformidade (Data Anonymization)
Implementação de técnicas de mascaramento de dados sensíveis utilizando SEDCMD. Esta configuração garante que informações privadas sejam anonimizadas antes da indexação, mantendo a conformidade com normas de privacidade sem comprometer a capacidade de análise técnica.

Conclusão
A capacidade de refinar o pipeline de dados garante que o SIEM forneça visibilidade total e precisão na detecção de ameaças. Este projeto valida competências essenciais de um analista de segurança e engenheiro de dados, desde a manipulação de arquivos de sistema até a criação de monitoramento estratégico.

Documentação Adicional:

Guia de Manipulação de Dados (PDF)

Relatório de Automação de Segurança (PDF)
