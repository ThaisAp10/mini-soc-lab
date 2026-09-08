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
│  Ubuntu + Wazuh    │
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
