# 🔬 Network Traffic Analysis

**Plataforma:** TryHackMe  
**Categoria:** Blue Team / Network Security  
**Data:** julho/2026

---

## O que é análise de tráfego de rede?
É o processo de capturar e inspecionar os pacotes que trafegam pela rede
para detectar comportamentos maliciosos que não aparecem em logs comuns.

---

## Por que logs simples não são suficientes?

Logs normais registram apenas metadados:
- Quem conectou em quem
- Quando aconteceu
- Qual protocolo foi usado

**O problema:** invasores escondem dados maliciosos dentro de comunicações
que parecem normais. Para detectar isso, é preciso abrir o pacote e olhar
o conteúdo completo.

| O que os logs mostram | O que a análise de tráfego revela |
|----------------------|----------------------------------|
| `WIN-016` consultou `malicious-tld.com` | O conteúdo da resposta DNS continha um comando codificado em Base64 |
| Requisição DNS normal | Dados exfiltrados escondidos dentro da resposta |
| Tráfego aparentemente legítimo | Comunicação C2 disfarçada |

---

## Protocolos importantes para entender

| Protocolo | Função |
|-----------|--------|
| **DNS** | Resolve nomes de domínio para IPs |
| **HTTP/HTTPS** | Transferência de páginas web |
| **TCP** | Comunicação confiável, com confirmação de entrega |
| **UDP** | Comunicação rápida, sem confirmação (usado pelo DNS) |

---

## Ferramentas de análise de tráfego

- **Wireshark** — captura e analisa pacotes em tempo real (GUI)
- **tcpdump** — captura pacotes via linha de comando
- **Zeek** — monitoramento de rede e geração de logs detalhados
- **Splunk / ELK** — correlação e visualização de eventos de rede

---

## Conceitos aprendidos

### O que é um pacote de rede?
Cada comunicação na internet é dividida em pequenos pedaços chamados pacotes.
Cada pacote contém cabeçalho (metadados) e payload (conteúdo real).
A análise de tráfego abre o payload para ver o que está sendo transferido.

### Por que isso importa no SOC?
Um analista que só olha logs pode deixar passar um ataque inteiro.
A análise de pacotes revela o que realmente está sendo comunicado —
comandos, dados exfiltrados, payloads escondidos.
