# Fase 1: Preparação - Resposta a Incidentes (IR)

Este documento detalha o estágio inicial do ciclo de vida de um incidente, estabelecendo as bases de governança, prontidão tecnológica e visibilidade necessárias para uma operação de **Purple Team** e **Blue Team** eficaz.

---

## 1. Visão Geral da Resposta a Incidentes
A resposta a incidentes (IR) visa detectar e gerenciar ataques adversários para minimizar impactos. No contexto deste projeto, diferenciamos:
* **Evento:** Ocorrência observada em um sistema ou rede (ex: login de usuário).
* **Incidente:** Violação das políticas de segurança com intenção maliciosa (ex: ransomware).

---

## 2. Fortalecendo a Prontidão Organizacional
A preparação é o alicerce que garante que a organização possa reagir e se recuperar de forma ágil através de cinco elementos críticos:

### Pessoas e Equipe CSIRT
* **Criação da Equipe:** Estabelecimento de uma equipe multidisciplinar (Técnica, Jurídica, PR e Negócios).
* **Capacitação:** Treinamento em ferramentas forenses, análise de logs de auditoria e campanhas de conscientização (Phishing/Engenharia Social).

### Documentação e Políticas
* **Diretrizes:** Definição clara de autoridade para monitoramento e limites de privacidade.
* **Procedimentos (Playbooks):** Processos ordenados para agilizar a contenção e erradicação.

### Cadeia de Custódia
O manuseio de evidências é registrado para garantir a integridade do processo investigativo.
* **Formulário de Evidência:** Registro de Matter/Caso, descrição detalhada dos itens (modelo/serial) e histórico de movimentação.

---

## 3. Preparação Tecnológica e Inventário
Conhecer a infraestrutura é essencial para priorizar a proteção de ativos de alto valor.

### Inventário de Ativos (Exemplo)
| Tipo de Ativo | Nome do Ativo | Sistema Operacional | Endereço IP |
| :--- | :--- | :--- | :--- |
| Servidor de e-mail | Servidor de Correio 1 | Windows Server 2019 | 192.168.0.2 |
| Servidor Web | Servidor Web 1 | Ubuntu Server 20.04 | 192.168.0.3 |
| VPN | Servidor VPN 1 | Ubuntu Server 20.04 | 192.168.0.4 |

### 🛠️ Instrumentação e Telemetria
Mapeamento de sensores para mitigação e detecção:
* **Mecanismos:** EDR, Antimalware, DLP e IDPS.
* **Centralização:** Uso de SIEM e ferramentas como *TheHive* para rastreamento.
* **Investigação Forense:** Capacidade de coleta de disco/memória e uso de *Sandboxes*.

---

## 4. Visibilidade Digital e Gestão de Logs
A visibilidade evita a operação "às cegas", permitindo identificar TTPs adversários em tempo real.

### Tipos de Logs Monitorados
* **Evento/Auditoria:** Registros de acessos e ações (Sucesso/Falha).
* **Erro/Depuração:** Identificação de falhas técnicas e resolução de problemas.

### Fontes de Dados
* **Rede/Perímetro:** Switches, Firewalls e VPNs.
* **Host/Aplicação:** Eventos do SO e serviços internos.

---

## 5. Configuração e Validação (Prática)

### Hardening de Visibilidade no Windows
Em sistemas sensíveis, aplicamos políticas para ocultar informações de login na tela de bloqueio e garantimos a ativação de serviços de log via Registro:
* **Chave:** `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog`
* **Ação:** Alteração do valor `Start` para `2` (Automático).

### Validação com Atomic Red Team
Para confirmar se a telemetria está operando, simulamos um ataque de Ransomware (**T1486**):

```powershell
# Executar simulação de nota de resgate (PureLocker)
Invoke-AtomicTest T1486-5

Evidência Complementar: Preparation.pdf
