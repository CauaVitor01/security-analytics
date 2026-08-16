# 🛡️ Relatório de Investigação: Boss of the SOC (BOTS) v3

> **Investigação de Segurança (Blue Team) baseada no conjunto de dados BOTSv3 do Splunk.**
> Este relatório documenta a triagem e investigação de incidentes envolvendo ambientes em nuvem (AWS), atividades anômalas em endpoints, mineração de criptomoedas, vazamento de credenciais e comunicação com servidores de Comando e Controle (C2).

---

## 1. Série 200: Eventos da AWS e Monitoramento de Endpoints

### Resumo da Investigação
Nesta tarefa, concentrei-me na investigação de eventos da AWS e em questões focadas em eventos relacionados a endpoints. Esta análise abordou desde o rastreamento de usuários e monitoramento de autenticação até a identificação de configurações incorretas em buckets e inventário de hardware.

### Passo a Passo da Investigação
1. **Monitoramento de Identidade e Acesso (IAM):** A primeira etapa exigiu encontrar os usuários do IAM que acessaram (com ou sem sucesso) os serviços no ambiente AWS da Frothly. Consultei a documentação de exemplos de arquivos de log do *AWS CloudTrail* para identificar o tipo de fonte correto e os campos específicos que continham essas respostas.
2. **Auditoria de Atividades da API e MFA:** Estruturei uma busca para alertar sobre atividades na API da AWS que ocorreram sem a autenticação multifator (MFA). Foi necessário identificar o caminho JSON completo referente a essa validação. Para limpar os resultados, excluí eventos relacionados a logins no console utilizando uma pesquisa por palavra-chave colocada entre asteriscos.
3. **Análise de Configurações Incorretas (Bucket S3):** Uma parte crítica da investigação girou em torno de uma configuração incorreta comum na nuvem: buckets S3 publicamente acessíveis. Após revisar o funcionamento de ACLs e da API `PutBucketAcl`, investiguei a ação do usuário Bud. Localizei o evento exato da chamada de API que habilitou o acesso público ao bucket.
4. **Rastreamento de Upload de Arquivos:** Sabendo o nome do bucket S3 comprometido, o próximo passo foi identificar o impacto dessa exposição. Investiguei o upload de um arquivo de texto executado através da API `PutObject`. Para encontrar esse evento, consultei um `sourcetype` diferente relacionado à AWS e utilizei os códigos de status HTTP como filtro auxiliar para isolar o upload bem-sucedido.
5. **Inventário de Endpoints e Hardware:** Além da nuvem, analisei os tipos de origem disponíveis no conjunto de dados focados em hardware para identificar o número do processador usado nos servidores web. Em seguida, expandi a pesquisa para incluir sistemas operacionais e hosts, o que permitiu identificar o FQDN de um endpoint que executava uma edição do SO Windows diferente dos demais.

### Evidências Extraídas (Questões Essenciais)
*   **Pergunta:** Liste os usuários do IAM que acessaram um serviço da AWS (com sucesso ou sem sucesso) no ambiente AWS da Frothly.
    *   **Resposta:** `bstoll, btun, splunk_access, web_admin`
    *   *Explicação:* Identificados através do mapeamento de usuários nos arquivos de log do CloudTrail.
*   **Pergunta:** Qual campo você usaria para alertar sobre uma atividade na API da AWS sem autenticação multifator (MFA)?
    *   **Resposta:** `userIdentity.sessionContext.attributes.mfaAuthenticated`
    *   *Explicação:* Encontrado mapeando o caminho JSON completo que indica a ausência de MFA nas requisições, excluindo acessos diretos via console.
*   **Pergunta:** Bud acidentalmente torna um bucket do S3 publicamente acessível. Qual é o ID do evento da chamada da API que habilitou o acesso público?
    *   **Resposta:** `ab45689d-69cd-41e7-8705-5350402cf7ac`
*   **Pergunta:** Qual o nome do arquivo de texto que foi carregado com sucesso no bucket S3 enquanto este estava publicamente acessível?
    *   **Resposta:** `OPEN_BUCKET_PLEASE_FIX.txt`
    *   *Explicação:* Localizado cruzando um novo tipo de origem da AWS com o código de status HTTP do método `PutObject`.

### O que foi realizado
*   **O que foi feito:** Realizei a análise de eventos em nuvem e endpoints, mapeando acessos de usuários do IAM na AWS, identificando falhas de uso de MFA em chamadas de API e rastreando configurações incorretas que expuseram publicamente um bucket S3 (culminando em upload indevido). Conduzi também o inventário de hardware de endpoints.
*   **Principais conceitos aprendidos:** Estrutura de logs da AWS (CloudTrail), análise de caminhos JSON para auditoria, e interpretação de chamadas de API críticas (`PutBucketAcl`, `PutObject`).
*   **Importância:** O domínio na leitura de logs da AWS é vital para detectar exposições acidentais e rastrear auditorias de acesso. A identificação rápida da chamada de API que altera uma ACL permite mitigar a exposição antes que agentes maliciosos possam explorá-la.

---

## 2. Série 200: Mineração de Criptomoedas e Eventos de Endpoint

### Resumo da Investigação
O foco foi a análise de logs de navegadores de endpoints e a detecção de atividades maliciosas relacionadas à mineração de criptomoedas. A análise envolveu a verificação do consumo de recursos de processadores, a ordenação cronológica de eventos no Splunk e a correlação de dados com os alertas do Symantec Endpoint Protection (SEP).

### Passo a Passo da Investigação
1. **Análise de Utilização do Processador em Endpoints:** Mapeamento da utilização de hardware. Realizei pesquisas por palavras-chave relacionadas a processadores para identificar os `sourcetypes`. Apliquei filtros para localizar endpoints com 100% de utilização de CPU, invertendo a ordem dos eventos para identificar a sequência cronológica dos picos de consumo.
2. **Ordenação de Eventos e Análise de Logs do SEP:** Organizei os dados exibindo os campos relevantes em tabela, usando a função de ordenação por tempo (`sort + _time`). Foi possível examinar os eventos de mineração, identificar o comportamento dos hosts e cruzar os achados com os registros do SEP para extrair o ID da assinatura, o nome do ataque, gravidade e a confirmação do endpoint que neutralizou a ameaça.

### Evidências Extraídas (Questões Essenciais)
*   **Pergunta:** Um endpoint Frothly apresenta sinais de atividade de mineração de criptomoedas. Qual é o nome do segundo processo a atingir 100% de utilização da CPU nesse endpoint?
    *   **Resposta:** `chrome#5`
    *   *Explicação:* Obtido ao listar os processos em ordem cronológica de pico de uso de CPU no `sourcetype` de métricas do sistema.
*   **Pergunta:** Qual é o nome de host abreviado do único endpoint do Frothly que realmente mina criptomoeda Monero?
    *   **Resposta:** `BSTOLL-L`
*   **Pergunta:** Usando as funções de ordem de eventos do Splunk, qual é o primeiro ID de assinatura visto da ameaça de mineração de criptomoedas de acordo com os dados do SEP?
    *   **Resposta:** `30358`
    *   *Explicação:* Encontrado aplicando a ordenação por tempo (`sort + _time`) nos logs do SEP e isolando o primeiro registro.
*   **Pergunta:** Qual o nome do ataque e a gravidade dessa ameaça específica de minerador de criptomoedas?
    *   **Resposta:** `JSCoinminer Download 8`
*   **Pergunta:** Qual é o nome de host abreviado do único endpoint do Frothly que demonstra ter neutralizado a ameaça das criptomoedas?
    *   **Resposta:** `BTUN-L`
    *   *Explicação:* Identificado verificando os logs do SEP em que o estado da ação aplicada resultou na neutralização.

### O que foi realizado
*   **O que foi feito:** Investigação de incidentes focados em mineração não autorizada de criptomoedas (Monero). Analisei métricas de CPU a 100%, apliquei funções de ordenação temporal e correlacionei os achados com eventos do SEP.
*   **Principais conceitos aprendidos:** Navegação por métricas de processador, ordenação de eventos no Splunk para reconstrução de linhas do tempo e interpretação de dados de proteção de endpoint (SEP).
*   **Importância:** Identificar picos anômalos de CPU no tempo diferencia falhas operacionais de atividades maliciosas (*cryptojacking*). Cruzar essas anomalias com logs de antivírus confirma se as defesas responderam adequadamente.

---

## 3. Série 200: Eventos AWS e Vazamento de Chaves

### Resumo da Investigação
A análise concentrou-se na identificação de anomalias no AWS IAM, rastreamento de credenciais vazadas em repositórios de código externos e no mapeamento das ações não autorizadas realizadas por um adversário utilizando essas chaves comprometidas.

### Passo a Passo da Investigação
1. **Identificação de Anomalias de Acesso no IAM:** Identifiquei qual chave de acesso estava gerando o maior número de erros distintos. Consultei os logs do *AWS CloudTrail* e apliquei funções do Splunk para recuperar a contagem distinta de valores.
2. **Rastreio de Vazamento de Credenciais:** O usuário Bud enviou acidentalmente chaves de acesso para um repositório externo. A Amazon abriu um caso de suporte. Localizei o evento de notificação e extraí o ID do caso de suporte, bem como a chave secreta associada.
3. **Mapeamento das Ações do Adversário:** Com a chave de acesso identificada, expandi a consulta para ver o que o adversário tentou fazer. Consultando a API `CreateAccessKey`, notei uma tentativa não autorizada de criar uma chave para um recurso específico. Em seguida, rastreei outra ação: a tentativa de descrever uma conta, extraindo a string completa do agente do usuário (*User-Agent*).

### Evidências Extraídas (Questões Essenciais)
*   **Pergunta:** Qual chave de acesso de usuário do IAM gera o maior número de erros distintos ao tentar acessar recursos do IAM?
    *   **Resposta:** `AKIAJOGC********PUPA`
    *   *Explicação:* Contagem distinta de erros nos logs do CloudTrail filtrados por chaves de acesso.
*   **Pergunta:** Bud acidentalmente envia chaves de acesso da AWS para um repositório de código externo e a Amazon abre um caso de suporte. Qual é o ID do caso de suporte?
    *   **Resposta:** `5244329601`
*   **Pergunta:** Qual é a chave de acesso secreta da chave que vazou para o repositório de código externo?
    *   **Resposta:** `Bx8/gTsYC98*************************`
*   **Pergunta:** Usando a chave vazada, o adversário faz uma tentativa não autorizada de criar uma chave para um recurso específico. Qual é o nome desse recurso?
    *   **Resposta:** `nullweb_admin`
    *   *Explicação:* Encontrada rastreando as chamadas da API `CreateAccessKey`.
*   **Pergunta:** Usando a chave vazada, o adversário faz uma tentativa não autorizada de descrever uma conta. Qual é a string do agente do usuário que originou a solicitação?
    *   **Resposta:** `ElasticWolf/5.1.6`
    *   *Explicação:* Extraída dos eventos vinculados à chave de acesso e ao nome de usuário.

### O que foi realizado
*   **O que foi feito:** Investigação de logs da AWS para identificar uma chave do IAM gerando alto volume de erros. Rastreei o vazamento acidental em um repositório externo e mapeei tentativas não autorizadas de criar novas chaves e descrever contas.
*   **Principais conceitos aprendidos:** Uso do Splunk para contagens distintas; estrutura de eventos do AWS CloudTrail; análise de chamadas de API críticas (`CreateAccessKey`).
*   **Importância:** O monitoramento contínuo de logs do IAM detecta anomalias indicativas de credenciais vazadas. Rastrear a chave no SIEM permite identificar os recursos visados e a origem do ataque (User-Agent), acelerando a resposta.

---

## 4. Série 300: Eventos de E-mail e Endpoints

### Resumo da Investigação
Foco em eventos relacionados a e-mails e endpoints. A análise abrangeu a identificação de uploads maliciosos em serviços de nuvem (OneDrive), triagem de anexos de e-mail com macros habilitadas, rastreamento de criação de contas não autorizadas (Linux/Windows) e detecção de binários de varredura de rede.

### Passo a Passo da Investigação
1. **Rastreamento de Upload Malicioso no OneDrive:** Identificação do agente do usuário responsável por carregar um link malicioso no OneDrive, utilizando eventos do Office 365 e registros de antivírus como ponto de partida.
2. **Análise de Malware em E-mail (Macros Habilitadas):** Busca por um anexo identificado como malware contendo macros. Estruturei a consulta utilizando curingas e extensões de arquivo com suporte a macros. Ao localizar o anexo, examinei os campos para descobrir o arquivo executável incorporado.
3. **Investigação de Comprometimento de Endpoint e Criação de Contas:** Identificação de comandos de criação de contas no Linux e análise dos registros do usuário `root`. Recuperei a senha de um usuário criado com sucesso pelo `root`. Em seguida, expandi a consulta, excluindo tipos de origem irrelevantes, para identificar contas criadas pós-comprometimento e seus respectivos grupos.
4. **Identificação de Processos e Scanners de Rede:** Busca por um ID de processo escutando em uma porta "leet" (traduzida para valores numéricos para refinar a busca). Por fim, investiguei o endpoint de Fyodor, cruzando o nome da máquina com códigos de conexão de rede, o que revelou um binário incomum do qual extraí o valor MD5.

### Evidências Extraídas (Questões Essenciais)
*   **Pergunta:** Qual é a string completa do agente do usuário que carregou o arquivo de link malicioso no OneDrive?
    *   **Resposta:** `Mozilla/5.0 (X11; U; Linux i686; ko-KP; rv: 19.1br) Gecko/20130508 Fedora/1.9.1-2.5.rs3.0 NaenaraBrowser/3.5b4`
*   **Pergunta:** Qual era o nome do anexo com macros habilitadas identificado como malware e qual executável estava incorporado nele?
    *   **Resposta:** `Frothly-Brewery-Financial-Planning-FY2019-Draft.xlsm`
    *   *Explicação:* Nome localizado buscando extensões de arquivo com macros em eventos de e-mail.
*   **Pergunta:** Qual o nome do usuário que foi criado após o comprometimento do endpoint e a quais grupos ele foi atribuído?
    *   **Resposta:** `svcvnc`
    *   *Explicação:* Identificado utilizando buscas genéricas e excluindo tipos de origem irrelevantes.
*   **Pergunta:** Qual é o ID do processo que está escutando em uma porta "leet"?
    *   **Resposta:** `14356`
*   **Pergunta:** Qual é o valor MD5 do arquivo baixado para o sistema de endpoint de Fyodor e usado para escanear a rede?
    *   **Resposta:** `586ef56f4d8963dd546163ac31c865d7`
    *   *Explicação:* Localizado cruzando o nome da máquina com o código de evento de conexões de rede.

### O que foi realizado
*   **O que foi feito:** Triagem avançada de eventos de segurança. Rastreio de uploads maliciosos no OneDrive, análise de e-mails com macros e executáveis embutidos, detecção de criação não autorizada de usuários e mapeamento de portas suspeitas e hashes de scanners de rede.
*   **Principais conceitos aprendidos:** Filtragem de eventos via palavras-chave estruturadas; exclusão sistemática de tipos de origem ruidosos; correlação de informações do host (nome da máquina, eventos de rede) para isolar binários anômalos.
*   **Importância:** A capacidade de navegar em grandes volumes de logs é essencial para montar o quebra-cabeça do incidente, desde a porta de entrada (e-mail/nuvem) até as ações de pós-comprometimento (persistência e reconhecimento).

---

## 5. Série 300: Eventos de Endpoint e Comando e Controle

### Resumo da Investigação
Direcionei a investigação para eventos em endpoints. As atividades envolveram o rastreamento do download de ferramentas de ataque, identificação de arquivos suspeitos no Linux, investigação de e-mails sobre exfiltração de dados e decodificação de payloads de PowerShell para mapear a infraestrutura de Comando e Controle (C2).

### Passo a Passo da Investigação
1. **Rastreamento de Download de Ferramentas de Ataque:** No host do usuário Fyodor, analisei diferentes protocolos (HTTP, TCP, FTP). Ao focar no FTP com a operação de transferência (`RETR`), visualizei a porta suspeita e a extensão do arquivo malicioso. Após uma pesquisa adicional, descobri que a porta `3333` (identificada nos logs) é amplamente utilizada para atividades de mineração de criptomoedas.
2. **Análise de Arquivos Transmitidos Remotamente:** Investigação de arquivos transferidos para o servidor Linux. Busquei no diretório `/tmp` (`/tmp/*.*`), refinando a consulta ao excluir `sourcetypes` irrelevantes, o que revelou dois arquivos suspeitos exatos.
3. **Confirmação de Exfiltração de Dados:** O adversário (Taedonggang) enviou um e-mail para Grace Hoppy se gabando do ataque. Ajustando as fontes incluídas e excluídas, encontrei a mensagem específica e contabilizei os e-mails de clientes expostos.
4. **Decodificação de Payload e Mapeamento de Comando e Controle:** Análise profunda nos registros do PowerShell. Realizei a decodificação para revelar os payloads ocultos. Com eles em mãos, identifiquei o caminho da URL de C2 e listei todos os endpoints da Frothly que contatavam essa infraestrutura maliciosa.

### Evidências Extraídas (Questões Essenciais)
*   **Pergunta:** Qual foi o número da porta utilizada pelo adversário para baixar suas ferramentas de ataque e qual arquivo continha essas ferramentas?
    *   **Resposta:** `[Respostas corretas confirmadas via logs]`
    *   *Explicação:* Atividades no host de Fyodor, utilizando o protocolo FTP (com a operação `RETR`).
*   **Pergunta:** Durante o ataque, dois arquivos são transmitidos remotamente para o diretório `/tmp` do servidor Linux local pelo invasor. Quais são os nomes desses arquivos?
    *   **Resposta:** `colonel.c, definitelydontinvestigatethisfile.sh`
    *   *Explicação:* Localizados buscando o caminho `/tmp/*.*`.
*   **Pergunta:** O adversário Taedonggang enviou um e-mail para Grace Hoppy se gabando da exfiltração de dados. Quantos e-mails de clientes da Frothly foram expostos?
    *   **Resposta:** `8`
*   **Pergunta:** Qual é o caminho da URL acessada pelo servidor de comando e controle?
    *   **Resposta:** `/admin/get.php`
    *   *Explicação:* Descoberto através da localização e decodificação dos registros do PowerShell.
*   **Pergunta:** Pelo menos dois endpoints do Frothly contatam a infraestrutura de comando e controle do adversário. Quais são seus nomes de host abreviados?
    *   **Resposta:** `ABUNGST-L, FYODOR-L`

### O que foi realizado
*   **O que foi feito:** Investigação de rastros de download via FTP, localização de artefatos maliciosos em diretórios temporários no Linux, confirmação de vazamento de dados por e-mail e decodificação de scripts de PowerShell para descobrir comunicação externa.
*   **Principais conceitos aprendidos:** Uso de operações específicas de protocolo (`RETR`); exclusão ativa de fontes ruidosas; extração e decodificação de comandos em logs do PowerShell para revelar payloads ofuscados.
*   **Importância:** Isolar eventos no nível do endpoint e analisar os logs do PowerShell são cruciais para entender a execução do ataque. As URLs decodificadas permitem bloquear os servidores de Comando e Controle (C2), enquanto o mapeamento de arquivos ajuda a identificar sistemas base comprometidos.
