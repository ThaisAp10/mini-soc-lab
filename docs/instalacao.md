# 🔧 Instalação e Configuração do Mini SOC com Wazuh

Este documento descreve a instalação e configuração do laboratório de **Mini SOC**, utilizando o Wazuh como plataforma SIEM para monitoramento e detecção de eventos de segurança em um ambiente Linux.

---

## 🏗️ 1. Arquitetura do laboratório

O laboratório foi desenvolvido utilizando duas máquinas virtuais no VirtualBox.

| Máquina | Sistema | Função |
|---|---|---|
| `soc-server` | Ubuntu Server | Wazuh Server, Manager, Indexer e Dashboard |
| `ubuntu-target` | Ubuntu Desktop | Máquina monitorada, Wazuh Agent e Auditd |

### Fluxo do laboratório

```text
                    ┌──────────────────────┐
                    │      SOC-SERVER      │
                    │                      │
                    │ Wazuh Manager        │
                    │ Wazuh Indexer        │
                    │ Wazuh Dashboard      │
                    └──────────┬───────────┘
                               │
                         Wazuh Agent
                               │
                    ┌──────────▼───────────┐
                    │    UBUNTU-TARGET     │
                    │                      │
                    │ Wazuh Agent          │
                    │ Auditd               │
                    │ Logs do sistema      │
                    └──────────────────────┘
```
## 💻 2. Criação das máquinas virtuais

As máquinas virtuais foram criadas utilizando o **VirtualBox**.

Foram utilizadas duas VMs:

- **soc-server**
- **ubuntu-target**

O servidor foi utilizado para executar a infraestrutura do **Wazuh**, enquanto a máquina **ubuntu-target** foi utilizada como endpoint monitorado.


## 🌐 3. Configuração da rede

Foi utilizada uma combinação de:

- **Adaptador em modo Bridge**
- **Adaptador Host-Only**

### soc-server

**Configuração:**

- Adaptador 1 → **Bridge**
- Adaptador 2 → **Host-Only**

O adaptador **Bridge** permite acesso à rede externa, enquanto o **Host-Only** foi utilizado para a comunicação entre as máquinas virtuais.

### ubuntu-target

**Configuração:**

- Adaptador 1 → **Host-Only**

Dessa forma, o ambiente possui uma rede interna para comunicação entre o **servidor Wazuh** e o **endpoint**.


## 🖥️ 4. Configuração de rede do Ubuntu Server

No **soc-server**, foi realizada a configuração da interface de rede através do **Netplan**.

O arquivo utilizado foi:

```text
/etc/netplan/00-installer-config.yaml
```
A interface foi configurada para utilizar DHCP.

Exemplo da configuração utilizada:
```
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
```
Após a configuração, foi aplicada a rede:
```
sudo netplan apply
```
A conectividade foi verificada utilizando:
```
ip addr
```
E também através do comando:
```
ping <IP>
```
## 🛡️ 5. Instalação do Wazuh Server

O **Wazuh** foi instalado no **soc-server**.

A instalação configurou os principais componentes necessários para o funcionamento do **SIEM**:

- **Wazuh Manager**
- **Wazuh Indexer**
- **Wazuh Dashboard**

Após a instalação, os serviços foram verificados com:

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard

```
Os serviços foram confirmados como:
```
active (running)
```
## 🌐 6. Acesso ao Wazuh Dashboard

Após a instalação, o **Dashboard do Wazuh** foi acessado através do navegador da máquina local.

Foi utilizado o endereço IP do servidor Wazuh na rede.

Exemplo:

```text
https://192.168.x.x
```
O endereço IP exato depende da configuração de rede atribuída à VM.

O Dashboard permite visualizar os eventos enviados pelos agentes e realizar a investigação dos alertas de segurança.

## 🖥️ 7. Instalação do Wazuh Agent

Na máquina **ubuntu-target**, foi instalado o **Wazuh Agent**.

O agente é responsável por coletar informações do endpoint e enviá-las para o **Wazuh Manager**.

Após a instalação, o serviço foi verificado:

```bash
sudo systemctl status wazuh-agent
```
O serviço foi confirmado como:
```
active (running)
```

## 🔗 8. Conexão do Agent com o Wazuh Server

O **ubuntu-target** foi configurado para se comunicar com o **soc-server**.

Após a configuração e inicialização do agente, o endpoint passou a aparecer no **Wazuh Dashboard**.

A comunicação foi validada através do próprio **Dashboard**, onde o agente passou a enviar eventos para o servidor.

## 📋 9. Instalação do Auditd

Para aumentar a capacidade de monitoramento do endpoint, foi instalado o **Auditd** na máquina **ubuntu-target**.

O **Auditd** permite registrar eventos relacionados às atividades realizadas no sistema Linux.

### Instalação

```bash
sudo apt update
sudo apt install auditd audispd-plugins -y
```
O serviço foi verificado com:
```
sudo systemctl status auditd
```
Resultado esperado:
```
active (running)
```
## 🔎 10. Integração do Auditd com o Wazuh

O **Wazuh Agent** foi configurado para coletar os eventos gerados pelo **Auditd**.

Foi adicionada a seguinte configuração ao arquivo:

```text
/var/ossec/etc/ossec.conf
```
Configuração
```
<localfile>
  <log_format>audit</log_format>
  <location>/var/log/audit/audit.log</location>
</localfile>
```
Após a alteração, o agente foi reiniciado:
```
sudo systemctl restart wazuh-agent
```
## 🧪 11. Validação do ambiente

Após a instalação, foram verificados os principais serviços.

### No soc-server

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```
No ubuntu-target
```
sudo systemctl status wazuh-agent
sudo systemctl status auditd
```
Todos os serviços necessários foram confirmados como ativos.

## 📊 12. Primeiro teste de monitoramento

Após a configuração do ambiente, foram realizados testes para verificar se os eventos do endpoint estavam chegando ao **Wazuh**.

Entre os testes realizados posteriormente estão:

- Uso de **sudo** e privilégios **ROOT**
- Acesso ao arquivo `/etc/shadow`
- Tentativas de **Brute Force via SSH**
- Alterações em arquivos utilizando **FIM**
- Criação de usuário no sistema

Os eventos foram posteriormente analisados através do **Wazuh Dashboard**.

## 🏁 13. Resultado da instalação

Ao final da configuração, o laboratório ficou estruturado da seguinte forma:

```text
┌─────────────────────────────────────┐
│             SOC-SERVER              │
│                                     │
│  Ubuntu Server                      │
│  ├── Wazuh Manager                  │
│  ├── Wazuh Indexer                  │
│  └── Wazuh Dashboard                │
│                                     │
└──────────────────┬──────────────────┘
                   │
                   │ Wazuh Agent
                   │
┌──────────────────▼──────────────────┐
│           UBUNTU-TARGET             │
│                                     │
│  Ubuntu Desktop                     │
│  ├── Wazuh Agent                    │
│  ├── Auditd                         │
│  └── Logs do sistema                │
│                                     │
└─────────────────────────────────────┘
```
O ambiente ficou funcional para realização de testes de segurança, geração de eventos, detecção e investigação através do Wazuh.
```

```
---
📚 Próximos documentos

Os testes realizados no laboratório estão documentados separadamente:
```
docs/
├── instalacao.md
├── caso-01-sudo.md
├── caso-02-shadow.md
├── caso-03-bruteforce-ssh.md
├── caso-04-fim.md
├── caso-05-criacao-usuario.md
└── caso-06-resposta-incidente.md
```
✅ Status

Instalação e configuração do Mini SOC: concluída.

Fase 1 do laboratório: concluída.


