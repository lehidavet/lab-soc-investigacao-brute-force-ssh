# 🔍 Investigação de tentativas de autenticação SSH falhas | SSH failed authentication attempt investigation (Lab SOC/Blue Team – Kali Linux)

![SOC](https://img.shields.io/badge/Equipe-SOC-green) ![Blue Team](https://img.shields.io/badge/Equipe-Blue%20Team-blue) ![Lab](https://img.shields.io/badge/Type-Laboratório%20Prático-purple) ![SSH](https://img.shields.io/badge/Protocolo-SSH-red) ![Kali](https://img.shields.io/badge/OS-Kali%20Linux-black) ![Linux](https://img.shields.io/badge/OS-Linux-orange) ![Investigation](https://img.shields.io/badge/Foco-Investigação%20de%20Incidente-darkgreen)

---

:brazil: | :us:

---

:brazil:

## 🎯 Objetivo

> Simular um cenário básico de brute force SSH em ambiente controlado e praticar a investigação em nível SOC/Blue Team, usando logs do sistema para identificar tentativas de autenticação anômalas, construir uma timeline e documentar o incidente em formato de relatório.

## 📂 Estrutura do repositório

A organização dos arquivos foi pensada para separar claramente documentação, evidências e artefatos gerados durante o laboratório.

```text
lab-soc-investigacao-brute-force-ssh/
├── README.md                 # Documentação principal do projeto
├── docs/                     # Evidências em formato de texto
│   ├── journalctl.txt        # Saída filtrada do journalctl com as tentativas de login SSH.
├── images/                   # Evidências em formato de imagem
│   ├── brute-force-ip.png
│   ├── brute-force-useradd.png
│   ├── brute-force-passwd-1.png
│   ├── brute-force-passwd-2.png
│   ├── brute-force-journalctl.png
```

### 🧩 1. Resumo do incidente

- Data/hora (aprox.): 06/04/2026, entre 10:26:03 e 10:26:56.

- Alvo: serviço SSH em host Kali Linux (lab).

- Descrição: múltiplas tentativas de login falhas para o usuário `attackerlab` a partir de localhost, simulando um cenário de brute force controlado.

- Classificação: simulação de incidente em ambiente de laboratório (não malicioso).

### 🌐 2. Escopo e ambiente

- Sistema operacional: Kali Linux (VM local).

- Serviço monitorado: SSH (sshd-session).

- Origem das tentativas: ::1 (localhost).

![IP de origem das tentativas de login](./images/brute-force-ip.png)

- Usuário alvo: attackerlab (criado exclusivamente para o lab).

### 🧭 3. Passos executados

- Criação do usuário de laboratório attackerlab em uma VM Kali Linux.

![Criação do usuário attackerlab e sua senha](./images/brute-force-useradd.png)

- Execução de múltiplas tentativas de autenticação SSH com senha incorreta para attackerlab a partir de localhost.

![Múltiplas tentativas de acesso via autentição SSH 1](./images/brute-force-passwd-1.png)

![Múltiplas tentativas de acesso via autentição SSH 2](./images/brute-force-passwd-2.png)

- Coleta de logs via journalctl relacionados ao serviço SSH.

![Logs coletados via comando journalctl](./images/brute-force-journalctl.png)

- Análise das entradas de log para identificar padrão de brute force, origem e impacto.

### 📎 4. Evidências

- Entre 10:26:03 e 10:26:56 de 06/04/2026, foram registradas aproximadamente 6 tentativas de autenticação SSH com senha incorreta para o usuário ‘attackerlab’, originadas do endereço ::1 (localhost), conforme logs do serviço sshd-session. As entradas indicam múltiplas falhas de autenticação e encerramento da conexão após sucessivas tentativas sem sucesso.

- O arquivo de log completo está em: [journalctl.txt](./docs/journalctl.txt).

### 🧠 5. Análise

- Volume e frequência: as tentativas ocorrem em uma janela muito curta (menos de um minuto), com falhas sucessivas de senha para o mesmo usuário, o que se assemelha a um padrão de brute force.

- Origem: o IP ::1 indica que as tentativas partiram do próprio host (localhost), não de um endereço externo.

- Impacto: não há evidência de autenticação bem-sucedida após as falhas, sugerindo que não houve comprometimento da conta.

- Contexto: trata-se de uma simulação controlada em ambiente de laboratório, não de atividade maliciosa em produção.

### 🏁 6. Conclusão e classificação

- Conclusão: o evento analisado representa um conjunto de tentativas de autenticação SSH falhas para o usuário ‘attackerlab’, sem sucesso de login, simulando um cenário de brute force local.

- Classificação: True Positive (tentativa de autenticação anômala) em ambiente de teste, com risco real nulo para ambiente produtivo.

- Status: incidente encerrado como simulação de lab, sem ação corretiva necessária.

### 🛡️  7. Possíveis ações recomendadas

- Habilitar ou ajustar regras de bloqueio após múltiplas falhas de autenticação (ex.: fail2ban).

- Restringir acesso SSH apenas a IPs confiáveis.

- Monitorar continuamente falhas de login SSH e gerar alertas para padrões semelhantes.

### 🌐8. Perspectiva de Network

- Neste lab, o serviço SSH que sofreu as tentativas de brute force opera na porta padrão **22/TCP**, utilizando o protocolo SSH na camada de aplicação sobre TCP na camada de transporte.

- Em termos de rede, um ataque de brute force SSH se reflete em múltiplas conexões TCP direcionadas à porta 22 do host, com várias tentativas de autenticação em sequência para o mesmo usuário, o que pode ser observado tanto em logs de aplicação (sshd) quanto em capturas de tráfego (ex.: muitos pacotes relacionados a sessões TCP em 22/TCP em um intervalo curto).

### 🧠 9. O que aprendi

- A identificar, em logs do sistema (`journalctl`), eventos de autenticação SSH relacionados a tentativas de brute force, usando campos como usuário, origem, porta e timestamp.

- A construir uma **timeline básica de incidente**, agrupando múltiplas falhas de login em uma janela de tempo curta para caracterizar um comportamento anômalo.

- A pensar como um **SOC Analyst L1**, documentando o incidente em formato estruturado (resumo, escopo, evidências, análise, conclusão e ações recomendadas).

- A diferenciar um cenário de teste/ruído interno de um ataque real, considerando origem (`localhost`), ausência de login bem-sucedido e contexto de laboratório.

### 📚 10. Referências

- Documentação e artigos sobre análise de logs e autenticação SSH em Linux, com uso de `journalctl` para investigar eventos de segurança.

- Conteúdos sobre detecção de brute force em SSH e uso de regras/políticas para mitigar esse tipo de ataque em ambientes reais (ex.: bloqueio após múltiplas falhas).

- Guias e materiais sobre fluxo de triagem de alertas e investigação em SOC, incluindo construção de timeline, classificação (true/false positive) e registro de decisão.

---

:us:

## 🎯 Objective

> Simulate a basic SSH brute‑force scenario in a controlled environment and practice SOC/Blue‑Team‑level investigation, using system logs to identify anomalous authentication attempts, build a timeline, and document the incident in the form of a report.

## 📂 Repository structure

The file organization was designed to clearly separate documentation, evidence, and artifacts generated during the lab.

```text
lab-soc-investigacao-brute-force-ssh/
├── README.md                 # Project's main documentation
├── docs/                     # Text format evidences 
│   ├── journalctl.txt        # Filtered output of journalctl with the login SSH attempts
├── images/                   # Images format evidences
│   ├── brute-force-ip.png
│   ├── brute-force-useradd.png
│   ├── brute-force-passwd-1.png
│   ├── brute-force-passwd-2.png
│   ├── brute-force-journalctl.png
```

### 🧩 1. Incident summary

- Date/hour (aprox.): 06/04/2026, between 10:26:03 e 10:26:56.

- Target: SSH service in host Kali Linux (lab).

- Description: multiple failed login attempts for the user `attackerlab` from localhost, simulating a controlled brute‑force scenario.

- Classification: Incident simulation in a laboratory environment (non‑malicious).

### 🌐 2. Scope and envirolment

- Operating system: Kali Linux (local VM).

- Monitored service: SSH (sshd-session).

- Origin of the attempts: ::1 (localhost).

![IP address of the login attempts](./images/brute-force-ip.png)

- Target user: attackerlab (exclusively created for the lab).

### 🧭 3. Steps executed

- Creation of the laboratory user attackerlab on a Kali Linux VM.

![Criação do usuário attackerlab e sua senha](./images/brute-force-useradd.png)

- Execution of multiple SSH authentication attempts with an incorrect password for attackerlab from localhost.

![Múltiplas tentativas de acesso via autentição SSH 1](./images/brute-force-passwd-1.png)

![Múltiplas tentativas de acesso via autentição SSH 2](./images/brute-force-passwd-2.png)

- Collection of logs via journalctl related to the SSH service.

![Logs coletados via comando journalctl](./images/brute-force-journalctl.png)

- Analysis of the log entries to identify brute‑force patterns, origin, and impact.

### 📎 4. Evidences

- Between 10:26:03 and 10:26:56 on 06/04/2026, approximately 6 SSH authentication attempts with an incorrect password for the user ‘attackerlab’ were recorded, originating from the address ::1 (localhost), according to logs from the sshd-session service. The entries indicate multiple authentication failures and termination of the connection after several unsuccessful attempts.

- The full log file is located at: [journalctl.txt](./docs/journalctl.txt).

### 🧠 5. Analysis

- Volume and frequency: the attempts occur within a very short window (less than one minute), with successive failed passwords for the same user, which resembles a brute‑force pattern.

- Origin: the IP address ::1 indicates that the attempts originated from the host itself (localhost), not from an external address.

- Impact: there is no evidence of successful authentication following the failures, suggesting that the account was not compromised.

- Context: this is a controlled simulation in a laboratory environment, not malicious activity in a production setting.

### 🏁 6. Conclusion and classification

- Conclusion: the event analyzed represents a set of failed SSH authentication attempts for the user ‘attackerlab’, with no successful login, simulating a local brute‑force scenario.

- Classification: True Positive (anomalous authentication attempt) in a test environment, with zero real risk to a production environment.

- Status: incident closed as a lab simulation, with no corrective action required.

### 🛡️  7. Recommended possible actions

- Enable or tune lockout rules after multiple failed authentication attempts (e.g., fail2ban).

- Restrict SSH access only to trusted IPs.

- Continuously monitor SSH login failures and generate alerts for similar patterns.

### 🌐8. Networking perspective

- In this lab, the SSH service targeted by brute force attempts runs on the default **22/TCP** port, using the SSH protocol at the application layer over TCP at the transport layer.  

- From a networking point of view, an SSH brute force attack appears as multiple TCP connections to port 22 on the host, with several back-to-back authentication attempts for the same user. This can be observed both in application logs (sshd) and in traffic captures (for example, many packets related to TCP sessions on port 22 within a short time window).

### 🧠 9. What I learned

- How to identify, in system logs (`journalctl`), SSH authentication events related to brute‑force attempts, using fields such as user, source, port, and timestamp.

- How to build a **basic incident timeline**, grouping multiple failed logins within a short time window to characterize anomalous behavior.

- How to think like a **SOC Analyst L1**, documenting the incident in a structured format (summary, scope, evidence, analysis, conclusion, and recommended actions).

- How to distinguish a test/internal‑noise scenario from a real attack, by considering the source (`localhost`), the absence of a successful login, and the laboratory context.

### 📚 10. References

- Documentation and articles on Linux SSH authentication log analysis, using `journalctl` to investigate security events.

- Content on SSH brute‑force detection and the use of rules/policies to mitigate this type of attack in real environments (e.g., blocking after multiple failures).

- Guides and materials on SOC alert triage workflows and investigation, including timeline construction, classification (true/false positive), and decision‑making documentation.

---

:brazil:

#### ⚠️ Aviso importante

> Todas as atividades descritas neste laboratório foram executadas em ambiente controlado, utilizando uma máquina virtual Kali Linux de uso próprio, sem qualquer exposição a sistemas de terceiros ou infraestruturas de produção.

> Os testes de autenticação SSH e simulação de brute force tiveram finalidade exclusivamente educacional, com foco em desenvolvimento de habilidades de monitoramento, análise de logs e documentação de incidentes para atuação em SOC/Blue Team.

---

:us:

#### ⚠️ Important warning

> All activities described in this lab were performed in a controlled environment, using a personal Kali Linux virtual machine, with no exposure to third‑party systems or production infrastructure.

> The SSH authentication tests and brute‑force simulation were conducted for exclusively educational purposes, with a focus on developing skills in monitoring, log analysis, and incident documentation for SOC/Blue‑Team operations.
