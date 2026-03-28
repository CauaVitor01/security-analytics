Fase 2: Identificação e Definição de Escopo

Este documento detalha o processo de deteção, validação e análise da extensão de incidentes de segurança, utilizando uma abordagem orientada por inteligência.

1. Introdução e Valor Estratégico

A Identificação é o ponto de transição onde um evento digital é validado como um incidente real.

Deteção Célere: A rapidez nesta fase é o fator determinante para limitar danos e reduzir o tempo de recuperação (MTTR - Mean Time to Recovery).

2. A Tríade da Identificação

A eficácia da deteção depende da harmonia entre três pilares:

PilarDescriçãoFerramentas/AçõesTecnologiaGeração de alertas e visibilidade técnica.Aurora EDR, Sysmon, Snort.ProcessosFluxo de triagem e escalonamento.Triagem -> Validação -> Notificação CSIRT.PessoasInterpretação humana e tomada de decisão.Analistas de SOC (N1/N2/N3).3. Visibilidade Técnica e Ferramental

Para garantir que nenhum ponto cego persista na infraestrutura, as seguintes soluções foram implementadas e dominadas:

EDR (Endpoint Detection & Response): Aurora EDR e Wazuh para monitorização profunda de hosts.

IDPS (Intrusion Detection/Prevention): Snort para análise de tráfego de rede e assinaturas maliciosas.

SIEM (Security Information & Event Management): Splunk e ELK Stack para correlação de logs centralizada.

4. Definição de Escopo e Análise de Impacto

Utilizamos o Inventário de Ativos e a Planilha da Perdição (SoD) para correlacionar IoCs com ativos críticos.

Inventário de Ativos (Alvos Monitorados)

AtivoEndereço IPOSProprietárioDC-01172.16.1.10Windows Server 2019Derick MarshallMAILSVR-01172.16.1.15Windows Server 2019Stan SimonWKSTN-02172.16.1.151Windows 10 ProMichael Ascot

Enriquecimento de Artefatos (Planilha da Perdição - SoD)

A SoD acelera a resposta ao identificar Indicadores de Comprometimento (IoCs) conhecidos:

IP Malicioso: 188.40.75.132 (Hospedagem de Malware).

Domínio Phishing: b24b-158-62-19-6.ngrok-free.app (Ngrok-free).

Hash (SHA1): 75ec7d0d1b6b2b4c816cbc1b71cd0f8f06bd8c1b (Malware Payload).

Ciclo de Feedback Orientado por Inteligência

A investigação não é linear. Ela segue um ciclo iterativo de refinamento:

Notificação: Gatilho inicial do incidente.

Documentação: Registo detalhado para base da investigação.

Coleta de Evidências: Logs, tráfego e artefatos voláteis.

Identificação de Artefatos: Extração de IoCs.

Ponto de Pivô: Descoberta de novas áreas (ex: movimento lateral), forçando o retorno à fase de documentação para expandir o escopo.

 Conclusão

A identificação precisa permite que a expertise técnica seja aplicada no momento certo, assegurando que o incidente seja gerido com precisão e que as partes interessadas sejam notificadas prontamente. O sucesso aqui define a eficácia da próxima fase: Contenção.

📂 Documento PDF Relacionado: Identification_and_scoping.pdf
