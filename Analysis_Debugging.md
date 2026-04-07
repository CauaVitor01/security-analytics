# Análise Dinâmica Avançada: Depuração e Engenharia Reversa

Este módulo foca na utilização de depuradores de nível de Assembly para analisar o comportamento interno de malwares, identificar técnicas de evasão e realizar modificações em tempo de execução (*Patching*).

---

## Conceitos de Evasão de Malware

Malwares modernos utilizam diversas estratégias para impedir a análise:

### 1. Evasão Estática
* **Packing & Ofuscação:** Ocultação do código real através de compressores ou criptografia de strings.
* **Dynamic Loading:** Uso de `LoadLibrary` para esconder funções importadas na IAT (Import Address Table).

### 2. Evasão Dinâmica (Anti-Analysis)
* **Anti-VM:** Verificação de drivers (VBoxGuest), chaves de registro e recursos de hardware (RAM/CPU) limitados.
* **Timing Attacks:** Uso de `Sleep` longo para esgotar o tempo limite de sandboxes.
* **Atividade Humana:** Monitoramento de movimentos de mouse e arquivos recentes para confirmar um usuário real.

---

## Ferramenta Utilizada: x32dbg / x64dbg

O depurador permite o controle total sobre a CPU durante a execução do artefato.

### Estrutura da Aba CPU:
1. **Desmontagem:** Visualização do código em Assembly e o ponteiro de instrução (`EIP`/`RIP`).
2. **Registradores:** Monitoramento de valores em tempo real (EAX, EBX, Flags).
3. **Dump de Memória:** Visualização de dados brutos e strings decodificadas na RAM.
4. **Stack (Pilha):** Argumentos de funções e endereços de retorno.



---

## Estudo de Caso: `crackme-arebel.exe`

Nesta análise, enfrentamos um binário que utiliza **TLS Callbacks** para executar código antes do ponto de entrada principal (`main`).

### Achados da Análise:
* **Detecção de Processos:** O malware utiliza a API `CreateToolhelp32Snapshot` para listar processos e buscar depuradores ativos.
* **Evasão Identificada:** Se um depurador é encontrado, o malware chama a API `SuspendThread` (da `KERNEL32.dll`) para congelar a análise.
* **Manipulação de Flag:** No salto condicional crítico, a **Zero Flag (ZF)** estava em `1`, impedindo o salto de segurança.

### Engenharia Reversa Ativa (Patching):
Para contornar a proteção, realizamos as seguintes ações:
1. **Interferência em Runtime:** Alteramos manualmente o valor da **Zero Flag** para `0`, forçando o salto para o caminho de execução legítimo.
2. **Aplicação de Patch:** Substituímos a instrução de chamada maliciosa por **NOPs (No Operation - 0x90)**. 
3. **Persistência do Patch:** Utilizamos a função *Patch File* para gerar um novo executável (`crackme-arebel-patched.exe`) permanentemente desarmado.

---

## Comandos Rápidos Utilizados
| Tecla | Função | Descrição |
| :--- | :--- | :--- |
| **F9** | Run | Executa até o próximo breakpoint. |
| **F7** | Step Into | Entra dentro de funções. |
| **F8** | Step Over | Pula a execução de funções, parando na linha seguinte. |
| **Ctrl+G** | Go to | Vai para um endereço de memória específico. |

---

> **Conclusão:** A depuração interativa é a única forma de garantir que o analista veja o que o malware tenta esconder. O domínio sobre instruções de salto e flags da CPU permite neutralizar mecanismos de defesa complexos.
