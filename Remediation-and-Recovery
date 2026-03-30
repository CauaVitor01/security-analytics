
# Fases 4 e 5: Erradicação, Remediação e Recuperação

Este documento detalha o processo de expulsão do adversário, o fortalecimento da infraestrutura e o retorno seguro das operações.

-----

## 1\. Erradicação: Eliminando a Presença do Adversário

A erradicação não é apenas deletar arquivos; é garantir que o atacante perca todos os pontos de acesso.

### O Perigo da Pressão e Transição Prematura

Mover-se para a erradicação sem um escopo completo (por pressão da gestão ou medo) pode alertar o atacante, levando-o a acelerar a destruição de dados ou criar novas persistências ocultas.

### Técnicas Aplicadas

  * **Erradicação Automatizada:** Uso de EDR/AV para ameaças conhecidas (agilidade).
  * **Reconstrução Completa (The "Nuke" Option):** Formatação total do endpoint. Recomendado para garantir 100% de limpeza em sistemas não críticos.
  * **Limpeza Direcionada:** Remoção manual de artefatos em servidores de alta disponibilidade para evitar *downtime* milionário.

-----

## 2\. Remediação: Hardening e Causa Raiz

A remediação transforma o incidente em oportunidade de melhoria para que a falha não se repita.

### Medidas de Fortalecimento

  * **Segmentação de Rede:** Implementação de regras de firewall para impedir o movimento lateral (Tráfego Leste-Oeste).
  * **Revisão de IAM (Identidade):** Aplicação do **Princípio do Menor Privilégio (PoLP)** e auditoria rigorosa de contas de Administradores de Domínio.
  * **Gestão de Patches:** Correção imediata da vulnerabilidade explorada, estendendo a atualização para **toda a organização** (escalabilidade).

-----

## 3\. Recuperação: Retorno Seguro à Produção

O objetivo é restaurar a normalidade operacional sob monitoramento intensivo.

### Estratégia de Retorno

1.  **Validação:** Realização de simulações de ataque (Pentest direcionado) para testar se as novas defesas resistem ao vetor original.
2.  **Restauração:** Uso de backups de configuração e *Golden Images* para reduzir o tempo de inatividade (RTO).
3.  **Vigilância Pós-Incidente:** Monitoramento de telemetria reforçado nos sistemas reintroduzidos para detectar recidivas.

### Plano de Ação (Timeline)

| Prazo | Foco | Exemplos |
| :--- | :--- | :--- |
| **Curto** | Crítico | Reset de senhas, patches de emergência. |
| **Médio** | Estrutural | Implementação de MFA em larga escala. |
| **Longo** | Estratégico | Revisão da arquitetura de segurança e cultura de TI. |

-----

> 📂 **Documento PDF Relacionado:** [Remediation\_and\_Recovery.pdf](./docs/Remediation_and_Recovery.pdf)

