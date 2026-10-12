# Caso 4 — File Integrity Monitoring (FIM)

## 1. Objetivo

O objetivo deste teste foi validar a capacidade do Wazuh de detectar alterações na integridade dos arquivos, incluindo criação, modificação e exclusão.

## 2. Teste inicial

Na máquina alvo (`ubuntu-target`), criei um arquivo de teste no diretório `/tmp` utilizando o comando:

```bash
echo "teste SOC" > /tmp/arquivo_soc.txt
```

Após aguardar alguns segundos, modifiquei o conteúdo do arquivo:

```bash
echo "arquivo modificado" >> /tmp/arquivo_soc.txt
```

Em seguida, acessei o Wazuh Dashboard e naveguei até:

**Security Events → Events**

Pesquisei pelos termos `syscheck` e `arquivo_soc`, mas os eventos esperados não foram encontrados.

![Modificação](/evidencias/modificacao1.png)

## 3. Ajuste da configuração do FIM

A ausência dos alertas indicou que o diretório `/tmp` provavelmente não estava incluído na configuração de monitoramento do File Integrity Monitoring (FIM) do agente.

Para corrigir isso, acessei a máquina `ubuntu-target` e editei o arquivo de configuração do Wazuh Agent:

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Dentro da seção `<syscheck>`, adicionei a seguinte configuração:

```xml
<directories realtime="yes">/tmp</directories>
```

Essa configuração habilita o monitoramento em tempo real do diretório `/tmp`.

Após salvar o arquivo, reiniciei o agente:

```bash
sudo systemctl restart wazuh-agent
```

Em seguida, verifiquei o estado do serviço:

```bash
sudo systemctl status wazuh-agent
```

O serviço estava ativo (`active (running)`).

## 4. Novo teste de integridade

Após a alteração da configuração, executei novamente os testes na máquina `ubuntu-target`.

### Criação do arquivo

```bash
echo "teste SOC" > /tmp/arquivo_soc.txt
```

### Modificação do arquivo

```bash
echo "arquivo modificado" >> /tmp/arquivo_soc.txt
```

Esses comandos permitiram testar a criação do arquivo e a alteração de seu conteúdo.

## 5. Validação no Wazuh Dashboard

Acessei novamente o Wazuh Dashboard em:

**Security Events → Events**

Pesquisei pelo termo `syscheck` e identifiquei os eventos relacionados às alterações realizadas no arquivo.

Foram observados os seguintes alertas:

| Rule ID | Descrição | Nível |
|---|---|---|
| `554` | `File added to the system.` | 5 |
| `550` | `Integrity checksum changed.` | 7 |

![Modificação 2](/evidencias/changed4.png)

Os eventos confirmaram que o Wazuh passou a monitorar o diretório `/tmp` e identificar as alterações realizadas no arquivo de teste.

## 6. Resultado

**Resultado: ✅ Teste concluído com sucesso.**

Após a configuração do FIM para monitorar `/tmp`, o Wazuh detectou a criação do arquivo e a alteração de seu conteúdo.

## 7. Conclusão

Este teste demonstrou a importância da configuração adequada do File Integrity Monitoring para identificar mudanças em arquivos do sistema.

A configuração do monitoramento em tempo real permitiu gerar alertas relacionados à integridade dos arquivos e visualizar os eventos no Wazuh Dashboard.

Essa funcionalidade contribui para identificar alterações inesperadas em arquivos e auxilia na investigação de atividades potencialmente suspeitas.

**Observação:** a exclusão do arquivo também fazia parte do objetivo do teste, mas os alertas documentados nesta execução confirmam especificamente a criação e a alteração. A exclusão deve ser registrada como detectada somente se houver um evento correspondente no Dashboard.
