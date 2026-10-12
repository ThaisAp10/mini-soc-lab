# Caso 3 — Detecção de Brute Force SSH

## 1. Objetivo

O objetivo deste teste foi simular múltiplas tentativas de autenticação SSH malsucedidas contra a máquina `ubuntu-target` e validar se o Wazuh conseguiria detectar e registrar essa atividade.

## 2. Verificação do endereço IP

Na máquina alvo (`ubuntu-target`), executei o comando:

```bash
ip a
```

O comando permitiu identificar os endereços IP das interfaces de rede. Para o teste, foi utilizado o endereço `192.168.56.102`.

## 3. Configuração do serviço SSH

Inicialmente, tentei acessar a máquina alvo a partir do `soc-server` utilizando SSH, mas a conexão não foi estabelecida porque o serviço SSH não estava instalado na máquina alvo.

### 3.1. Verificação do serviço

Na máquina `ubuntu-target`, executei:

```bash
sudo systemctl status ssh
```

O retorno inicial foi:

```text
Unit ssh.service could not be found
```

Isso indicou que o serviço SSH não estava instalado.

### 3.2. Instalação e ativação do SSH

Na máquina `ubuntu-target`, executei:

```bash
sudo apt update
sudo apt install openssh-server -y
sudo systemctl enable --now ssh
```

Depois, verifiquei o estado do serviço:

```bash
sudo systemctl status ssh
```

O serviço foi iniciado e configurado para iniciar automaticamente com o sistema.

### 3.3. Verificação da autenticação por senha

Para verificar a configuração de autenticação, executei:

```bash
sudo grep -i 'PasswordAuthentication' /etc/ssh/sshd_config
```

Quando necessário, editei o arquivo:

```bash
sudo nano /etc/ssh/sshd_config
```

A configuração foi ajustada para permitir autenticação por senha, utilizando:

```text
PasswordAuthentication yes
```

Após salvar o arquivo, reiniciei o serviço:

```bash
sudo systemctl restart ssh
```

> A autenticação por senha foi habilitada para viabilizar o teste controlado. Em ambientes reais, prefira métodos mais seguros, como autenticação por chave SSH, e evite expor o serviço desnecessariamente.

## 4. Teste de conexão SSH

No `soc-server`, executei o comando abaixo, substituindo o usuário e o endereço IP conforme a configuração do laboratório:

```bash
ssh thais@192.168.56.102
```

Na primeira conexão, foi solicitada a confirmação da chave do host. Após aceitar a chave, foi possível prosseguir com a autenticação.

Em seguida, realizei várias tentativas de login utilizando senhas incorretas, simulando manualmente um cenário de tentativas repetidas de acesso.

## 5. Detecção no Wazuh Dashboard

Após gerar as tentativas malsucedidas, acessei o Wazuh Dashboard e naveguei até:

**Security Events → Events**

Realizei pesquisas relacionadas a:

- `sshd`
- `authentication`
- `Failed password`

Foram identificados eventos relacionados a falhas de autenticação SSH, incluindo:

- `sshd: authentication failed.`
- `User login failed`, quando apresentado nos eventos.

**[INSERIR PRINT: eventos de falha de autenticação SSH no Dashboard]**

Ao abrir os detalhes do evento, foi possível analisar informações como o host afetado, o usuário alvo, o endereço IP de origem, o horário e a regra de detecção.

**[INSERIR PRINT: detalhes do evento no Wazuh Dashboard]**

### Informações da detecção

- **Host afetado:** `ubuntu-target`
- **IP do alvo:** `192.168.56.102`
- **IP de origem:** `192.168.56.101`
- **Usuário alvo:** `thais`
- **Rule ID:** `5760`
- **
