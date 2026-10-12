# Caso 2 — Detecção de Acesso ao `/etc/shadow`

## 1. Objetivo

O objetivo deste teste foi verificar se o Wazuh conseguiria detectar especificamente o acesso ao arquivo `/etc/shadow`, que contém informações sensíveis relacionadas às contas de usuários do Linux.

## 2. Teste inicial

Na máquina alvo (`ubuntu-target`), foi executado o comando:

```bash
sudo cat /etc/shadow
```

Em seguida, no Wazuh Dashboard, acessei **Security Events → Events** e pesquisei pelo termo `shadow`.

O evento retornado foi:

`Successful sudo to ROOT executed`

Ao pesquisar por `root`, foram encontrados eventos semelhantes aos observados no Caso 1.

**Resultado inicial:** o Wazuh identificava o uso de `sudo`, mas ainda não apresentava uma detecção específica para o acesso ao arquivo `/etc/shadow`.

## 3. Instalação do Auditd

Para registrar o acesso ao arquivo, foi instalado o Auditd na máquina `ubuntu-target`.

```bash
sudo apt update
sudo apt install auditd audispd-plugins -y
```

Depois, verifiquei o estado do serviço:

```bash
sudo systemctl status auditd
```

O serviço estava ativo (`active (running)`).

## 4. Configuração da regra de auditoria

Foi criado um arquivo de regras para monitorar o acesso de leitura ao `/etc/shadow`:

```bash
sudo nano /etc/audit/rules.d/wazuh-shadow.rules
```

Dentro do arquivo, foi adicionada a seguinte regra:

```text
-w /etc/shadow -p r -k shadow_access
```

- `-w /etc/shadow`: monitora o arquivo.
- `-p r`: monitora operações de leitura.
- `-k shadow_access`: define uma chave para identificar os eventos.

Após salvar o arquivo, carreguei as regras:

```bash
sudo augenrules --load
```

Para confirmar que a regra estava carregada, executei:

```bash
sudo auditctl -l | grep shadow
```

A saída confirmou a presença da regra:

```text
-w /etc/shadow -p r -k shadow_access
```

## 5. Validação do Auditd

Para gerar um evento, executei novamente:

```bash
sudo cat /etc/shadow
```

Depois, consultei os registros do Auditd:

```bash
sudo ausearch -k shadow_access
```

O comando retornou o registro correspondente ao acesso ao arquivo.

**[INSERIR PRINT: regra carregada no Auditd e evento retornado pelo ausearch]**

Esse resultado confirmou que o Auditd estava registrando o acesso ao `/etc/shadow` utilizando a chave `shadow_access`.

## 6. Configuração do Wazuh Agent

Para que o Wazuh Agent coletasse os registros do Auditd, editei o arquivo de configuração na máquina `ubuntu-target`:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Dentro de uma seção `<ossec_config>`, adicionei a seguinte configuração:

```xml
<localfile>
  <log_format>audit</log_format>
  <location>/var/log/audit/audit.log</location>
</localfile>
```

Após salvar o arquivo, reiniciei o agente:

```bash
sudo systemctl restart wazuh-agent
```

Verifiquei se o serviço estava ativo:

```bash
sudo systemctl status wazuh-agent
```

O serviço estava em execução (`active (running)`).

Em seguida, gerei um novo evento:

```bash
sudo cat /etc/shadow
sudo ausearch -k shadow_access
```

No Dashboard, pesquisei novamente por `shadow`. Entretanto, o evento ainda aparecia associado à detecção genérica de uso de `sudo`.

![Config shadow1](/evidencias/config_shadow1.png)
![Config shadow2](/evidencias/config_shadow2.png)

## 7. Criação de uma regra personalizada no Wazuh

Para gerar um alerta específico, foi criada uma regra personalizada no servidor `soc-server`.

Editei o arquivo:

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
```

Dentro da estrutura `<group>` existente, adicionei:

```xml
<rule id="100101" level="10">
    <if_sid>80700</if_sid>
    <field name="audit.key">shadow_access</field>
    <description>Audit: Access to /etc/shadow detected</description>
    <group>audit,privilege_escalation,</group>
</rule>
```

A regra foi configurada com os seguintes parâmetros:

- **Rule ID:** `100101`
- **Nível:** `10`
- **Regra base:** `80700`
- **Campo monitorado:** `audit.key`
- **Chave esperada:** `shadow_access`

A regra base identifica eventos provenientes do Auditd, enquanto a condição `audit.key` verifica se o evento contém a chave configurada para o acesso ao arquivo.

Após salvar o arquivo, reiniciei o Wazuh Manager:

```bash
sudo systemctl restart wazuh-manager
```

Verifiquei o serviço:

```bash
sudo systemctl status wazuh-manager
```

## 8. Teste final e validação

Na máquina `ubuntu-target`, executei novamente:

```bash
sudo cat /etc/shadow
```

Depois, acessei o Wazuh Dashboard e pesquisei por `shadow_access` em **Security Events → Events**.

O alerta esperado foi identificado:

`Audit: Access to /etc/shadow detected`

![Result shadow](/evidencias/result_shadowaccess.png)

O evento apresentou:

- **Rule ID:** `100101`
- **Nível:** `10`
- **Descrição:** `Audit: Access to /etc/shadow detected`

## 9. Resultado

**Resultado: ✅ Teste concluído com sucesso.**

A configuração permitiu monitorar o acesso de leitura ao arquivo `/etc/shadow` por meio do Auditd, encaminhar os registros ao Wazuh e gerar um alerta específico utilizando uma regra personalizada.

## 10. Conclusão

Este teste demonstrou a integração entre Auditd e Wazuh para monitorar o acesso a um arquivo sensível do Linux.

A criação da regra personalizada permitiu diferenciar o acesso ao `/etc/shadow` dos eventos genéricos relacionados ao uso de `sudo`, facilitando a identificação e a investigação de atividades relevantes para a segurança do sistema.
