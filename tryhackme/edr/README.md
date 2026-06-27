# 🛡️ EDR - Endpoint Detection & Response

**Plataforma:** TryHackMe  
**Categoria:** Blue Team / SOC  
**Data:** junho/2026

---

## O que é EDR?
EDR (Endpoint Detection and Response) é uma solução de segurança que monitora
endpoints (computadores, servidores) em tempo real para detectar comportamentos
suspeitos e responder a incidentes.

**Diferença do antivírus tradicional:**
- Antivírus → detecta arquivos maliciosos por assinatura
- EDR → detecta comportamentos maliciosos mesmo sem assinatura conhecida

---

## Caso prático: Initial Access via Malicious Office Document

### 📋 Resumo do alerta
Um documento Office com macro (`invoice.docm`) foi aberto pelo `WINWORD.EXE`.
A macro disparou o `CMD.EXE`, que usou o `cURL` para baixar um payload de um domínio externo.
O arquivo foi salvo em disco mas **não executado** — comportamento típico de *malware staging*.

| Campo | Valor |
|-------|-------|
| Usuário | alice.thomas |
| Host | DESKTOP-HR01 |
| Timestamp | Jun 27, 2026 às 13:03 |
| Severidade | 🔴 High |
| Status | Not Acknowledged |
| Tática MITRE | Initial Access |
| Técnica | T1566.001 - Spearphishing Attachment |

### 🔗 Process Chain (cadeia de processos)
```
WINWORD.EXE
    └── CMD.EXE
            └── cURL.EXE
                    └── INSTALL.EXE (salvo, não executado)
```

**Detalhes do WINWORD.EXE:**
- Path: `C:\Program Files\Microsoft Office\root\Office16\WINWORD.EXE`
- Command Line: `WINWORD.EXE /n "invoice.docm"`
- PID: 3124
- Parent Process: explorer.exe
- User: alice.thomas
- Behavior: Spawned CMD after opening macro-enabled document
- SHA256: `e59a3b3e445c2ad9132df1a90a3f5e6dfbb19b87fa77cddf5c82d33f3f10ae84`

### 🚨 IOCs (Indicators of Compromise)

| Tipo | Valor | Fonte | Ação |
|------|-------|-------|------|
| File Path | `C:\Users\Public\install.exe` | Suspicious Binary | Quarantined |
| File Name | `install.exe` | Dropped Payload | Flagged |
| Domain | `ayebd.thm` | C2 Server | Blocked |
| IP Address | `1.161.138.92` | External Download | Logged |
| SHA256 | `9e107d9d372bb6826bd81d3542a419d6eaf1e3f5b94fc3b1f69413c5c30ef2e5` | Unknown Executable | Analyzed, Flagged |
| File Path | `C:\User\alice\Downloads\invoice.docm` | Suspicious Document | Quarantined |

### 🖼️ Prints

**Summary do alerta:**
![EDR Summary](prints/edr-summary.png)

**Process Chain:**
![Process Chain](prints/edr-process-chain.png)

**IOC/Indicators:**
![IOCs](prints/edr-iocs.png)

---

## Conceitos aprendidos

### O que é Malware Staging?
Técnica onde o payload é baixado e salvo em disco mas **não é executado imediatamente**.
O atacante faz isso para:
- Testar se o ambiente está sendo monitorado
- Executar o payload em um momento mais oportuno
- Dificultar a detecção

### O que é MITRE ATT&CK?
Framework que cataloga táticas e técnicas usadas por atacantes reais.
- **Tática**: o objetivo (ex: Initial Access, Persistence, Exfiltration)
- **Técnica**: como o objetivo é alcançado (ex: T1566.001 - Spearphishing Attachment)

### Spearphishing Attachment (T1566.001)
Phishing direcionado onde o atacante envia um arquivo malicioso por e-mail.
Diferente do phishing genérico, o spearphishing é personalizado para a vítima.
