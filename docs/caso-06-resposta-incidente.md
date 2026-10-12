# Caso 6 — Análise e Resposta a Incidente de Segurança

## 1. Objetivo

O objetivo deste teste foi realizar a análise de um incidente de segurança relacionado a tentativas repetidas de autenticação SSH malsucedidas, detectadas durante o Caso 3 — Brute Force SSH.

A atividade teve como finalidade praticar o processo de identificação, investigação, avaliação e documentação de um incidente utilizando o Wazuh e os logs do sistema Linux.

## 2. Identificação do incidente

No Wazuh Dashboard, acessei:

**Security Events → Events**

Realizei uma pesquisa pelos eventos relacionados a falhas de autenticação SSH e identifiquei o alerta:

`sshd: authentication failed.`

### Informações do alerta

- **Rule ID:** `5760`
- **Nível:** `5`
- **Host afetado:** `ubuntu-target`
- **IP do alvo:** `192.168.56.102`
- **IP de origem:** `192.168.56.101`
- **Usuário alvo:** `thais`
- **MITRE ATT&CK:** `T1110.001 – Password Guessing`
- **MITRE ATT&CK:** `T1021.004 – SSH`

**[INSERIR PRINT: alerta de falha de autenticação SSH no Wazuh Dashboard]**

## 3. Investigação do incidente

Após identificar o alerta, abri os detalhes do evento no Dashboard para analisar as informações disponíveis.

Durante a investigação, foram verificados:

- O host afetado.
- O usuário utilizado nas tentativas de autenticação.
- O endereço IP de origem.
- A quantidade de tentativas malsucedidas.
- O horário dos eventos.
- A regra de detecção acionada.
- As técnicas relacionadas no MITRE ATT&CK.

O evento apresentou três tentativas de autenticação malsucedidas, permitindo identificar a origem das tentativas e o usuário alvo.

**[INSERIR PRINT: detalhes do alerta, incluindo IP de origem, usuário e quantidade de eventos]**

## 4. Validação nos logs do sistema

Para confirmar as informações apresentadas pelo Wazuh, acessei a máquina `ubuntu-target` e consultei os registros locais de autenticação.

Executei o comando:

```bash
sudo grep "Failed password" /var/log/auth.log | tail -10
```

Esse comando permitiu visualizar as últimas tentativas de autenticação SSH malsucedidas registradas no sistema.

Também consultei o histórico de tentativas de login malsucedidas com:

```bash
sudo lastb | head -10
```

**[INSERIR PRINT: logs de autenticação no terminal da máquina ubuntu-target]**

A análise dos logs confirmou a existência das tentativas malsucedidas identificadas pelo Wazuh.

## 5. Avaliação do incidente

Com base nas evidências disponíveis, foi possível concluir que ocorreram tentativas repetidas de autenticação SSH utilizando credenciais incorretas.

Os registros analisados não apresentaram evidências de autenticação bem-sucedida ou de comprometimento da conta durante o cenário investigado.

O incidente foi classificado como uma atividade suspeita que exigia investigação, pois tentativas repetidas de autenticação podem indicar uma tentativa de adivinhação de senhas.

## 6. Resposta ao incidente

Após a análise, foram realizadas as seguintes ações:

1. Identificação do alerta no Wazuh Dashboard.
2. Análise dos detalhes do evento.
3. Identificação do host afetado, usuário alvo e IP de origem.
4. Validação das tentativas nos logs locais do sistema.
5. Avaliação das evidências disponíveis.
6. Documentação dos resultados da investigação.

Como não foram encontradas evidências de comprometimento da conta durante o teste, não foi necessário realizar bloqueio de IP ou alteração emergencial da configuração do SSH.

A resposta foi baseada nas evidências observadas no laboratório.

## 7. Resultado

**Resultado: ✅ Incidente analisado e documentado.**

O Wazuh permitiu identificar as falhas de autenticação SSH, enquanto os logs locais auxiliaram na validação e investigação do evento.

A análise reuniu informações relevantes para compreender a atividade suspeita e avaliar a necessidade de ações adicionais.

## 8. Conclusão

Este caso demonstrou a aplicação prática de um fluxo básico de resposta a incidentes de segurança, desde a identificação do alerta até a investigação e documentação das evidências.

A utilização conjunta do Wazuh Dashboard e dos logs de autenticação do Linux permitiu analisar as tentativas de acesso indevido e verificar se havia indícios de comprometimento.

O exercício reforçou a importância de correlacionar alertas do SIEM com os registros do sistema antes de definir as ações de resposta.

**Fluxo aplicado:**

```text
Detecção
   ↓
Investigação
   ↓
Identificação da origem
   ↓
Validação dos logs
   ↓
Avaliação do impacto
   ↓
Resposta e documentação
```
