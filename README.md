# Simulação de Ataque de Força Bruta com Medusa e Kali Linux

Projeto prático desenvolvido durante o **Bootcamp Santander Cibersegurança - Do Zero à Prática**, oferecido pela [DIO](https://dio.me) em parceria com o **Santander**.

---

## 🎯 Objetivo
Demonstrar na prática o ciclo de reconhecimento de rede e execução de ataques de força bruta (*Brute Force* e *Password Spraying*) em serviços de rede, utilizando um ambiente controlado e seguro, além de propor medidas sólidas de defesa e mitigação.

---

## 🛠️ Ambiente do Laboratório
Para garantir total isolamento e segurança, todo o tráfego foi contido em uma rede local virtualizada:
- **Topologia de Rede:** Modo **Host-Only** (Rede Exclusiva do Hospedeiro no VirtualBox), impedindo que qualquer pacote ou teste vazasse para a rede externa ou para a internet.
- **Máquina Atacante:** Kali Linux
- **Máquina Alvo:** Metasploitable 2 (IP: `192.168.56.101`)
- **Ferramentas Utilizadas:** `Nmap` (Reconhecimento e varredura) e `Medusa` (Ataque de força bruta modular).

---

## 🚀 Passo a Passo da Execução

### 1. Reconhecimento e Varredura de Rede (Nmap)
Inicialmente, foi executado o reconhecimento do alvo para mapeamento de portas e identificação de versões de serviços em execução:

```bash
# Varredura completa de portas e detecção de versões de serviço no alvo
nmap -sS -sV -O 192.168.56.101
```

* **Serviços identificados com potencial para testes de autenticação:**
  * Porta 21/TCP: FTP (vsftpd)
  * Portas 139/445/TCP: SMB / Samba (Compartilhamento de arquivos Windows/Linux)

---

### 2. Preparação dos Arquivos de Teste (Wordlists)
Foram preparadas listas controladas de usuários e senhas para os testes:
- `users.txt`: lista contendo usuários padrão e alvos (ex: `admin`, `user`, `msfadmin`).
- `passwords.txt`: dicionário de senhas fracas comuns.

---

### 3. Ataque de Força Bruta com Medusa

#### Cenário A: Força Bruta no Serviço FTP
Execução de teste paralelo de credenciais contra o servidor FTP:
```bash
medusa -h 192.168.56.101 -u msfadmin -P passwords.txt -M ftp
```

#### Cenário B: Password Spraying / Força Bruta no Serviço SMB
Teste de autenticação sobre o serviço SMB (compartilhamento de arquivos):
```bash
medusa -h 192.168.56.101 -U users.txt -P passwords.txt -M smbnt
```

---

### 4. Resultados Obtidos
- **Validação de Sucesso:** Identificação de credenciais válidas em tempo reduzido devido ao uso de senhas fracas no ambiente alvo.
- **Evidência:** Conexão autenticada aos serviços com as credenciais reveladas pelo Medusa, confirmando a vulnerabilidade de senhas previsíveis.

---

## 🛡️ Medidas de Mitigação e Defesa
Para evitar invasões por força bruta e enumeração de SMB/FTP em ambientes de produção, recomenda-se:

1. **Políticas Rígidas de Senhas:** Exigência de senhas longas, complexas e sem termos de dicionário comum.
2. **Bloqueio de Contas e Rate Limiting:** Implementação de bloqueio de conta após determinado número de tentativas consecutivas incorretas (ex: via políticas de grupo no Active Directory para SMB ou ferramentas como `Fail2ban` em Linux).
3. **Desativação de Protocolos Inseguros:** Desabilitar o SMBv1 e descontinuar serviços legados em texto claro (como FTP puro), priorizando protocolos modernos e cifrados (SFTP, SSH com autenticação por chave criptográfica).
4. **Segmentação e Controle de Acesso (Firewall):** Restringir o acesso a portas administrativas (SMB 445, FTP 21) apenas a IPs e redes autorizadas.
5. **Autenticação Multifator (MFA):** Requisito indispensável para acessos externos e serviços críticos.

---

## 🤖 Mentoria e Créditos
Este projeto contou com o apoio e mentoria do **DIO Agent** (agente de inteligência artificial da DIO) para estruturação do plano de estudos, boas práticas de documentação técnica e direcionamento para a entrega do desafio.
