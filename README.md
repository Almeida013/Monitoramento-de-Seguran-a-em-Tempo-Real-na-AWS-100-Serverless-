# 🛡️ Projeto AWS | Detecção e Alertas em Tempo Real

 ![AWS EC2](https://img.shields.io/badge/AWS-EC2-orange?logo=amazon-aws&logoColor=white)
![CloudWatch](https://img.shields.io/badge/AWS-CloudWatch-FF4F8B?logo=amazon-aws&logoColor=white)
![SNS](https://img.shields.io/badge/AWS-SNS-FF9900?logo=amazon-aws&logoColor=white)
![Email Alerts](https://img.shields.io/badge/Email-Alerts-blue?logo=gmail&logoColor=white)
![Amazon Linux](https://img.shields.io/badge/Amazon-Linux%202023-232F3E?logo=linux&logoColor=white)
![Terraform](https://img.shields.io/badge/IaC-Terraform-844FBA?logo=terraform&logoColor=white)


> E se alguém começasse a minerar criptomoeda escondido na sua instância EC2 agora?  
> Você só descobriria quando a conta da AWS chegasse no fim do mês?

Foi com essa provocação que nasceu este projeto.  
Muitas empresas **descobrem tarde demais** abusos como cryptomining, exfiltração de dados ou até custos descontrolados.  
O objetivo aqui foi criar uma **arquitetura 100% serverless e funcional** que monitora atividades suspeitas na AWS **em tempo real** e dispara alertas críticos antes que o prejuízo aconteça.

---

## 🎯 Objetivo
Detectar automaticamente comportamentos suspeitos em **instâncias EC2** e outros recursos da AWS, disparando **alertas críticos via email (SNS)** em tempo real.  
Casos de uso principais:
- 🚩 **Cryptomining** → uso excessivo de CPU  
- 🚩 **Data Exfiltration** → tráfego de saída anormal  
- 🚩 **Comprometimento de instância** → falhas no status check  
- 🚩 **Custo inesperado** → alertas de billing

---

## 🏗️ Arquitetura
Fluxo do sistema:

EC2 Metrics → CloudWatch Alarms → SNS → Email


### Por que funciona?
✅ CloudWatch tem permissão **nativa** para disparar SNS  
✅ Nenhuma dependência de `iam:PassRole`  
✅ Métricas de EC2 são coletadas **automaticamente**  
✅ Foco em **casos reais de segurança**  

📌 A arquitetura completa em **diagramas** e **prints de execução real** está em [`/docs`](./docs).

---

## ⚙️ Implementação
Cada alarme cobre um cenário crítico:

1. **High CPU Utilization** → Detecta possível cryptomining  
2. **Network Out Alta** → Detecta exfiltração de dados  
3. **Status Check Failed** → Indica comprometimento da instância  
4. **Billing Alert** → Protege contra uso indevido/custos inesperados  

👉 Todos configurados no **CloudWatch Alarms** e integrados com **SNS** para envio de email.

---

## 📊 Dashboard
Para acompanhamento contínuo, foi criado um **Dashboard customizado no CloudWatch**, exibindo:
- Uso de CPU em tempo real  
- Tráfego de rede (Network Out)  
- Status dos alarms (OK / In Alarm)  
- Indicadores visuais de incidentes  

Veja screenshots em [`/docs/screenshots`](./docs/screenshots).

---

## 🧪 Testes e Demonstração
O projeto foi **validado em laboratório** com simulações reais:

- 💻 **Cryptomining** → usando `stress --cpu` na instância  
- 🌐 **Data Exfiltration** → enviando 1GB em 5 minutos  
- ⚠️ **Falha de instância** → forçando `StatusCheckFailed`  
- 💸 **Billing** → ultrapassando limite definido  

📬 Todos os cenários resultaram em **alertas por email em tempo real**.

---

## 📂 Estrutura do Repositório
security-monitoring-aws/
├── cloudwatch/ # Alarms e dashboards
├── docs/ # Arquitetura e screenshots
├── scripts/ # Scripts de teste e automação
├── infra/ # Futuro: Terraform/IaC
├── LICENSE
└── README.md

---

## 🚀 Roadmap
- [ ] Automação da arquitetura com **Terraform**    
- [ ] Export de logs para **SIEM (Athena / Security Hub)**   

---

## ⚠️ Limitações do Lab vs Produção

Este projeto foi desenvolvido para **aprendizado e demonstração prática** de monitoramento de métricas na AWS.  
Apesar de funcionar de verdade, **não deve ser usado em produção sem ajustes**. Eis os principais pontos:

1. **Ação Automática (Stop Instance)**  
   - Bom para demo, mas em produção pode derrubar sistemas críticos.  
   - Correto: isolar/quarentenar instância ou aplicar política automatizada via SOAR.  

2. **Alertas apenas por Email (SNS)**  
   - Email pode ser lento ou ignorado.  
   - Correto: integrar com Slack/Teams, PagerDuty ou SIEM.  

3. **Thresholds fixos nos alarmes**  
   - Pode gerar falsos positivos/negativos.  
   - Correto: usar Anomaly Detection ou métricas adaptativas.  

4. **Exposição de portas no Security Group**  
   - Necessário para teste, mas inseguro em produção.  
   - Correto: restringir IPs ou usar ALB + WAF.  

5. **Logs não centralizados**  
   - Hoje ficam apenas no CloudWatch.  
   - Correto: armazenar em S3 e integrar com SIEM/GuardDuty/Security Hub.  

👉 **Resumo:** este projeto é um **lab didático**, ótimo para aprendizado, mas deve ser adaptado para uso corporativo.

---

## 🙋‍♂️ Autor
Desenvolvido por **Kaike Almeida**  
🎯 Cloud Security | DevSecOps | Arquitetura AWS

👉 Conecte-se comigo no [LinkedIn]([https://www.linkedin.com/](https://www.linkedin.com/in/kaikealmeida))  

---

## 📄 Licença
MIT License — use, adapte e contribua livremente.  

---

## 🤝 Contribuições
Pull requests e ideias são muito bem-vindas.  
Abra uma issue, vamos construir juntos.  

---

## 🔗 Links Rápidos
- 📚 [Arquitetura & Prints](./docs)  
- 📜 [Alarms JSON](./cloudwatch/alarms)  
- ⚙️ [Scripts de Teste](./scripts)  
