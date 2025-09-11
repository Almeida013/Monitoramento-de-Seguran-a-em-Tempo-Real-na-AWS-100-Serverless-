#  Tutorial Completo: Sistema de Detecção de Ameaças AWS 

## 👋 OPA! Vamos construir algo incrível juntos

Imagine descobrir que alguém está minerando Bitcoin na sua instância AWS **antes** da conta de R$ 5.000 chegar no fim do mês. Esse tutorial te ensina exatamente isso.

Esse tutorial eu fiz para você que quer praticar e também pra mim quando quiser revisar o projeto kkk, então vai estar tudo passo a passo, explicando o **porquê** de cada decisão. No final, você terá um sistema que monitora 4 ameaças críticas em tempo real.

---

## 🎯 O que vamos construir

**Sistema que detecta automaticamente:**
- 🚨 **Cryptomining** → CPU alta suspeita  
- 🚨 **Vazamento de dados** → Tráfego de rede anormal  
- 🚨 **Instância comprometida** → Falhas de sistema  
- 🚨 **Custos descontrolados** → Limite de billing ultrapassado  

**Tempo total:** ~45 minutos  
**Custo:** Praticamente $0 (Free Tier)  
**Dificuldade:** Iniciante/Intermediário  

---

## ⚡ Pré-requisitos (MUITO IMPORTANTE!)

### ✅ **1. Conta AWS com as permissões certas**
Você precisa ter acesso a:
- **EC2** (criar/gerenciar instâncias)
- **CloudWatch** (criar alarmes e dashboards)  
- **SNS** (enviar emails)
- **Billing** (ver custos)

💡 **Dica:** Se você é admin da conta, já tem tudo. Se não, peça pro seu gestor liberar essas permissões.

### ✅ **2. Região AWS correta - CRÍTICO!**

**⚠️ ATENÇÃO:** Para billing alerts funcionarem, você **PRECISA** estar em:
- **Região:** `US East (N. Virginia) us-east-1`

🤔 **Por quê?** Os dados da métrica de cobrança são armazenados nesta Região e representam cobranças em todo o mundo. - documentação oficial da AWS.

**Como trocar a região:**
1. No canto superior direito do console AWS
2. Clique no dropdown da região (ex: "São Paulo")
3. Selecione **"US East (N. Virginia)"**

### ✅ **3. Email válido**
Você vai receber os alertas por email, então use um email que você realmente monitora.

---

# 🚀 ETAPA 1: Criando a Instância de Teste

## Por que criar uma instância?
Simples: precisamos de algo para monitorar! Vamos criar uma instância básica que simula um servidor de produção.

### 1.1 Lançar a instância

1. **AWS Console** → **EC2** → **Launch Instance**

2. **Configure os detalhes:**
   ```
   Name: security-monitoring-lab
   Application and OS: Amazon Linux 2023
   Architecture: 64-bit (x86)
   Instance type: t3.micro (✅ Free tier eligible)
   ```

3. **Key pair:**
   - Se você já tem: selecione uma existente
   - Se não tem: **"Create new key pair"**
     - Name: `security-lab-key`
     - Type: RSA
     - Format: `.pem`
     - **Download** e guarde em local seguro!

### 1.2 Security Group (CUIDADO AQUI!)

❌ **Não faça isso:**
```
Allow HTTP from 0.0.0.0/0 (toda internet)
Allow HTTPS from 0.0.0.0/0 (toda internet)
```

✅ **Faça isso (muito mais seguro):**
1. **Edit Security Group**
2. **SSH Rule:**
   - Type: SSH (22)
   - Source: **My IP** (sua IP atual)
3. **HTTP Rule (só se necessário):**
   - Type: HTTP (80)  
   - Source: **My IP** (não 0.0.0.0/0!)

🤔 **Por quê?** Deixar HTTP aberto para toda internet é como deixar a porta de casa aberta. Nossa instância é só para testes, então só você precisa acessar.

### 1.3 Storage e Launch
- **Storage:** 8 GB (padrão está bom)
- **Advanced details:** deixe tudo padrão
- **Launch instance** 🚀

**Aguarde 2-3 minutos** para a instância ficar "Running".

---

# 📧 ETAPA 2: Sistema de Notificações (SNS)

## Por que SNS?
CloudWatch não envia email sozinho. O SNS é o "carteiro" que vai entregar os alertas no seu email.

### 2.1 Criando o tópico

1. **AWS Console** → **SNS** (Simple Notification Service)
2. **Topics** → **Create topic**

3. **Configuração:**
   ```
   Type: Standard (não FIFO)
   Name: security-alerts
   Display name: 🛡️ AWS Security Monitoring
   ```

4. **Create topic**

### 2.2 Inscrevendo seu email

1. **Topics** → **security-alerts** → **Create subscription**

2. **Protocol:** Email  
   **Endpoint:** seu-email@exemplo.com

3. **Create subscription**

4. **IMPORTANTE:** Vá no seu email AGORA e clique em **"Confirm subscription"**

5. **Volte ao console** e veja se Status = **"Confirmed"** ✅

💡 **Dica:** Se não chegou o email, verifique spam/lixo eletrônico.

---

# 🚨 ETAPA 3: Alarme 1 - Detecção de Cryptomining

## Por que monitorar CPU?
Cryptomining consome 100% da CPU por horas. Um servidor normal raramente passa de 50% por muito tempo.

### 3.1 Criando o alarme

1. **AWS Console** → **CloudWatch** → **Alarms** → **Create alarm**

2. **Select metric:**
   - **Browse** → **EC2** → **Per-Instance Metrics**
   - Encontre sua instância: `security-monitoring-lab`
   - ✅ **CPUUtilization**
   - **Select metric**

### 3.2 Definindo as condições

```
Statistic: Average (média dos valores)
Period: 5 minutes (verifica a cada 5 min)

Threshold type: Static (valor fixo)
Whenever CPUUtilization is: Greater than 80

Additional configuration:
Datapoints to alarm: 2 out of 2
Missing data treatment: Treat as not breaching
```

🤔 **Por que 2 out of 2?** Evita falsos positivos. Se a CPU subir por 1 minuto só, pode ser normal. Se subir por 2 verificações seguidas (10 min), aí é suspeito!

### 3.3 Ações automáticas

**Notification:**
- **Alarm state trigger:** In alarm
- **Send notification to:** security-alerts ✅

**EC2 action (OPCIONAL):**
- ✅ **Stop this instance**

⚠️ **Atenção:** Essa ação vai **parar sua instância** automaticamente quando CPU ficar alta. Em produção, cuidado com isso!

### 3.4 Naming
```
Alarm name: 🚨-CRITICAL-High-CPU-Cryptomining-Detection
Alarm description: 
Detecta uso excessivo de CPU que pode indicar:
- Cryptomining não autorizado
- Ataques de DDoS interno
- Processos maliciosos
AÇÃO: Investigar imediatamente e considerar isolamento da instância
```

**Create alarm** 🎉

---

# 🌐 ETAPA 4: Alarme 2 - Detecção de Vazamento de Dados

## Por que monitorar tráfego de rede?
Se alguém está enviando seus dados para fora (exfiltração), o tráfego de saída vai disparar.

### 4.1 Criando o alarme de rede

1. **CloudWatch** → **Alarms** → **Create alarm**

2. **Select metric:**
   - **EC2** → **Per-Instance Metrics**
   - Sua instância → ✅ **NetworkOut**
   - **Select metric**

### 4.2 Condições críticas

```
Statistic: Sum (soma total do período)
Period: 5 minutes

Whenever NetworkOut is: Greater than 500000000
(500MB em 5 minutos = tráfego suspeito!)

Datapoints to alarm: 1 out of 1
(vazamento precisa ser detectado rápido!)
```

💡 **Por que 500MB?** Um servidor normal raramente envia mais que isso. Se enviar, pode estar vazando arquivos grandes.

### 4.3 Configuração final

**Actions:**
- **SNS topic:** security-alerts

**Name:**
```
🚨-CRITICAL-Network-Exfiltration-Detection
```

**Description:**
```
Detecta tráfego de rede anormalmente alto que pode indicar:
- Exfiltração de dados confidenciais
- Ataques de data breach
- Upload não autorizado de arquivos
AÇÃO: Verificar processos de rede e considerar isolamento imediato
```

---

# 💔 ETAPA 5: Alarme 3 - Instância Comprometida

## Por que Status Check?
Quando uma instância é comprometida, ela pode corromper o sistema operacional. O AWS detecta isso automaticamente.

### 5.1 Alarm de status

1. **Create alarm** → **Select metric**
   - **EC2** → **Per-Instance Metrics**
   - Sua instância → ✅ **StatusCheckFailed**

### 5.2 Condições de falha

```
Statistic: Maximum
Period: 1 minute (checagem rápida!)

Whenever StatusCheckFailed is: Greater than or equal to 1

Datapoints: 1 out of 1
(falha crítica = alerta imediato)
```

### 5.3 Ação de recuperação

**Actions:**
- **Notification:** security-alerts
- **EC2 action:** ✅ **Reboot this instance**

🤔 **Por quê reboot?** Muitas vezes um reboot resolve problemas de sistema corrompido.

**Name:** `🚨-CRITICAL-Instance-Status-Failed`

---

# 💰 ETAPA 6: Alarme 4 - Controle de Custos

## Por que billing alert?
Ataques podem gerar custos enormes (instâncias extras, tráfego, etc.). Melhor descobrir em $10 que em $1000.

### 6.1 Habilitando billing alerts

**IMPORTANTE:** Certifique-se que está em **us-east-1**!

1. **AWS Console** → **Billing and Cost Management**
2. **Billing preferences** (menu lateral)
3. ✅ **Receive Billing Alerts**
4. **Save preferences**

### 6.2 Criando billing alarm

1. **CloudWatch** → **Create alarm** → **Select metric**
   - **Browse** → **Billing** → **Total Estimated Charge**
   - **Currency:** USD
   - **Select metric**

### 6.3 Limite de custo

```
Threshold type: Static
Whenever EstimatedCharges is: Greater than 10
(ou o valor que faz sentido pra você)

Period: 6 hours
Datapoints: 1 out of 1
```

💡 **Dica:** Coloque um valor que seja ~20% maior que seu gasto mensal normal.

**Name:** `💸-CRITICAL-Monthly-Cost-Alert`

---

# 🧪 ETAPA 7: HORA DO TESTE! (A Parte Divertida)

Agora vamos simular ataques reais para ver se funciona. É como um "fire drill" da segurança.

### 7.1 Conectando na instância

```bash
# No seu terminal local
ssh -i sua-key.pem ec2-user@IP-DA-SUA-INSTANCIA
```

💡 **Como pegar o IP?** EC2 Console → Instances → sua instância → copie o "Public IPv4 address"

### 7.2 Instalando ferramenta de stress

**✅ COMANDO VALIDADO para Amazon Linux 2023:**

Amazon Linux 2023 usa DNF como gerenciador de pacotes por padrão

```bash
# Comando correto para AL2023 (testado e funcionando!)
sudo dnf install stress -y

# Se preferir uma versão mais avançada:
sudo dnf install stress-ng -y

# Verificar se instalou corretamente:
stress --version
```

**⚠️ Se der erro, tente:**
```bash
# Fallback para sistemas específicos
sudo dnf update -y
sudo dnf install epel-release -y
sudo dnf install stress -y
```

### 7.3 Simulando Cryptomining 🎭

```bash
# Simula mineração de Bitcoin (usa 100% CPU)
echo "🚨 SIMULANDO CRYPTOMINING - CPU VAI A 100%"
stress --cpu 2 --timeout 600s

# Você vai ver algo assim:
# stress: info: [1234] dispatching hogs: 2 cpu, 0 io, 0 vm, 0 hdd
```

**O que esperar:**
- ⏱️ **2-5 minutos:** CPU sobe para 100%
- ⏱️ **8-10 minutos:** Alarme dispara!
- 📧 **Email chegará:** "🚨-CRITICAL-High-CPU-Cryptomining-Detection"
- 🛑 **Instância pode parar** (se configurou ação automática)

### 7.4 Verificando no CloudWatch

1. **CloudWatch** → **Alarms**
2. Seu alarme deve estar **"In alarm"** (vermelho) 🔴
3. **History tab** → mostra exatamente quando disparou

### 7.5 Simulando vazamento de dados

```bash
# Para o stress anterior
# Ctrl + C

# Simular envio de dados (testado e funcionando!)
echo "🚨 SIMULANDO DATA EXFILTRATION"

# Comando validado - gera tráfego real:
for i in {1..10}; do
  dd if=/dev/zero bs=1M count=50 | curl -X POST \
    --data-binary @- \
    --max-time 60 \
    https://httpbin.org/post > /dev/null 2>&1 &
done

# Esse comando vai gerar ~500MB de tráfego de saída
# O alarme deve disparar em 5-8 minutos
```

### 7.6 Forçando falha de status

```bash
# ⚠️ CUIDADO - isso vai crashear a instância propositalmente
echo "🚨 SIMULANDO INSTANCE COMPROMISE"

# Comando para simular crash de sistema:
sudo bash -c 'echo 1 > /proc/sys/kernel/sysrq'
sudo bash -c 'echo c > /proc/sysrq-trigger'

# A instância vai reiniciar automaticamente
# Status check alarm deve disparar
```

---

# 📊 ETAPA 8: Dashboard de Comando

Vamos criar um painel visual para monitorar tudo em tempo real, como um "centro de comando".

## 8.1 Criando dashboard

1. **CloudWatch** → **Dashboards** → **Create dashboard**
2. **Name:** `🛡️ Security Monitoring Command Center`

## 8.2 Adicionando widgets

**Widget 1 - CPU Real-Time:**
- **Add widget** → **Line** → **Metrics**
- **EC2** → **Per-Instance Metrics** → **CPUUtilization**
- **Widget title:** `🔥 CPU Utilization (Real-Time)`

**Widget 2 - Network Traffic:**
- **Add widget** → **Line**
- **Metrics:** NetworkIn + NetworkOut
- **Title:** `🌐 Network Traffic Monitor`

**Widget 3 - Alarm Status:**
- **Add widget** → **Number**
- **CloudWatch** → **Metrics** → selecione seus alarms
- **Title:** `🚨 Active Alarms`

**Widget 4 - Instance Status:**
- **Add widget** → **Number**  
- **StatusCheckFailed**
- **Title:** `💚 Instance Health`

## 8.3 Layout final

Organize os widgets numa grid 2x2 para ficar visual e profissional.

**Save dashboard** 🎨

---

# ✅ ETAPA 9: Validação Final do Sistema

## 9.1 Checklist de funcionamento

```
□ Instância EC2 rodando normalmente
□ 4 alarmes criados e em status "OK" (verde)
□ SNS subscription confirmada no email
□ Dashboard mostrando métricas em tempo real
□ Teste de stress executado com sucesso
□ Email de alerta recebido
```

## 9.2 Teste de recuperação

1. **Pare o stress test:** `Ctrl + C`
2. **Aguarde 10-15 minutos**
3. **Verifique:** Alarme volta para "OK" (verde)
4. **Email de recovery** deve chegar

## 9.3 Troubleshooting comum

### ❌ "Email não chegou"
```
✅ Confirme subscription no SNS
✅ Verifique spam/lixo eletrônico  
✅ Teste manual: SNS → Topics → security-alerts → Publish message
```

### ❌ "Alarm não disparou"  
```
✅ Verifique se stress realmente subiu CPU (CloudWatch metrics)
✅ Confirme threshold (deve estar em 80%)
✅ Aguarde tempo suficiente (datapoints 2 out of 2)
```

### ❌ "Billing alarm não funciona"
```
✅ Confirme que está em us-east-1
✅ Billing alerts habilitadas?
✅ Aguarde - billing pode demorar até 24h para atualizar
```

### ❌ "Comando stress não funciona"
```
✅ Use: sudo dnf install stress -y (não yum!)
✅ Se ainda der erro: sudo dnf install epel-release -y
✅ Teste: stress --version
```

---

# 🎉 PARABÉNS! Você Construiu um Sistema Real de Segurança

## O que você conseguiu:

1. ✅ **Sistema de detecção** que monitora 4 ameaças críticas  
2. ✅ **Alertas automáticos** por email em tempo real  
3. ✅ **Dashboard visual** para monitoramento contínuo  
4. ✅ **Ações automáticas** (stop/reboot) para proteção  
5. ✅ **Testes validados** com simulações reais  

## Próximos níveis (se quiser evoluir):

### 🚀 **Nível Intermediate:**
- Integrar com **Slack/Teams** via webhooks
- Usar **Lambda** para ações customizadas
- **CloudFormation** para automação completa

### 🚀 **Nível Advanced:**  
- **AWS Security Hub** centralizado
- **GuardDuty** para detecção de anomalias com ML
- **Config Rules** para compliance automático
- **VPC Flow Logs** para análise de tráfego

---

# ⚠️ IMPORTANTE: Limpeza (Para não pagar nada)

Quando terminar os testes:

1. **EC2** → **Instances** → selecione sua instância → **Terminate**
2. **CloudWatch** → **Alarms** → delete os 4 alarms criados
3. **SNS** → **Topics** → delete security-alerts
4. **CloudWatch** → **Dashboards** → delete o dashboard

**Custo total do lab:** ~$0.50 (se deixar rodando por algumas horas)

---

# 🤔 Reflexão Final

A diferença entre uma empresa que detecta ataques em **minutos** vs **meses** pode ser a sobrevivência do negócio.

**Compartilhe** essa experiência, coloque no LinkedIn, mostre pro seu chefe. Você acabou de provar que entende tanto a parte técnica quanto o impacto de negócio da segurança.


