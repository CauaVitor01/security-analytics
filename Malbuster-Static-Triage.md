# Malbuster: Triagem Estática de Malware

> **Nota de Documentação:** Este arquivo Markdown apresenta um resumo executivo da análise estática básica realizada nas amostras. Para a investigação detalhada, incluindo o passo a passo da triagem, extração de IOCs e as capturas de tela (prints) comprovando o uso de todas as ferramentas, consulte o relatório completo em formato PDF disponível na raiz deste repositório.

---

## 1. Cenário do Projeto

Atuando como Engenheiro Reverso, prestei suporte à equipe de SOC após a detecção de comportamentos anômalos na rede corporativa. O objetivo da missão foi realizar a triagem inicial de 6 amostras de binários suspeitos (denominadas "Malbusters") para identificar famílias de malware, mapear capacidades táticas e extrair Indicadores de Comprometimento (IOCs) para auxiliar na remediação imediata do incidente.

---

## 2. Resumo dos Achados Técnicos

* **Identificação de Famílias:** Detecção de ameaças conhecidas associadas aos malwares TrickBot e Zbot.
* **Mapeamento MITRE ATT&CK:** Identificação de táticas e técnicas como Discovery (T1083) e evasão de análise (Anti-VM/Sandbox evasion).
* **Capacidades Críticas:** Isolamento de binários com funções integradas de Keylogging e mecanismos de comunicação de rede ofuscada.
* **Análise de Cabeçalho PE (Portable Executable):** Investigação aprofundada de DLLs importadas (como `shell32.dll` e `mscoree.dll`) e extração de artefatos em metadados (`VS_VERSION_INFO`).

---

## 3. Ferramentas Utilizadas

O processo de análise estática apoiou-se no seguinte ferramental de engenharia reversa e inteligência de ameaças:

* **PEstudio:** Análise estrutural de cabeçalhos PE, verificação de DLLs e extração de strings suspeitas.
* **CAPA (Mandiant):** Identificação automatizada de capacidades maliciosas e mapeamento direto para o framework MITRE ATT&CK.
* **VirusTotal / Abuse.ch:** Plataformas de Threat Intelligence para verificação de reputação de hashes e contexto global da ameaça.
* **FLARE VM:** Ambiente de sandbox customizado, garantindo isolamento seguro para a análise.

---

## 4. Aviso de Segurança

Todos os procedimentos de análise de malware documentados neste projeto foram realizados em um ambiente estritamente controlado, monitorado e isolado da rede externa (Sandbox). **Nunca execute binários suspeitos em sua máquina host ou em redes de produção.**
