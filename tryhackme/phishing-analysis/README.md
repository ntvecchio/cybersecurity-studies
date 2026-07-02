# 🎣 Phishing Analysis Fundamentals

**Plataforma:** TryHackMe  
**Categoria:** Blue Team / SOC / Email Security  
**Conclusão:** junho/2026  
**Progresso:** ✅ 100%

---

## O que é Phishing?
Phishing é um ataque de engenharia social onde o atacante se passa por uma
entidade confiável para enganar a vítima e obter credenciais, dados sensíveis
ou fazer com que execute algo malicioso.

---

## 🔧 Ferramentas utilizadas

### PhishTool
Ferramenta usada para analisar e-mails de phishing de forma detalhada.
Mostra todas as informações do e-mail organizadas: cabeçalhos, remetente,
IP de origem, links, anexos e muito mais.
- Site: [phishtool.com](https://phishtool.com)

### CyberChef
Ferramenta online para codificar, decodificar e transformar dados.
Usada para decodificar conteúdo em **Base64** encontrado nos e-mails.
- Site: [gchq.github.io/CyberChef](https://gchq.github.io/CyberChef)

---

## 📧 Como analisar um e-mail suspeito

### 1. Cabeçalho do e-mail (Email Header)
O cabeçalho contém informações técnicas que o usuário normal não vê.
Para visualizar: **Ctrl+U** (abre o código fonte do e-mail no navegador)

**O que procurar no cabeçalho:**

| Campo | O que verifica |
|-------|---------------|
| `From` | Endereço do remetente — bate com quem diz ser? |
| `Reply-To` | Se diferente do `From`, é sinal de alerta |
| `Return-Path` | Para onde vai se você responder |
| `Received` | Caminho que o e-mail percorreu até chegar |
| `X-Originating-IP` | **IP real de onde o e-mail foi enviado** |
| `Message-ID` | Identificador único do e-mail |
| `DKIM / SPF / DMARC` | Verificações de autenticidade do domínio |

### 2. IP de origem
O campo `X-Originating-IP` ou `Received: from` revela o IP real do servidor
que enviou o e-mail — mesmo que o `From` esteja falsificado.

Com o IP em mãos, dá para:
- Pesquisar no **VirusTotal** se o IP tem histórico malicioso
- Usar **AbuseIPDB** para ver se foi reportado
- Fazer lookup de geolocalização (o IP bate com o país da empresa remetente?)

### 3. Corpo do e-mail
O que analisar no conteúdo:

- **Links**: o texto do link bate com o destino real? Passe o mouse em cima sem clicar
- **Urgência**: linguagem que pressiona ("sua conta será suspensa em 24h")
- **Erros de português**: remetentes legítimos raramente erram
- **Anexos**: extensões perigosas: `.exe`, `.docm`, `.js`, `.vbs`, `.iso`
- **Domínio do remetente**: `suporte@paypa1.com` vs `suporte@paypal.com`

---

## 🔐 Base64 e decodificação

### O que é Base64?
Base64 é uma forma de codificar dados em texto. Atacantes usam isso para:
- Esconder links maliciosos no código do e-mail
- Ofuscar o conteúdo e escapar de filtros de segurança
- Codificar payloads em scripts

**Exemplo:**
```
Texto original:  http://malicioso.com/payload
Em Base64:       aHR0cDovL21hbGljaW9zby5jb20vcGF5bG9hZA==
```

### Como decodificar com CyberChef
1. Acesse [gchq.github.io/CyberChef](https://gchq.github.io/CyberChef)
2. No campo **Input**, cole o texto codificado
3. Na barra de operações, arraste **"From Base64"** para a receita
4. O resultado aparece em **Output**

---

## 🚩 Red Flags — sinais de phishing

| Sinal | Exemplo |
|-------|---------|
| Domínio parecido | `arnazon.com` ao invés de `amazon.com` |
| Reply-To diferente do From | E-mail vem do banco mas resposta vai para gmail |
| IP de origem suspeito | IP de país diferente do remetente |
| Link com redirecionamento | `bit.ly/xyz` que leva para site malicioso |
| Urgência na mensagem | "Clique agora ou perca acesso" |
| Anexo executável | `.exe`, `.docm`, `.js` |
| Base64 no código fonte | Conteúdo codificado escondendo links |

---

## 🛡️ SPF, DKIM e DMARC

Protocolos de autenticação de e-mail que verificam se o remetente é legítimo:

| Protocolo | O que faz |
|-----------|----------|
| **SPF** | Verifica se o IP está autorizado a enviar pelo domínio |
| **DKIM** | Assinatura digital que garante que o e-mail não foi alterado |
| **DMARC** | Define o que fazer se SPF/DKIM falharem (quarentena, rejeitar) |

Se um e-mail chega com **SPF: fail** ou **DKIM: fail**, é sinal forte de spoofing.

---

## Fluxo de análise de um e-mail suspeito

```
Recebeu e-mail suspeito
        ↓
Abre no PhishTool ou Ctrl+U para ver cabeçalho
        ↓
Verifica IP de origem → pesquisa no VirusTotal / AbuseIPDB
        ↓
Verifica domínio do remetente → é legítimo?
        ↓
Analisa links → hover para ver destino real
        ↓
Verifica código fonte → tem Base64? Decodifica no CyberChef
        ↓
Verifica SPF / DKIM / DMARC
        ↓
Classifica: Phishing ✅ ou Legítimo ❌
```
