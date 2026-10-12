# Caso 5 — Detecção de Criação de Usuário

## 1. Objetivo

O objetivo deste teste foi validar se o Wazuh consegue detectar a criação de novas contas de usuário na máquina Linux monitorada, identificando alterações relacionadas ao gerenciamento de usuários do sistema.

## 2. Criação do usuário de teste

Na máquina alvo (`ubuntu-target`), executei o comando abaixo para criar um usuário de teste:

```bash
sudo useradd soc_teste
```

Em seguida, verifiquei se o usuário havia sido adicionado ao arquivo `/etc/passwd`:

```bash
grep "soc_teste" /etc/passwd
```

O comando retornou uma entrada correspondente ao usuário `soc_teste`, confirmando que a conta havia sido criada.

## 3. Detecção no Wazuh Dashboard

Após criar o usuário, acessei o Wazuh Dashboard pelo navegador e naveguei até:

**Security Events → Events**

Realizei a consulta dos eventos de segurança e identifiquei um alerta relacionado à criação de usuário.

O Wazuh apresentou o seguinte evento:

`New user added to the system.`

### Informações do alerta

- **Rule ID:** `5902`
- **Nível:** `8`
- **Descrição:** `New user added to the system.`
- **Máquina monitorada:** `ubuntu-target`

**[INSERIR PRINT: alerta de criação de usuário no Wazuh Dashboard]**

## 4. Análise do evento

O alerta demonstrou que o Wazuh identificou a criação de uma nova conta de usuário no sistema Linux.

Esse tipo de monitoramento é importante porque a criação de contas pode fazer parte de atividades administrativas legítimas, mas também pode representar um risco quando realizada sem autorização.

A análise do evento permite investigar a alteração e verificar se a criação do usuário era esperada.

## 5. Remoção do usuário de teste

Após concluir o teste, removi a conta criada para evitar manter um usuário desnecessário no ambiente.

Executei:

```bash
sudo userdel soc_teste
```

Depois, verifiquei se o usuário ainda aparecia no arquivo `/etc/passwd`:

```bash
grep "soc_teste" /etc/passwd
```

A ausência de resultados confirmou que a entrada do usuário não estava mais presente nesse arquivo.

## 6. Resultado

**Resultado: ✅ Teste concluído com sucesso.**

O Wazuh detectou a criação do usuário de teste e gerou um alerta de nível 8, identificado pela regra `5902`.

## 7. Conclusão

Este teste demonstrou a capacidade do Wazuh de identificar alterações relacionadas à criação de contas de usuário em um endpoint Linux.

A detecção contribui para o monitoramento de atividades administrativas e auxilia na investigação de possíveis alterações não autorizadas no sistema.

O cenário reforça a importância de monitorar eventos relacionados ao gerenciamento de usuários em um ambiente de segurança da informação.
