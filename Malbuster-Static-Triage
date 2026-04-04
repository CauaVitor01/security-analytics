Malbuster-Static-Triage

Este repositório contém a documentação técnica de uma simulação de resposta a incidentes, focada na Análise Estática Básica de binários maliciosos.

Cenário do Projeto
Atuando como Engenheiro Reverso, prestei suporte à equipe de SOC após a detecção de comportamentos anômalos na rede. O objetivo foi realizar a triagem de 6 amostras ("Malbusters") para identificar famílias de malware, mapear capacidades táticas e extrair IOCs (Indicadores de Comprometimento) para remediação imediata.

Resumo dos Achados
- Identificação de Famílias:** Detecção de ameaças conhecidas como TrickBot e Zbot.
- Táticas MITRE ATT&CK: Mapeamento de técnicas de Discovery (T1083) e Anti-VM.
- Capacidades Críticas: Identificação de binários com funções de Keylogging e comunicação de rede ofuscada.
- Análise de Cabeçalho PE: Investigação de DLLs importadas (`shell32.dll`, `mscoree.dll`) e metadados (`VS_VERSION_INFO`).

Ferramentas Utilizadas
- PEstudio: Análise de cabeçalhos, DLLs e strings.
- CAPA (Mandiant):Identificação de capacidades e mapeamento MITRE.
- VirusTotal / Abuse.ch: Inteligência de ameaças e reputação de hashes.
- FLARE VM: Ambiente isolado e seguro para análise.

📂 Relatório Completo
> O relatório detalhado em formato PDF está disponível na raiz deste repositório. Ele contém a análise completa de cada amostra, incluindo capturas de tela (screenshots) de todas as ferramentas, logs detalhados e evidências visuais dos achados.

Nota de Segurança: Todos os procedimentos foram realizados em ambiente controlado e isolado (Sandbox). Nunca execute binários suspeitos em sua máquina host.
