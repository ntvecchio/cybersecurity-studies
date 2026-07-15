# 🌐 Fundamentos de Segurança em Aplicações Web

**Plataforma:** TryHackMe  
**Nível:** ⭐ Fácil  
**Categoria:** Blue Team / Segurança Web  
**Data:** 13/07/2026

---

## Por que segurança em aplicações web é importante?

Nas últimas décadas, as aplicações migraram do desktop para a web.
Hoje quase tudo roda no navegador — e-mail, banco, redes sociais, até máquinas de laboratório.

Essa mudança traz vantagens mas também **sérios riscos de segurança**:

| Como Dono do App | Como Usuário |
|------------------|-------------|
| App exposto 24/7 | Dados armazenados potencialmente de forma insegura |
| Qualquer pessoa no mundo pode tentar atacar | Navegador comprometido = todas as contas em risco |
| Responsável por proteger dados dos usuários | Risco de roubo de identidade ou prejuízo financeiro |

### Exemplos reais
- **Equifax (2017):** dados de 150 milhões de americanos expostos por vulnerabilidade no Apache Struts
- **Capital One (2019):** 100 milhões de clientes afetados por WAF mal configurado

> **Princípio fundamental:** Quem coleta e armazena dados é responsável por protegê-los.
> O **dono da aplicação web** é sempre o responsável final pela segurança dos dados dos usuários.

---

## 🔄 O Ciclo de Requisição e Resposta

Toda interação com um site segue esse ciclo:

```
[Navegador] --- Requisição (Request) ---> [Servidor Web]
                                                |
                                        Processa a requisição
                                                |
[Navegador] <-- Resposta (Response) --- [Servidor Web]
```

- **GET** → buscar um recurso (página web, imagem)
- **POST** → enviar dados (credenciais de login, formulários)

Atacantes abusam desse ciclo através de:
- Sobrecarga de requisições (DDoS)
- Bypass de controles de acesso
- Execução de comandos maliciosos no servidor

---

## 🏗️ Os Três Componentes de Qualquer Serviço Web

| Componente | O que é | Exemplo |
|-----------|---------|---------|
| **Aplicação** | O código, imagens, estilos — o que você vê e interage | WordPress, app em Python Flask |
| **Servidor Web** | Software que escuta requisições e retorna respostas | Apache, Nginx, IIS |
| **Máquina Host** | O sistema operacional que roda tudo | Ubuntu Linux, Windows Server |

### Servidores web mais comuns

| Servidor | Usado por |
|----------|----------|
| **Apache** | O mais popular — sites WordPress |
| **Nginx** | Apps de alta performance — Netflix, Airbnb, GitHub |
| **IIS** | Ambientes corporativos Microsoft |

---

## 🛡️ Defesa em Profundidade — Protegendo as Três Camadas

A ideia é simples: **nunca depender de só uma barreira de segurança.**
Se um atacante passa pela aplicação, encontra o servidor web.
Se passa pelo servidor web, a máquina host o para.

### Protegendo a Aplicação
- **Código Seguro:** evitar funções inseguras, tratar erros corretamente
- **Validação e Sanitização de Entrada:** nunca confiar no input do usuário — validar tudo no servidor
- **Controle de Acesso:** restringir acesso por nível de permissão do usuário

> 🔑 Regra de ouro no desenvolvimento web: **"Nunca confie nos dados que vêm do usuário."**
> Tudo enviado pelo navegador deve ser validado no back-end antes de ser processado.

### Protegendo o Servidor Web
- **Logs de Acesso:** registro detalhado de cada requisição (IP, timestamp, página, status, user agent)
- **WAF (Web Application Firewall):** filtra e bloqueia tráfego malicioso
- **CDN (Content Delivery Network):** reduz exposição direta do servidor

### Protegendo a Máquina Host
- **Menor Privilégio:** nunca rodar serviços como root — usar usuários com baixo privilégio (ex: `www-data`)
- **Hardening do Sistema:** desativar serviços desnecessários, fechar portas não utilizadas
- **Antivírus:** bloqueia malwares conhecidos no nível do endpoint

### Aplicável às três camadas
- **Autenticação Forte:** proteger painéis admin e acesso à máquina host
- **Patch Management:** manter dependências do app, servidor web e SO atualizados

> A maioria das invasões bem-sucedidas acontece porque as organizações esquecem de atualizar seus sistemas.
> Um simples `sudo apt update && sudo apt upgrade` resolve grande parte das vulnerabilidades conhecidas.

---

## 📋 Logs de Acesso

Servidores web registram cada requisição recebida. Esses logs são essenciais para investigações.

**Campos tipicamente registrados:**
- IP do cliente
- Timestamp
- Página ou recurso solicitado
- Método HTTP (GET, POST)
- Código de status da resposta
- User agent (navegador ou ferramenta usada)

**Exemplo de sequência de log (comportamento normal):**
```
10.10.10.100 GET  /index.html     → usuário visita a página inicial
10.10.10.100 GET  /login.html     → navega para o login
10.10.10.100 POST /login.html     → envia as credenciais
10.10.10.100 GET  /myaccount.html → acessa a página da conta
```

Mesmo tráfego normal como esse ajuda analistas a reconstruir sequências de ataque durante resposta a incidentes.

---

## 🌍 CDN (Content Delivery Network)

CDNs armazenam conteúdo em cache em servidores mais próximos do usuário para reduzir latência.

```
[Usuário no Brasil] → [Servidor de borda em SP] → [Servidor de origem nos EUA]
```

### Benefícios de segurança

| Benefício | Como ajuda |
|-----------|-----------|
| **IP Masking** | Esconde o IP real do servidor de origem |
| **Proteção DDoS** | Absorve picos massivos de tráfego |
| **HTTPS Forçado** | Comunicação criptografada por padrão |
| **WAF Integrado** | Cloudflare, AWS CloudFront e Azure Front Door incluem WAFs |

---

## 🔥 WAF (Web Application Firewall)

Um WAF inspeciona tráfego HTTP/HTTPS e bloqueia requisições maliciosas.

> Pense como um **segurança na porta de uma balada** — cada requisição é verificada antes de entrar.

### Tipos de WAF

| Tipo | Como é implantado | Melhor para |
|------|------------------|------------|
| **Cloud-based** | Fica na frente do servidor (proxy reverso) | Fácil de implantar, escalável |
| **Host-based** | Instalado diretamente no servidor web | Controle por aplicação |
| **Network-based** | Appliance físico/virtual no perímetro da rede | Ambientes corporativos |

### Métodos de detecção

| Método | Como funciona | Exemplo |
|--------|-------------|---------|
| **Baseado em Assinatura** | Compara com padrões de ataque conhecidos | Bloqueia requisições do `sqlmap/1.8.1` |
| **Heurístico** | Analisa contexto e comportamento | Detecta strings longas com caracteres especiais |
| **Anomalia/Comportamento** | Detecta desvios do tráfego normal | Um IP fazendo 1000 tentativas de login |
| **Reputação de IP** | Bloqueia IPs de listas de ameaças conhecidas | IPs de países fora da área de negócio |

---

## 🦠 Antivírus (AV)

AVs protegem principalmente **endpoints** (desktops, laptops, servidores) contra arquivos maliciosos conhecidos.

- Usa principalmente **detecção por assinatura**
- Importante mesmo em cenários de segurança web
- Detecta **uploads maliciosos** como web shells e ferramentas pós-exploração

**Exemplo de cenário:**
Um atacante encontra uma vulnerabilidade de upload e tenta enviar um web shell.
Mesmo que o WAF não detecte, o antivírus na máquina host escaneia o arquivo e o deleta antes da execução.

> AV é apenas **uma camada** — deve ser combinado com WAF, CDN, código seguro e logging.

---

## 🧠 Resumo dos conceitos-chave

| Conceito | Definição |
|---------|----------|
| **Mitigação** | Parar ou limitar danos causados por ameaças |
| **Patch Management** | Manter todos os softwares e componentes atualizados |
| **Defesa em Profundidade** | Múltiplas camadas de segurança para que nenhuma falha única comprometa tudo |
| **Menor Privilégio** | Serviços rodam com o mínimo de permissões necessárias |
| **Validação de Entrada** | Verificar input do usuário antes de processar no servidor |
| **WAF** | Firewall especializado em filtrar tráfego web |
| **CDN** | Rede de servidores de borda que cacheia conteúdo e provê segurança |
| **Log de Acesso** | Registro de cada requisição recebida pelo servidor web |

---

## ✅ Respostas da Sala

| Pergunta | Resposta |
|----------|---------|
| Apps migraram do desktop para a web? | Yea |
| Quem é responsável pela segurança dos dados do usuário? | The Web App Owner |
| O que o navegador envia para receber uma página? | A request |
| Servidor web mais comum para WordPress? | Apache |
| Conceito de parar/limitar danos de ameaças? | Mitigation |
| Controle que mantém softwares atualizados? | Patch Management |
| Tipo de WAF que roda no mesmo sistema do app? | Host-based |
| Detecção WAF que compara com padrões maliciosos conhecidos? | Signature-Based Detection |
