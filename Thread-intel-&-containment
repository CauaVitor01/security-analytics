# Fase 3: Contenção Baseada em Inteligência

Este documento detalha as estratégias de contenção e o uso de **Threat Intelligence** para mitigar incidentes de forma proativa e controlada.

-----

## 1\. Pré-Contenção e Coleta de Evidências

Antes de isolar o sistema, é fundamental extrair **IOCs (Indicadores de Comprometimento)** para entender a natureza da ameaça.

### Identificação do "Paciente Zero"

Através da análise de tráfego (Packetbeat/IDS), identificamos o download de um artefato malicioso (`dropper.exe`).

**Extração de Hash (DNA do Malware):**

  * **Windows:** `Get-FileHash dropper.exe`
  * **Linux:** `sha256sum dropper.exe`
  * **Hash Identificado:** `84BDE632C5BFD2A7FF84E579E6F7561543CA0AAD6D8E7275DAE5926BA4F561C1`

## 2\. Estratégias de Contenção: O Dilema do Defensor

A escolha entre isolar ou monitorar depende do risco e da necessidade de inteligência.

| Estratégia | Abordagem | Prós | Contras |
| :--- | :--- | :--- | :--- |
| **Isolamento Completo** | Agressiva | Interrompe C2 e exfiltração imediatamente. | Perceptível; pode causar ações destrutivas do atacante. |
| **Isolamento Controlado** | Monitoramento | Coleta de TTPs e descoberta de novos sistemas afetados. | Alto risco; exige monitoramento 24/7. |

-----

## Inteligência de Ameaças (Threat Intel)

A inteligência permite prever os próximos passos do adversário através das **TTPs**:

  * **Táticas:** Objetivos gerais (ex: roubo de dados).
  * **Técnicas:** Métodos específicos (ex: Phishing, Escala de Privilégios).
  * **Procedimentos:** O fluxo da cadeia de ataque (Kill Chain).

### Aplicação Prática

Utilizamos plataformas como **OpenCTI** e feeds como **AlienVault OTX** para:

1.  **Armar o SIEM:** Configurar alertas proativos para hashes e IPs conhecidos.
2.  **Contextualização:** Atribuir atividades a grupos específicos (ex: *tal0nix*).

-----

## O Perigo do "Whack-a-Mole" (Bater na Toupeira)

A remoção precipitada de um host sem definir o escopo total gera uma **falsa sensação de segurança**. O atacante pode permanecer em outros sistemas não detectados.

> **Lógica de Sucesso:** Melhor Inteligência ➔ Melhor Escopo ➔ Contenção Inteligente ➔ Controle Total.

## Ciclos de Feedback e Melhoria

As lições aprendidas nesta fase alimentam o endurecimento (hardening) da rede:

  * Refinamento de telemetria no SIEM para reduzir ruído.
  * Criação de canais oficiais de denúncia (evitando o encaminhamento manual de anexos maliciosos).

-----

>  **Documento PDF Relacionado:** [Thread\_intel\_&\_containment.pdf](https://www.google.com/search?q=./docs/Thread_intel_%26_containment.pdf)

-----
