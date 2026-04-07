# Defesas de Malware: Engenharia Reversa e Técnicas de Evasão

Este repositório documenta o estudo avançado de técnicas **Anti-Engenharia Reversa** e as metodologias utilizadas para contornar defesas que visam impedir a análise de malwares.

---

## A Corrida Armamentista
A análise de malware é uma evolução contínua. Enquanto autores de ameaças desenvolvem **Armored Malware** (malware blindado) para retardar a detecção, analistas utilizam engenharia reversa para:
* **Extrair IOCs:** Identificar IPs, domínios e hashes maliciosos.
* **Mapear Funcionalidades:** Entender o impacto real do artefato no sistema.
* **Desenvolver Contramedidas:** Criar assinaturas e regras de detecção.

---

## Técnicas Anti-Depuração (Anti-Debugging)
A depuração interativa é a maior ameaça ao malware. Para combatê-la, são implementadas proteções ativas:

1. **Detecção de Ambiente:** Uso de APIs como `IsDebuggerPresent` para verificar sinalizadores no PEB (Process Environment Block).
2. **Adulteração de Hardware:** Redefinição de registradores de debug da CPU (`DR0-DR7`) para "cegar" pontos de interrupção.
3. **Código Automodificável:** O código altera sua própria estrutura em memória durante a execução, invalidando a análise estática.
4. **Suspensão de Threads:** Uso da API `SuspendThread` para paralisar o depurador após identificar o processo via títulos de janela (ex: "dbg", "debugger").

### Estudo de Caso: Patching da API `SuspendThread`
* **Mecanismo:** O malware enumerava janelas e suspendia o PID do depurador.
* **Solução:** Localização da chamada intermodular no **x32dbg** e aplicação de **NOPing** (substituição da instrução por `0x90`).
* **Resultado:** Neutralização da rotina de defesa e continuidade da análise.

---

## Detecção de Ambiente (Anti-VM)
Malwares utilizam "Fingerprinting" para identificar se estão em uma Sandbox ou Máquina Virtual:

* **Rastros de Sistema:** Busca por processos (`vmmtoolsd.exe`) ou chaves de registro de virtualização.
* **Network OUI:** Identificação de endereços MAC vinculados a fornecedores como VMware e VirtualBox.
* **Recursos Limitados:** Verificação de baixa contagem de núcleos de CPU ou RAM insuficiente (< 4GB).
* **Sensores Térmicos (WMI):** Interrogação da classe `Win32_TemperatureProbe`. Em VMs, esta consulta retorna `Not Supported`.

### Metodologias de Bypass
* **Memory Patching:** Alteração direta de bits no Dump de memória para mudar resultados de funções de verificação (ex: mudar `0` para `1` em variáveis de retorno).
* **Manipulação de EIP:** Alteração do registrador **EIP (Instruction Pointer)** para "teleportar" a execução para além das rotinas de verificação, indo direto para o *payload*.

---

## Ofuscação e Packers (Empacotadores)
A ofuscação visa obscurecer o código através de codificação (Base64/XOR) ou criptografia. O uso de **Packers** (ex: UPX, Themida) comprime o malware original dentro de um "invólucro".

### Processo de Unpacking Manual
Para analisar um arquivo empacotado, seguimos o fluxo de descompressão dinâmica:
1. **Identificação:** Uso de **Detect It Easy (DIE)** e **PEStudio** para analisar alta entropia e seções anômalas.
2. **Localização do OEP:** Identificação do **Original Entry Point** (onde o código real começa após a descompactação).
3. **Dumping e Reconstrução (Scylla):** * Extração do processo da memória para um novo arquivo `.exe`.
   * **Fix IAT:** Reconstrução da Tabela de Endereços de Importação para garantir que o binário extraído seja funcional.

---

## Conclusão Técnica
O domínio sobre os **Registradores da CPU** e o **Endereçamento de Memória** permite ao analista reescrever a realidade do programa durante a execução. O sucesso da engenharia reversa depende da capacidade de identificar o ponto de transição entre as camadas de proteção e o código malicioso real.

---
