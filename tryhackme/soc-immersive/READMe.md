# 🖥️ SOC Immersive — Triagem de Alertas e Investigação

**Plataforma:** TryHackMe  
**Categoria:** Blue Team / SOC / Incident Response  
**Data:** 05/07/2026

---

## Contexto
Simulação de ambiente SOC real onde atuei como analista L1, triando alertas
da fila e investigando eventos suspeitos no Splunk.

---

## 📋 Alertas investigados

### 🔴 Alerta #1000 — Suspicious email from external domain
**Tipo:** Phishing | **Severidade:** Low | **Data:** 05/07/2026 às 19:14

| Campo | Valor |
|-------|-------|
| Subject | Inheritance Alert: Unknown Billionaire Relative Left You Their Hat Fortunes |
| Sender | eileen@trendymillineryco.me |
| Recipient | support@tryhatme.com |
| Attachment | None |
| Direction | Inbound |
| Timestamp | 07/05/2026 19:12:26.253 |

**Análise:**
- Domínio do remetente (`trendymillineryco.me`) não tem relação com o destinatário
- Assunto clássico de golpe ("bilionário desconhecido deixou herança")
- Solicita dados bancários imediatamente → urgência + engenharia social
- **Conclusão: Phishing confirmado**

---

### 🟡 Alerta #1001 — Suspicious Parent Child Relationship
**Tipo:** Process | **Severidade:** Low | **Data:** 05/07/2026 às 19:16

| Campo | Valor |
|-------|-------|
| Host | win-3459 |
| Datasource | sysmon |
| Process | TrustedInstaller.exe |
| PID | 3577 |
| Parent Process | services.exe (PID 3506) |
| Command Line | `C:\Windows\servicing\TrustedInstaller.exe` |
| Working Directory | `C:\Windows\system32\` |
| Event Action | Process Create |

**Análise:**
- `TrustedInstaller.exe` normalmente é iniciado pelo sistema para gerenciar atualizações do Windows
- Ser filho de `services.exe` pode ser legítimo mas merece atenção
- Caminho correto (`C:\Windows\servicing\`) — não foi movido para pasta suspeita
- **Requer correlação com outros eventos do mesmo host**

---

### 🟡 Alerta #1009 — Suspicious Parent Child Relationship
**Tipo:** Process | **Severidade:** Low | **Data:** 05/07/2026 às 19:29

| Campo | Valor |
|-------|-------|
| Host | win-3453 |
| Datasource | sysmon |
| Process | rdpclip.exe |
| PID | 3565 |
| Parent Process | svchost.exe (PID 3925) |
| Command Line | `rdpclip` |
| Working Directory | `C:\Windows\system32\` |
| Event Action | Process Create |

**Análise:**
- `rdpclip.exe` é o processo de clipboard do RDP (Remote Desktop Protocol)
- Normalmente é iniciado durante sessões RDP legítimas
- **Indica que pode haver uma sessão RDP ativa no host win-3453**
- Vale investigar: quem está conectado via RDP nesse host?

---

### 🟡 Alerta #1012 — Suspicious Parent Child Relationship
**Tipo:** Process | **Severidade:** Low | **Data:** 05/07/2026 às 19:38

| Campo | Valor |
|-------|-------|
| Host | win-3459 |
| Datasource | sysmon |
| Process | svchost.exe |
| PID | 3842 |
| Parent Process | services.exe (PID 3700) |
| Command Line | `C:\Windows\system32\svchost.exe -k wsappx -p` |
| Working Directory | `C:\Windows\system32\` |
| Event Action | Process Create |

**Análise:**
- `svchost.exe -k wsappx` é relacionado à Windows Store e serviços de app
- Filho de `services.exe` é o comportamento esperado para svchost
- Caminho legítimo (`C:\Windows\system32\`)
- **Possivelmente False Positive** — comportamento normal do Windows

---

## 🔍 Investigação no Splunk

**Query utilizada:**
```spl
win-3459
```
**Resultado:** 2 de 2 eventos corresponderam no intervalo de 6 horas.

### Evento 1 — TrustedInstaller.exe
```
host: win-3459
datasource: sysmon
event.action: Process Create
event.code: 1
process.name: TrustedInstaller.exe
process.command_line: C:\Windows\servicing\TrustedInstaller.exe
process.parent.name: services.exe
process.parent.pid: 3506
process.pid: 3577
timestamp: 07/05/2026 19:14:37.253
```

### Evento 2 — TSTheme.exe
```
host: win-3459
datasource: sysmon
event.action: Process Create
event.code: 1
process.name: TSTheme.exe
process.command_line: C:\Windows\system32\TSTheme.exe -Embedding
timestamp: 07/05/2026 19:12:20.253
process.pid: 3508
process.parent.pid: 3848
```

> **Nota:** `TSTheme.exe` é o processo de tema do Terminal Server (RDP).
> Junto com `rdpclip.exe`, reforça que há atividade de RDP nos hosts investigados.

### 🖼️ Prints
![Alert Queue - Phishing](prints/alert-queue-phishing.png)
![Alert - Parent Child 1001](prints/alert-parent-child-1001.png)
![Alert - Parent Child 1009](prints/alert-parent-child-1009.png)
![Alert - Parent Child 1012](prints/alert-parent-child-1012.png)
![Splunk - win-3459](prints/splunk-win3459.png)

---

## Conceitos aprendidos

### O que é Sysmon?
**System Monitor** — ferramenta da Microsoft que registra eventos detalhados
do sistema no Windows, como criação de processos, conexões de rede e alterações
em arquivos. É uma fonte essencial de logs para SOC.

**Event Code 1 = Process Create** — registra toda vez que um processo é criado,
incluindo nome, PID, processo pai e linha de comando.

### O que é Parent-Child Relationship?
Todo processo no Windows tem um processo pai que o iniciou.
Relações suspeitas acontecem quando um processo é iniciado por um pai inesperado.

**Exemplos suspeitos:**
```
Word.exe → cmd.exe          ← macro maliciosa
explorer.exe → powershell   ← pode ser suspeito
svchost.exe → mimikatz.exe  ← definitivamente malicioso
```

**Exemplos normais:**
```
services.exe → svchost.exe  ← comportamento esperado
explorer.exe → notepad.exe  ← usuário abriu o bloco de notas
```

### O que é RDP?
**Remote Desktop Protocol** — protocolo que permite acesso remoto à área de
trabalho de outro computador. Processos associados:
- `rdpclip.exe` — gerencia o clipboard entre sessões RDP
- `TSTheme.exe` — gerencia temas visuais em sessões RDP

### Fluxo de triagem de alertas (L1)
```
Recebe alerta na fila
        ↓
Lê a descrição e identifica o tipo
        ↓
Coleta contexto: host, usuário, processo, timestamp
        ↓
Investiga no SIEM/Splunk
        ↓
Classifica: True Positive / False Positive
        ↓
Escala para L2 ou fecha o alerta
```
