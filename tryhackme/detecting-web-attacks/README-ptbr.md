# 🕵️ Detectando Ataques Web

**Plataforma:** TryHackMe  
**Nível:** ⭐ Fácil  
**Categoria:** Blue Team / Segurança Web / SOC  
**Data:** 13/07/2026

---

## Visão Geral

Ataques web são um dos vetores de entrada mais comuns para atacantes.
Aplicações web públicas geralmente ficam na frente de bancos de dados e infraestrutura crítica,
tornando-as alvos de alto valor. Essa sala cobre como **detectar** ataques web usando
logs, análise de tráfego de rede e WAFs.

---

## 🖥️ Ataques Client-Side (Lado do Cliente)

Ataques que exploram fraquezas no **navegador ou comportamento do usuário**.

> Limitação: Logs do servidor e capturas de rede oferecem **pouca ou nenhuma visibilidade**
> sobre o que acontece dentro do navegador do usuário. Esses ataques são difíceis de detectar do ponto de vista do SOC.

### Ataques Client-Side mais comuns

| Ataque | Como funciona |
|--------|--------------|
| **XSS (Cross-Site Scripting)** | Scripts maliciosos injetados em um site confiável e executados no navegador da vítima |
| **CSRF (Cross-Site Request Forgery)** | Navegador enganado para enviar requisições não autorizadas em nome do usuário |
| **Clickjacking** | Elementos invisíveis sobrepostos a conteúdo legítimo para enganar o usuário |

**Exemplo de XSS:**
```html
Hello <script>alert('You have been hacked');</script>
```
Se uma caixa de comentários não filtra o input, esse script roda no navegador de todo visitante.
Em um ataque real, em vez de um popup inofensivo, poderia roubar cookies de sessão.

---

## 🖧 Ataques Server-Side (Lado do Servidor)

Ataques que exploram vulnerabilidades no **servidor web, código da aplicação ou backend**.

> Vantagem para defensores: ataques server-side **deixam rastros** em logs e tráfego de rede.

### Ataques Server-Side mais comuns

| Ataque | Como funciona | Exemplo real |
|--------|--------------|-------------|
| **Força Bruta** | Tentativas automáticas repetidas de login | T-Mobile 2021 — dados de 50M de clientes expostos |
| **SQL Injection (SQLi)** | Comandos SQL maliciosos injetados via formulários | MOVEit 2023 — mais de 2.700 organizações afetadas |
| **Injeção de Comando** | Comandos do sistema injetados via input do usuário | Servidor executa comandos com permissões da aplicação |

---

## 📋 Detecção por Logs

### Formato do Log de Acesso

| Campo | Indicador Suspeito |
|-------|-------------------|
| IP do cliente | IP malicioso conhecido ou geolocalização inesperada |
| Timestamp | Requisições em horários incomuns ou alta frequência |
| Código de status | Muitos 404s = varredura de diretórios |
| Tamanho da resposta | Respostas muito menores ou maiores que o normal |
| Referrer | Páginas que não fazem parte da navegação normal |
| User-Agent | Ferramentas de ataque como `sqlmap`, `wpscan`, `gobuster` |

### Sequência de ataque nos logs

**Passo 1 — Directory fuzzing (varredura de diretórios)**
```
GET /admin     200  ← página válida encontrada
GET /login.php 200  ← página válida encontrada
GET /backup    404
GET /config    404
```
Muitos 404s com alguns 200s = atacante mapeando a aplicação.

**Passo 2 — Ataque de força bruta**
```
POST /login.php 401  ← tentativa falhou
POST /login.php 401  ← tentativa falhou
POST /login.php 401  ← tentativa falhou
POST /login.php 302  ← SUCESSO (redirecionou para /account)
```
Múltiplos POST em sequência rápida = força bruta.
Status `302 Found` = login bem-sucedido com redirecionamento.

**Passo 3 — Tentativas de SQL Injection**
```
GET /search?q=' OR '1'='1     200
GET /search?q=1' OR 'a'='a   200
```
Caracteres especiais e palavras-chave SQL na query string = tentativa de SQLi.

### Limitações dos logs
Logs de acesso **não registram o corpo dos POSTs** — você vê que houve uma tentativa de login
mas não as credenciais enviadas. Query strings de GET podem ou não ser registradas
dependendo da configuração do servidor.

---

## 🔬 Análise de Tráfego de Rede (Wireshark)

Capturas de rede são **mais detalhadas que logs** — revelam o conteúdo completo
de cada requisição e resposta, incluindo headers, corpo do POST, cookies e arquivos.

> Limitação: Tráfego criptografado (HTTPS) limita a visibilidade sem as chaves de descriptografia.
> Os exemplos desta sala focam em tráfego HTTP.

### O que capturas de rede revelam que os logs não mostram

| O que os logs mostram | O que o Wireshark adiciona |
|----------------------|--------------------------|
| POST /login — 302 | Usuário e senha reais enviados |
| GET /search?q=... | Payload SQLi completo e resposta do banco de dados |
| GET /upload — 200 | Conteúdo real do arquivo enviado |

### Filtros úteis no Wireshark
```
http                          # mostrar apenas tráfego HTTP
http.user_agent               # filtrar por user agent
ip.dst == 10.10.20.200        # filtrar por IP de destino
```

**Clique direito → Follow HTTP Stream** reconstrói a conversa completa entre cliente e servidor.

### Sequência de ataque no Wireshark

**Força bruta:** Pacotes POST repetidos → o último bem-sucedido revela a senha válida (`password123`)

**SQLi:** Inspecionar o corpo do pacote HTTP mostra o payload exato usado
e os dados retornados pelo banco (ex: tabela Users com nomes em texto claro)

---

## 🔥 WAF — Web Application Firewall

WAFs funcionam como **guardiões** — inspecionam pacotes de requisição completos e os filtram
antes de chegarem ao servidor. Diferente do Wireshark, WAFs também conseguem descriptografar tráfego HTTPS.

### Tipos de regras WAF

| Tipo de Regra | Descrição | Exemplo |
|--------------|-----------|---------|
| Bloquear padrões de ataque | Bloqueia payloads maliciosos conhecidos | Bloquear User-Agent: `sqlmap` |
| Negar fontes maliciosas | Reputação de IP e geo-bloqueio | Bloquear IPs de campanhas de botnet |
| Regras customizadas | Adaptadas à sua aplicação | Permitir apenas GET/POST em `/login` |
| Rate limiting | Limita frequência de requisições | Máx. 5 tentativas de login/min por IP |

### Exemplo — Regra WAF customizada
```
Se User-Agent contém "sqlmap"
então BLOQUEAR
```

### Mecanismos de Challenge-Response
WAFs podem também **desafiar** requisições suspeitas com CAPTCHA em vez de bloquear diretamente.
Útil quando há maior chance de falsos positivos (bloquear usuários legítimos).

> 37% do tráfego global da web vem de bots maliciosos.

### Integração com Inteligência de Ameaças
WAFs modernos incluem:
- Conjuntos de regras cobrindo o **OWASP Top 10**
- Bloqueio automático de IPs maliciosos conhecidos
- Atualizações regulares para novos CVEs e TTPs de grupos de ameaças
- Listas de IPs curadas: botnets, VPNs, anonimizadores, malware (ex: Cloudflare)

---

## 🧠 Resumo dos conceitos-chave

| Conceito | Definição |
|---------|----------|
| **Ataque client-side** | Explora comportamento do usuário ou vulnerabilidades do navegador |
| **Ataque server-side** | Explora servidor web, código da aplicação ou backend |
| **XSS** | Scripts maliciosos injetados em sites confiáveis |
| **CSRF** | Navegador enganado para fazer requisições não autorizadas |
| **SQLi** | Comandos SQL injetados para manipular bancos de dados |
| **Força Bruta** | Tentativas automáticas repetidas de login |
| **Directory Fuzzing** | Varredura automatizada de páginas e diretórios válidos |
| **Log de Acesso** | Registro de cada requisição recebida pelo servidor web |
| **WAF** | Firewall especializado em filtrar tráfego web |
| **Rate Limiting** | Restringe frequência de requisições para prevenir abuso |

---

## ✅ Respostas da Sala

| Pergunta | Resposta |
|----------|---------|
| Que classe de ataques explora comportamento do usuário? | Client-side attacks |
| Qual é o ataque client-side mais comum? | Cross-Site Scripting (XSS) |
| Que classe de ataques explora vulnerabilidades no servidor? | Server-side attacks |
| Qual ataque server-side permite dumpar banco de dados? | SQL Injection (SQLi) |
| O que WAFs inspecionam e filtram? | Web requests (requisições web) |
