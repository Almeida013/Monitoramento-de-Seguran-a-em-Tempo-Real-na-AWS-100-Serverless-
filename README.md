# 🛡️ Monitoramento de Segurança em Tempo Real na AWS (100% Serverless)

Este projeto implementa uma arquitetura serverless para detecção e alerta de eventos críticos em ambientes AWS.  
Utiliza os serviços **CloudTrail**, **S3**, **Lambda** e **SNS**, com roteamento inteligente de notificações baseado em severidade.

---

## 📌 Objetivo

Criar um sistema de **monitoramento automatizado** que:

- Detecta atividades sensíveis (ex: criação de usuários IAM, alteração de políticas, acessos a buckets);
- Processa eventos em tempo real;
- Envia alertas para canais adequados (e-mail, Slack, SIEM, SOAR, etc);
- Seja **escalável, serverless e seguro** por padrão.

---

## ⚙️ Arquitetura da Solução
![Cópia do Projeto security](https://github.com/user-attachments/assets/6e89a604-0fe6-4b9d-a653-211b9caba296)



---

## 🔁 Fluxo de Eventos

1. **Usuário realiza uma ação na AWS**
2. **CloudTrail** registra o evento
3. Evento é armazenado em um **bucket S3**
4. Um **trigger Lambda** é ativado a cada novo log
5. A **Lambda** processa o log e identifica eventos relevantes
6. A Lambda publica alertas no **SNS**
7. O **SNS** envia notificações para diferentes canais, conforme a severidade:

| Severidade | Destino                          |
|------------|----------------------------------|
| Alta       | Webhook (Slack, SOAR, PagerDuty) |
| Média      | E-mail da equipe de segurança    |
| Informativa| SIEM / CloudWatch Logs / S3      |

---

## 🧰 Serviços Utilizados

- **AWS CloudTrail** – Captura todas as ações na conta
- **Amazon S3** – Armazena os logs de eventos
- **AWS Lambda** – Processa os logs e identifica ações críticas
- **Amazon SNS** – Gerencia e distribui alertas por severidade
- *(Opcional)*: Integrações com Slack, Discord, SIEMs ou sistemas SOAR

---

## 🔐 Boas Práticas Implementadas

- Princípio de **Menor Privilégio** (IAM Role da Lambda com permissões mínimas)
- Segregação por **nível de severidade**
- Escalabilidade horizontal via Lambda + SNS
- Armazenamento seguro dos logs
- Notificações multicanal
- Design serverless (sem gerenciamento de servidores)

--- 
