# Documentação do Projeto - Sistema de Monitoramento de Segurança AWS
⚠️ Ambiente: Sistema desenvolvido e testado em AWS Sandbox (recursos temporários)

--

## Conexão SSH e Acesso ao Servidor

<img width="1097" height="609" alt="image" src="https://github.com/user-attachments/assets/c8490708-4273-415c-96d4-18b6c7baad24" />

*Descrição:* Conexão SSH estabelecida com sucesso ao servidor AWS EC2 (IP 54.212.89.67) via PowerShell, mostrando o banner do Amazon Linux 2023. Esta conexão permite acesso remoto seguro para administração, monitoramento e execução de testes no ambiente de produção.
*Parte do Projeto:* Infraestrutura e acesso remoto - módulo de administração e manutenção do sistema.

--

## Instalação de Ferramenta de Teste de Stress

<img width="1910" height="970" alt="image" src="https://github.com/user-attachments/assets/c1f1303f-1e97-452b-b9f1-ace0a9152293" />
*Descrição:* Terminal mostrando a instalação da ferramenta 'stress' via yum no Amazon Linux para simular alta carga de CPU. Esta etapa faz parte dos testes controlados para validar a eficácia do sistema de detecção de anomalias, permitindo gerar carga artificial que dispara os alertas configurados.
*Parte do Projeto:* Módulo de testes e validação - simulação de cenários de ataque para verificação do sistema.

--

 # Análise Detalhada do Alerta no CloudWatch

<img width="1910" height="970" alt="image" src="https://github.com/user-attachments/assets/d9d0cce7-cc07-4986-b404-e4135361e281" />
*Descrição:* Interface do AWS CloudWatch mostrando os detalhes completos do alarme "CRITICAL-High-CPU-Possible-Cryptomining", incluindo o gráfico de utilização de CPU ao longo do tempo e o histórico do alarme. A visualização permite análise granular do comportamento suspeito detectado pelo sistema de monitoramento.
*Parte do Projeto:* Módulo de análise forense - ferramenta de investigação de incidentes de segurança.

--

# Alerta Crítico de Possível Cryptomining
<img width="901" height="790" alt="image" src="https://github.com/user-attachments/assets/12b9ef42-00a0-4572-bbaf-ca55b3aefcbf" />

*Descrição:* Notificação por email do AWS CloudWatch alertando sobre atividade suspeita de alto uso de CPU que pode indicar cryptomining malicioso. O alerta foi disparado quando a utilização de CPU ultrapassou o threshold de 80% configurado, atingindo valores próximos a 99% em duas medições consecutivas.
*Parte do Projeto:* Sistema de alertas e notificações - módulo de detecção de anomalias e segurança.

# Dashboard de Monitoramento de Segurança - Visão Geral

<img width="1914" height="771" alt="image" src="https://github.com/user-attachments/assets/cab89cac-9d2c-4377-a5c2-ed7d76f40429" />

*Descrição:* Dashboard principal do sistema de monitoramento exibindo métricas críticas de CPU e tráfego de rede em tempo real. Esta interface mostra o painel de controle centralizado onde são visualizados os indicadores de performance do sistema, incluindo utilização de CPU chegando a 100% e picos de tráfego de rede de saída.
*Parte do Projeto:* Interface de monitoramento frontend - módulo de visualização de métricas em tempo real.
