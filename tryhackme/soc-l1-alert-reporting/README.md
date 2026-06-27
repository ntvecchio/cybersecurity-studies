# 🚨 SOC L1 - Alert Reporting

**Plataforma:** TryHackMe  
**Categoria:** Blue Team / SOC  
**Data:** junho/2026

---

## O que é um SOC?
SOC (Security Operations Center) é o time responsável por monitorar,
detectar, investigar e responder a incidentes de segurança.

### Níveis de analistas
| Nível | Responsabilidade |
|-------|-----------------|
| L1 | Triagem de alertas, classificação inicial |
| L2 | Investigação aprofundada de incidentes |
| L3 | Threat hunting, resposta a incidentes críticos |

---

## Prática: SOC L1 Alert Reporting

Nessa sala, atuei como analista L1 fazendo a triagem de alertas de segurança.

### Alertas investigados

| Horário | Nome do Alerta | Severidade | Status | Veredito | Analista |
|---------|---------------|-----------|--------|---------|---------|
| 19:56 | Spike of Domain Discovery Commands | Medium | Awaiting action | — | — |
| 19:25 | Email Marked as Phishing after Delivery | Medium | Awaiting action | — | — |
| 19:10 | Web Scanning of Corporate Resources | Low | In Progress | ✅ True Positive | E.Fleming (L2) |
| 18:30 | Sensitive Document Share to External | Medium | In Progress | ✅ True Positive | E.Fleming (L2) |
| 18:02 | Fast Beaconing to Untrusted Domain | High | Closed | ❌ False Positive | S.Todd (L1) |

### Detalhes: Web Scanning of Corporate Resources
- **Source IP:** 5.178.87.180
- **Alvos:** DMZ-WEBFRONT-PRD, DMZ-WEBAPI-PRD, DMZ-MSEXCHANGE-2013
- **Request Sample:** `https://www.tryhackme.thm/products?id=1' OR '1'='1` *(SQL Injection!)*
- **Total de requests:** 865.700
- **URIs únicas:** 112.569
- **Análise:** O atacante realizou um scan massivo tentando explorar vulnerabilidades como XSS e SQLi. Pelo volume e pela amostra de SQL Injection, foi classificado como **True Positive** e escalado para L2.

### 🖼️ Print
![SOC L1 Alert Dashboard](prints/soc-l1-dashboard.png)

---

## Conceitos aprendidos

### True Positive vs False Positive
- **True Positive**: o alerta é real, houve atividade maliciosa de fato
- **False Positive**: o alerta disparou, mas a atividade é legítima
- **True Negative**: não houve alerta e não houve ataque (correto)
- **False Negative**: houve ataque mas o sistema não detectou (pior cenário)

### O que é Triagem (Triage)?
Processo de análise inicial dos alertas para determinar:
1. O alerta é válido ou falso positivo?
2. Qual a severidade real?
3. Precisa ser escalado?

### SQL Injection
Ataque onde comandos SQL são inseridos em campos de entrada para manipular o banco de dados.
`id=1' OR '1'='1` força a condição a ser sempre verdadeira, retornando todos os registros.

### Beaconing
Comportamento onde um malware contacta periodicamente seu servidor C2 (Command & Control).
O intervalo regular de comunicação é o que gera o alerta de "Fast Beaconing".
