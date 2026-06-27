# 📊 Splunk + SOC Immersive — Investigação de Phishing

**Plataforma:** TryHackMe  
**Categoria:** SIEM / Blue Team / Incident Response  
**Data:** junho/2026

---

## Contexto
Nessa sala, investiguei um alerta de e-mail suspeito usando o **Splunk** como SIEM
e documentei o caso no **SOC Immersive** (sistema de Case Reports).

---

## 🔍 Investigação no Splunk

**Query utilizada:**
```spl
* j.garcia@thetrydaily.thm
```

**Resultado:** 1 de 2 eventos corresponderam no intervalo de 6 horas.

### E-mail encontrado

| Campo | Valor |
|-------|-------|
| Recipient | j.garcia@thetrydaily.thm |
| Sender | onboarding@hrconnex.thm |
| Subject | Action Required: Finalize Your Onboarding Profile |
| Timestamp | 06/27/2026 17:50:23.686 |
| Datasource | email |
| Direction | inbound |
| Attachment | None |

**Conteúdo do e-mail:**
> "Hi Ms. Garcia, Welcome to TheTryDaily! As part of your onboarding, please complete your final profile setup so we can configure your access. Kindly click the link below: [Set Up My Profile](https://hrconnex.thm/onboarding/15400654060/j.garcia)"

### 🖼️ Prints
![Busca no Splunk](prints/splunk-search.png)

---

## 📋 Case Report (SOC Immersive)

**Event ID:** 8814  
**Alert rule:** Inbound Email Containing Suspicious External Link  
**Incident type:** Phishing  
**Severity:** Medium  
**Data/hora:** Jun 27, 2026 às 13:52

**Descrição do alerta:**
> O alerta foi disparado por um e-mail inbound contendo um ou mais links externos com características potencialmente suspeitas. Como parte da investigação, verificar logs de firewall/proxy para determinar se algum endpoint acessou as URLs do e-mail.

### 🖼️ Prints
![Case Report](prints/case-report-8814.png)

---

## Análise: É phishing?

**Indicadores suspeitos:**
- ✅ Domínio do remetente (`hrconnex.thm`) diferente do domínio da empresa (`thetrydaily.thm`)
- ✅ Link aponta para domínio externo (`hrconnex.thm`)
- ✅ Urgência na mensagem ("Action Required")
- ✅ Solicitação de ação imediata (clicar em link para "finalizar perfil")
- ✅ Tema de onboarding é clássico em ataques de engenharia social

**Conclusão:** E-mail com alto indicativo de **phishing/spearphishing**.
Próximo passo seria verificar nos logs de proxy se j.garcia clicou no link.

---

## Conceitos aprendidos

### SPL (Search Processing Language)
Linguagem de busca do Splunk. Permite filtrar, agregar e visualizar logs.

```spl
# Busca simples por campo
* j.garcia@thetrydaily.thm

# Filtrar por sourcetype
index=main sourcetype=email recipient="j.garcia@thetrydaily.thm"

# Contar e-mails por remetente
index=main sourcetype=email | stats count by sender
```

### O que verificar em e-mails suspeitos
1. **Remetente**: o domínio bate com quem diz ser?
2. **Links**: apontam para domínios legítimos?
3. **Urgência**: linguagem que pressiona o usuário?
4. **Anexos**: arquivos executáveis ou documentos com macro?
5. **Contexto**: a pessoa estava esperando esse e-mail?

### Phishing vs Spearphishing
| Tipo | Característica |
|------|---------------|
| Phishing | Genérico, enviado em massa |
| Spearphishing | Direcionado, personalizado para a vítima |
| Whaling | Spearphishing focado em executivos (CEO, CFO) |
