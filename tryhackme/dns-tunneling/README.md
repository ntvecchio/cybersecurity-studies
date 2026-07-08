# 🌐 DNS in Detail — DNS Tunneling

**Plataforma:** TryHackMe  
**Categoria:** Network Security / Threat Intelligence  
**Data:** julho/2026

---

## O que é DNS?
DNS (Domain Name System) funciona como a lista de contatos da internet.
Quando você digita `google.com`, seu computador pergunta ao servidor DNS:
"Qual é o IP desse domínio?" e o servidor responde com o endereço.

---

## Tipos de registros DNS

| Tipo | Função |
|------|--------|
| **A** | Mapeia domínio para IP (IPv4) |
| **AAAA** | Mapeia domínio para IP (IPv6) |
| **MX** | Servidor de e-mail do domínio |
| **CNAME** | Alias — aponta um domínio para outro |
| **TXT** | Armazena texto livre — usado para verificações e, em ataques, para esconder dados |
| **PTR** | Resolução reversa (IP → domínio) |

---

## 🚨 DNS Tunneling (Tunelamento DNS)

### O que é?
Técnica usada por atacantes para **camuflar comunicação C2 dentro do protocolo DNS**,
aproveitando que a maioria dos firewalls libera tráfego DNS livremente.

### Por que funciona?
O DNS é essencial para a internet. Bloquear DNS quebraria a navegação inteira,
então empresas geralmente deixam esse tráfego passar sem inspeção profunda.

---

## O ataque na prática

### Cenário real investigado

**Host comprometido:** `WIN-016`

O malware instalado no `WIN-016` precisava receber comandos do servidor C2 do atacante.
Em vez de conectar diretamente (o que o firewall bloquearia), ele usou DNS Tunneling:

**1. Requisições para subdomínios aleatórios:**
```
aj39skdm.malicious-tld.com
xk72pqrs.malicious-tld.com
```
Para o firewall, parece apenas navegação normal.

**2. O atacante responde com registro TXT codificado:**
```
TXT: "SSBsb3ZlIHlvdXIgY3VyaW91c2l0eQ=="
```
Parece uma string aleatória, mas é um comando escondido em **Base64**.

**3. Decodificando no CyberChef:**
```
SSBsb3ZlIHlvdXIgY3VyaW91c2l0eQ==  →  "I love your curiosity"
```
Em um ataque real, o conteúdo seria algo como `delete_system_files` ou `download_payload`.

---

## Por que o registro TXT é usado?

O tipo TXT foi criado para armazenar informações textuais de configuração de domínios
(como verificação do Google, SPF de e-mail, etc.). Por aceitar texto livre,
os atacantes abusam dele para esconder comandos codificados.

---

## Como detectar DNS Tunneling?

| Indicador | O que observar |
|-----------|---------------|
| Volume alto de requisições DNS | Muitas consultas em curto período |
| Subdomínios aleatórios longos | `aj39skdm.dominio.com` — não é nome legível |
| Registros TXT incomuns | Respostas com Base64 ou conteúdo codificado |
| Domínio de destino desconhecido | Não está na whitelist da empresa |
| Padrão de beaconing | Intervalos regulares de consulta |

---

## Como investigar no Splunk

```spl
# Ver todas as consultas DNS de um host
index=main sourcetype=dns host="WIN-016"

# Filtrar por tipo de registro TXT
index=main sourcetype=dns record_type=TXT

# Top domínios consultados
index=main sourcetype=dns | stats count by query | sort -count
```

---

## Conceitos aprendidos

### Base64 no contexto de DNS Tunneling
Atacantes codificam os comandos em Base64 para:
- Evitar caracteres especiais que quebrariam o protocolo DNS
- Dificultar a detecção por regras simples de firewall
- Passar como texto "normal" em registros TXT

Para decodificar: **CyberChef** → operação "From Base64"

### C2 (Command and Control)
Canal de comunicação entre o atacante e a máquina comprometida.
O DNS Tunneling é uma das formas mais furtivas de manter esse canal ativo,
pois usa um protocolo que raramente é bloqueado ou inspecionado.

### Relação com a Unified Kill Chain
O DNS Tunneling aparece na fase **Command & Control (C2)** —
o atacante mantém comunicação com o host comprometido sem levantar alertas.
