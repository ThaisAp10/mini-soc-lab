# 🛡️ Mini SOC Lab

Projeto de laboratório desenvolvido com o objetivo de simular um ambiente de **Security Operations Center (SOC)**, utilizando o **Wazuh** para monitoramento, detecção e análise de eventos de segurança.

O laboratório foi construído utilizando máquinas virtuais, permitindo simular atividades suspeitas em um ambiente controlado e analisar como um SIEM identifica e registra esses eventos.

---

## 🎯 Objetivo

O objetivo deste projeto é desenvolver conhecimentos práticos em:

- Monitoramento de segurança
- SIEM (Security Information and Event Management)
- Análise de eventos e alertas
- Detecção de atividades suspeitas
- Investigação de incidentes
- File Integrity Monitoring (FIM)
- Monitoramento de autenticação
- Linux e gerenciamento de usuários
- Auditd
- MITRE ATT&CK

---

## 🏗️ Arquitetura do Laboratório

O ambiente foi desenvolvido utilizando duas máquinas virtuais:

| Máquina | Função | Sistema |
|---|---|---|
| `soc-server` | Wazuh Server, Manager, Indexer e Dashboard | Ubuntu Server |
| `ubuntu-target` | Máquina monitorada | Ubuntu Desktop |

### Fluxo do ambiente

```text
┌─────────────────────┐
│    ubuntu-target    │
│                     │
│  Ubuntu + Wazuh     │
│      Agent          │
│                     │
│      Auditd         │
└──────────┬──────────┘
           │
           │ Eventos de segurança
           ▼
┌─────────────────────┐
│     soc-server      │
│                     │
│   Wazuh Manager     │
│   Wazuh Indexer     │
│   Wazuh Dashboard   │
└─────────────────────┘
```

## 🛠️ Tecnologias utilizadas

- **Wazuh**
- **Wazuh Agent**
- **Wazuh Manager**
- **Wazuh Indexer**
- **Wazuh Dashboard**
- **Auditd**
- **Ubuntu Server**
- **Ubuntu Desktop**
- **VirtualBox**

---

## 🔐 Cenários de Segurança Testados

Durante o desenvolvimento do laboratório foram realizados diferentes cenários de segurança.

### 1. Detecção de uso de privilégios ROOT

Foi realizada uma atividade utilizando `sudo` para verificar a capacidade do Wazuh de identificar operações com privilégios elevados.

**Resultado:** ✅ Detectado pelo Wazuh.

---

### 2. Detecção de acesso ao `/etc/shadow`

Foi configurado o Auditd para monitorar o acesso ao arquivo `/etc/shadow`, que contém informações sensíveis relacionadas às contas do sistema.

Uma regra personalizada do Wazuh foi criada para identificar esse comportamento.

**Resultado:** ✅ Detectado pelo Wazuh.

**Regra personalizada:** `100101`

**Nível:** `10`

---

### 3. Detecção de Brute Force SSH

Foram realizadas várias tentativas de autenticação SSH utilizando credenciais incorretas.

O Wazuh identificou as tentativas de autenticação malsucedidas.

**Resultado:** ✅ Detectado pelo Wazuh.

**Rule ID:** `5760`

**MITRE ATT&CK:**

- `T1110.001 – Password Guessing`
- `T1021.004 – SSH`

---

### 4. File Integrity Monitoring (FIM)

Foi configurado o monitoramento de `/tmp` utilizando o recurso de File Integrity Monitoring do Wazuh.

Foram realizados testes de:

- Criação de arquivo
- Alteração de arquivo
- Remoção de arquivo

O Wazuh identificou alterações na integridade dos arquivos.

**Resultado:** ✅ Detectado pelo Wazuh.

**Regras observadas:**

- `554` — File added to the system
- `550` — Integrity checksum changed

---

### 5. Detecção de criação de usuário

Foi criado um usuário de teste para verificar a capacidade do Wazuh de identificar alterações relacionadas às contas do sistema.

**Resultado:** ✅ Detectado pelo Wazuh.

**Rule ID:** `5902`

**Nível:** `8`

**Descrição:** `New user added to the system.`

---

### 6. Análise e resposta a incidente

Foi realizada uma análise de um incidente de **Brute Force SSH** previamente detectado pelo Wazuh.

Foram analisadas as evidências disponíveis no Wazuh e nos logs do sistema, permitindo identificar:

- Host afetado
- Usuário alvo
- Endereço IP de origem
- Tentativas de autenticação malsucedidas
- Regra de detecção
- Técnica relacionada ao MITRE ATT&CK

Após a análise, foi constatado que as tentativas de autenticação não foram bem-sucedidas e não havia evidências de comprometimento da conta.

**Resultado:** ✅ Incidente identificado, analisado e documentado.

---


## 📊 Resultados

Os testes demonstraram a capacidade do Wazuh de monitorar e identificar diferentes tipos de eventos de segurança em um ambiente Linux.

| Cenário | Resultado |
|---|---|
| Sudo / ROOT | ✅ Detectado |
| Acesso ao `/etc/shadow` | ✅ Detectado |
| Brute Force SSH | ✅ Detectado |
| File Integrity Monitoring | ✅ Detectado |
| Criação de usuário | ✅ Detectado |
| Análise e resposta a incidente | ✅ Detectado |

---

## 📚 Documentação

Os procedimentos e evidências de cada cenário estão organizados na pasta `docs/`.

```text
docs/
├── instalacao.md
├── caso-01-sudo.md
├── caso-02-shadow.md
├── caso-03-bruteforce-ssh.md
├── caso-04-fim.md
├── caso-05-criacao-usuario.md
└── caso-06-resposta-incidente.md
