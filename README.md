# 🛡️ Active Directory Mastery: Engenharia de Identidade e Hardening

Este repositório apresenta um guia técnico de **Engenharia de Sistemas** focado na arquitetura, segurança e protocolos do **Active Directory Domain Services (AD DS)**. O material foi consolidado com o auxílio da Inteligência Artificial (NotebookLM), aplicando curadoria de fontes oficiais e engenharia de prompts.

---

## 🎯 Contexto e Objetivos
O objetivo deste projeto é documentar estratégias de **Defesa em Profundidade** para infraestruturas críticas. Em vez de focar apenas na administração básica, este guia explora como o AD funciona "sob o capô" e como proteger o coração da identidade corporativa contra ataques avançados.

---

## 📚 Curadoria de Fontes (Knowledge Base)
Para treinar o modelo de IA, foram utilizadas as seguintes fontes técnicas:
* **Microsoft Docs:** *Active Directory Replication Concepts & Topology*.
* **CIS Benchmarks:** *Windows Server Security Configuration Guide*.
* **NIST SP 800-207:** *Zero Trust Architecture (aplicado a Identidades)*.
* **RFC 4120:** *The Kerberos Network Authentication Service*.

---

## 🧠 Engenharia de Prompts (Cicatrizes de Aprendizado)
Para extrair este nível de detalhamento, utilizei prompts de **Chain-of-Thought (Cadeia de Pensamento)**:

* **Prompt de Desafio:** "Atue como um Arquiteto de Segurança. Analise a diferença entre os protocolos Kerberos e NTLM e crie um plano de migração que neutralize o ataque Pass-the-Hash."
* **Troubleshooting:** O modelo inicialmente ignorou a importância dos **Silos de Autenticação**. Refinei a consulta exigindo a correlação entre contas de serviço e políticas de restrição de logon.

---

## 📝 Guia de Hardening para Active Directory (Entrega Final)

### 1. Modelo de Administração em Camadas (Tiering)
Para isolar segredos de autenticação e impedir a movimentação lateral, a infraestrutura deve ser dividida em:

| Camada | Escopo | Descrição |
| :--- | :--- | :--- |
| **Tier 0** | **Identidades** | Controladores de Domínio (DCs) e servidores de segurança crítica. |
| **Tier 1** | **Servidores** | Aplicações de negócio, bancos de dados e servidores web. |
| **Tier 2** | **Estações** | Desktops de usuários finais e dispositivos móveis. |

> **Regra de Ouro:** Administradores de Tier 0 **nunca** devem logar em máquinas de Tier 1 ou 2 com suas contas privilegiadas.

### 2. Estações de Trabalho de Acesso Privilegiado (PAWs)
Ações administrativas não ocorrem em máquinas comuns.
* **Isolamento:** Uso de máquinas físicas dedicadas ou VDIs seguras.
* **Conectividade:** Bloqueio total de Internet e portas USB.
* **Modo Restricted Admin:** Habilitação via RDP para evitar que o Hash da senha fique na RAM da máquina de destino.

### 3. Fortalecimento de Protocolos (Kerberos vs NTLM)
* **Morte ao NTLM:** Substituição integral pelo **Kerberos**, que utiliza tickets criptografados e mútua autenticação.
* **Kerberos Armoring (FAST):** Criação de um túnel seguro para o tráfego inicial de autenticação (TGT), dificultando interceptações.
* **Silos de Autenticação:** Vinculação estrita de contas a hosts específicos, forçando o uso de PAWs.

### 4. Gestão de Privilégios (PAM & ZSP)
* **Zero Standing Privilege (ZSP):** Ninguém tem "poder total" o tempo todo. O privilégio é concedido via **Just-In-Time (JIT)**.
* **Protected Users Group:** Inclusão de contas críticas neste grupo para desativar automaticamente delegações perigosas e protocolos legados.
* **MFA Obrigatório:** Uso de tokens físicos (FIDO2) para qualquer elevação de privilégio.

### 5. Proteção do Banco de Dados e Schema
* **DCs Dedicados:** Jamais instale SQL, Exchange ou IIS em um Controlador de Domínio.
* **Schema Admins:** Este grupo deve estar **vazio** por padrão. Modificações no Schema são irreversíveis no banco de dados `NTDS.dit` e devem passar por aprovação rigorosa em laboratório antes da produção.

---

## 🛠️ Ferramentas Sugeridas para Auditoria
Para validar este Hardening, utilize:
1.  **PingCastle:** Auditoria de risco de Active Directory.
2.  **BloodHound:** Visualização de caminhos de ataque e privilégios excessivos.
3.  **PurpleKnight:** Scanner de segurança para vulnerabilidades de infraestrutura.
