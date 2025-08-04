# 🛡️ Monitoramento de Segurança em Tempo Real na AWS (100% Serverless)

Arquitetura serverless para monitoramento contínuo de eventos críticos em ambientes AWS, utilizando **CloudTrail + S3 + Lambda + SNS**.

> Projetado com foco em boas práticas de segurança, automação e escalabilidade.  
> Futuramente implementado via **Terraform** com deploy completo e modularizado.

---

## 🧠 Visão Geral

Essa solução tem como objetivo identificar **ações críticas** dentro da sua conta AWS e emitir **alertas em tempo real**, categorizados por severidade.


---

## 🏗️ Arquitetura

![Cópia do Projeto security](https://github.com/user-attachments/assets/7b0ea8d7-f599-4cde-88f8-bfbdd31823b3)

### 🔁 Fluxo:

1. Usuário ou sistema realiza uma ação sensível na AWS  
2. O CloudTrail registra a chamada de API  
3. Logs são armazenados automaticamente em um bucket S3  
4. Novo log aciona uma função Lambda  
5. A Lambda processa e analisa o evento  
6. Um alerta é enviado via Amazon SNS conforme a severidade  
7. A equipe de segurança é notificada por e-mail, webhook ou outra integração

---

## 🧩 Componentes Usados

| Serviço AWS      | Função                                                    |
|------------------|------------------------------------------------------------|
| **CloudTrail**   | Captura eventos da conta (criação de IAM, S3 access, etc)  |
| **S3 Bucket**    | Armazena os logs gerados pelo CloudTrail                   |
| **Lambda**       | Processa logs, filtra eventos relevantes e define severidade |
| **SNS Topic**    | Roteia notificações para canais conforme a criticidade     |

---

## 🎯 Severidade e Ações

| Severidade | Cor  | Ação                                                         |
|------------|------|--------------------------------------------------------------|
| Alta       | 🔴   | Envio para webhook (Slack/Discord/SOAR/automatização)        |
| Média      | 🟠   | E-mail para equipe de segurança                               |
| Informativa| 🔵   | Armazenamento e análise posterior via SIEM / Athena          |

---

##🛠️ Próximas Evoluções
 Deploy completo via Terraform

 Lógica de resposta automática via Step Functions

 Integração com AWS Security Hub

 Dashboards com Athena + QuickSight

 Suporte a múltiplas contas com AWS Organizations

 Análise comportamental e alertas inteligentes

## 🙋‍♂️ Autor
Desenvolvido por Kaike Almeida
🎯 Focado em Cloud Security, DevSecOps e arquitetura AWS.

## 📄 Licença
MIT License. Use, adapte e contribua.

## 🤝 Contribuições
Pull requests são bem-vindos. Se quiser dar feedback ou colaborar com ideias, fique à vontade para abrir uma issue.
