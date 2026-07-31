# Relatorio de Investigacao SOC: Analise de Persistencia e Atividade Maliciosa em Windows

> **Nota de Documentacao:** Este arquivo Markdown apresenta um resumo executivo dos principais Indicadores de Comprometimento (IoCs) e resultados consolidados do incidente. Para uma analise visual detalhada, incluindo as capturas de tela (prints) das consultas e logs extraidos do Splunk SIEM, consulte o documento em formato PDF de mesmo nome disponivel neste repositorio.

---

## 1. Visao Geral do Cenario
O analista de SOC Johny reportou comportamentos severamente anomalos na telemetria de multiplos hosts baseados no sistema operacional Windows. Evidencias iniciais apontam para um comprometimento bem-sucedido do perimetro com acoes pos-exploracao focadas na criacao de mecanismos de persistencia direta (backdoors) e execucao de comandos maliciosos via PowerShell.

O objetivo desta investigacao consistiu em analisar os logs consolidados no indice principal do Splunk para isolar os artefatos tecnicos, determinar as contas afetadas e reconstruir as conexoes e taticas empregadas pelo adversario.

---

## 2. Investigacao de Contas e Tecnicas de Persistencia

A criacao de contas locais ou de dominio arbitrárias e uma tatica frequente utilizada por atacantes para garantir acesso persistente ao ambiente, mitigando o risco de perder o acesso caso a vulnerabilidade inicial seja corrigida.

### Identificacao do Usuario Backdoor
* **Descricao:** Nome de usuario malicioso provisionado pelo invasor em um dos hosts infectados para servir como porta de entrada secundaria.
* **Resposta / Usuario Identificado:** `A1berto`

### Tecnica de Impersonation (Imitacao de Identidade)
* **Descricao:** Analise de contas corporativas legitimas que o adversario tentou mimetizar para mascarar suas acoes e dificultar a deteccao em ferramentas de monitoramento de comportamento.
* **Resposta / Usuario Alvo de Imitacao:** `Alberto`

### Execucao de Comando Remoto para Persistencia
* **Descricao:** Sintaxe de linha de comando exata empregada pelo invasor a partir de um terminal remoto para forcar a criacao e configuracao do usuario backdoor no ativo alvo.
* **Resposta / Comando Tecnico Utilizado:** `C:\windows\System32\Wbem\WMIC.exe" /node:WORKSTATION6 process call create "net user /add A1berto paw0rd1`

---

## 3. Analise Forense do Registro do Windows

Para garantir que as contas maliciosas permaneçam ativas ou mantenham privilegios administrativos mesmo apos a reinicializacao da maquina afetada, modificacoes diretas no Registro do Windows sao executadas.

### Atualizacao de Chaves de Registro Estruturais
* **Descricao:** Identificacao do caminho absoluto modificado ou criado no registro do sistema operacional associado diretamente a persistencia do novo usuario oculto.
* **Resposta / Caminho Completo da Chave:** `HKLM\SAM\SAM\Domains\Account\Users\Names\A1berto`

---

## 4. Telemetria de Execucao de Scripts (PowerShell)

A exploracao de sistemas Windows frequentemente abusa de ferramentas nativas do proprio sistema operacional (tatica conhecida como Living off the Land). A analise minuciosa dos logs do PowerShell e mandatoria para identificar acoes executadas direto na memoria.

### Isolamento do Host Infectado
* **Descricao:** Mapeamento do nome de rede do computador (Hostname) onde os comandos suspeitos e scripts maliciosos baseados em PowerShell foram disparados.
* **Resposta / Nome do Host:** `James.browne`

### Volumetria de Eventos do PowerShell
* **Descricao:** Volume total de logs de eventos gerados especificamente pelas atividades e blocos de scripts maliciosos no dispositivo auditado, validando que a auditoria avancada de script block (Event ID 4104) estava operacional.
* **Resposta / Metrica de Eventos:** `79`

### Desofuscacao de Comando e Conexao Externa (C2)
* **Descricao:** Extração e decodificacao do script ocultado (geralmente em Base64 atraves do parametro `-EncodedCommand`) que realizou uma requisicao externa de rede para baixar artefatos adicionais.
* **Resposta / URL do Servidor Malicioso:** `hxxp[://]10[.]10[.]10[.]5/news[.]php`

---

## 5. Conclusao Tecnica
A investigacao confirmou que o incidente envolveu taticas classicas de estabelecimento de persistencia por conta local e manipulacao de chaves de registro, seguidas de execucao de codigo ofuscado. A correta habilitacao dos logs de auditoria do PowerShell (Script Block Logging) no ambiente Windows foi o fator decisivo para que a equipe de SOC conseguisse obter a visibilidade necessaria para extrair a URL de Comando e Controle (C2) externa do adversario.
