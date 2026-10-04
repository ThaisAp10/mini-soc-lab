# Caso 1 — Detecção de Elevação de Privilégios (Sudo/ROOT)

O primeiro teste teve como objetivo validar se o Wazuh estava capturando corretamente eventos relacionados à elevação de privilégios utilizando `sudo` e `root`, e exibindo esses eventos no Dashboard.

## 1. Teste na máquina alvo

Na máquina `Ubuntu-Target`, foi executado o comando:
```bash
sudo su
```

Em seguida, foi utilizado:
```bash
whoami
```

O comando retornou:
```bash
root
```

Isso confirmou que a sessão havia sido elevada para o usuário root.

Após o teste, foi executado:
```bash
exit
```

para sair da sessão com privilégios de root.

## 2. Verificação no Wazuh Dashboard
No navegador, foi acessado o:
```bash
Modules → Security Events → Events
```

Foi realizada uma pesquisa utilizando o termo:
```bash
sudo
```

O Wazuh apresentou o evento:
```bash
Successful sudo to ROOT executed
```

Também foram identificados eventos relacionados à sessão de autenticação e ao uso de sudo.

## 3. Resultado
O teste confirmou que o agente Wazuh está enviando corretamente os eventos relacionados ao uso de sudo para o Wazuh Manager.

Também foi validado que o Wazuh Dashboard consegue receber e apresentar esses eventos de segurança.

#### O agente está enviando os logs corretamente.
