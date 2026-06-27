# ⛓️ Unified Kill Chain (UKC)

**Plataforma:** TryHackMe  
**Categoria:** Threat Intelligence / Framework  
**Conclusão:** junho/2026  
**Progresso:** ✅ 100%

---

## O que é a Unified Kill Chain?
Criada por **Paul Pols** em 2017, a UKC é uma evolução do Cyber Kill Chain da Lockheed Martin.
Enquanto o modelo original tem 7 fases focadas no ataque externo, a UKC tem **18 fases**
cobrindo o ciclo completo de um ataque — desde o reconhecimento até o impacto final.

## Comparação com o Cyber Kill Chain

| | Cyber Kill Chain | Unified Kill Chain |
|---|---|---|
| Criado por | Lockheed Martin | Paul Pols |
| Ano | 2011 | 2017 |
| Fases | 7 | 18 |
| Foco | Ataque externo | Ataque completo |
| Cobre movimento lateral? | Não | Sim |

---

## As 3 grandes etapas

A UKC organiza as 18 fases em 3 ciclos:

```
CYCLE 1: IN        → Como o atacante ganha acesso inicial
CYCLE 2: THROUGH   → Como o atacante se move dentro da rede
CYCLE 3: OUT       → Como o atacante atinge o objetivo final
```

---

## 🔴 CYCLE 1 — IN (Ganhar Acesso)

O atacante ainda está fora da organização e tentando entrar.

### 1. Reconnaissance (Reconhecimento)
Coleta de informações públicas sobre o alvo antes do ataque.
- OSINT: LinkedIn, site da empresa, registros DNS
- Ferramentas: Shodan, Maltego, nmap

### 2. Weaponization (Armamento)
Criação do payload/exploit que será usado no ataque.
- Documento malicioso com macro
- Exploit para vulnerabilidade conhecida

### 3. Social Engineering
Manipulação psicológica de pessoas para obter acesso ou informações.
- Phishing por e-mail
- Pretexting (se passar por outra pessoa)

### 4. Exploitation (Exploração)
Execução do exploit na máquina da vítima para obter acesso inicial.

### 5. Persistence (Persistência)
O atacante garante que vai continuar com acesso mesmo após reinicializações.
- Backdoors
- Tarefas agendadas maliciosas
- Modificação de chaves de registro

### 6. Defense Evasion (Evasão de defesa)
Técnicas para não ser detectado por antivírus, EDR e outras ferramentas.
- Ofuscação de código
- Desabilitar logs
- Usar ferramentas legítimas do sistema (Living off the Land)

### 7. Command & Control (C2)
**O atacante cria uma conexão entre o computador invadido e o dele.**
Isso permite controlar a máquina remotamente de qualquer lugar.
- Canal de comunicação criptografado
- Pode usar protocolos comuns (HTTP, DNS) para se camuflar
- Ferramentas: Cobalt Strike, Metasploit

---

## 🟡 CYCLE 2 — THROUGH (Movimento pela rede)

O atacante já está dentro e agora quer se aprofundar.

### 8. Pivoting
Usar a máquina comprometida como trampolim para acessar outras partes da rede
que não seriam acessíveis diretamente de fora.

### 9. Discovery (Descoberta)
**O atacante procura dados e recursos importantes dentro da rede.**
- Mapeamento de arquivos e diretórios
- Identificação de outros hosts na rede
- Busca por credenciais salvas

### 10. Privilege Escalation (Escalada de privilégios)
**Buscar escalation** — o atacante tenta obter permissões maiores do que as que tem.
- De usuário comum → administrador local
- De administrador local → administrador de domínio (Domain Admin)
- Exploração de vulnerabilidades do SO

### 11. Execution (Execução)
Rodar scripts e payloads para avançar no ataque.

### 12. Credential Access (Acesso a credenciais)
Roubo de senhas e hashes para se autenticar em outros sistemas.
- Ferramentas: Mimikatz
- Técnicas: Pass-the-Hash, Kerberoasting

### 13. Lateral Movement (Movimento lateral)
**Se disfarçar como um funcionário para procurar arquivos não só no computador
invadido, mas em todo o sistema da empresa.**
- Usar credenciais roubadas para acessar outros computadores
- Mover-se pela rede sem levantar suspeitas
- Ferramentas: PsExec, WMI, RDP

---

## 🔵 CYCLE 3 — OUT (Atingir o objetivo)

O atacante já tem acesso amplo e agora executa o objetivo final.

### 14. Collection (Coleta)
**Procurar e reunir os dados importantes encontrados.**
- Documentos confidenciais
- Banco de dados de clientes
- Propriedade intelectual

### 15. Exfiltration (Exfiltração)
**Comprimir os dados (ex: .zip) e enviar para o computador do atacante.**
- Compressão para reduzir tamanho e dificultar detecção
- Envio via C2, FTP, cloud storage, DNS tunneling
- Pode ser feito aos poucos para não gerar alertas de volume

### 16. Impact (Impacto)
**O atacante pode causar danos diretos ao sistema.**
- Deletar ou corromper arquivos
- Derrubar serviços
- **Instalar ransomware** — criptografa os dados e exige resgate

### 17. Objectives (Objetivos)
O objetivo final do ataque, que pode ser:
- 💰 **Financeiro**: vender dados, pedir resgate (ransomware)
- 🕵️ **Espionagem**: roubar segredos industriais ou governamentais
- 🎭 **Hacktivismo**: protestar ou expor informações
- 💣 **Sabotagem**: destruir sistemas de um concorrente ou inimigo

### 18. Confidentiality / CIA Triad Impact
O atacante pode violar qualquer pilar da CIA Triad:
- **Confidencialidade**: dados vazados → impacto reputacional e legal
- **Integridade**: dados alterados → decisões baseadas em informações falsas
- **Disponibilidade**: sistemas derrubados → prejuízo operacional

---

## 💡 Por que a UKC é melhor que o Cyber Kill Chain original?

1. **Cobre o movimento lateral** — o que acontece depois que o atacante entrou
2. **Mais realista** — ataques modernos raramente são lineares; o atacante pode pular etapas ou voltar atrás
3. **Ajuda defensores** — cada fase é uma oportunidade de detectar e interromper o ataque
4. **Integra com MITRE ATT&CK** — as técnicas se mapeiam diretamente no framework

---

## Resumo visual

```
[FORA]                    [DENTRO]                   [OBJETIVO]
Recon → Exploit → C2  →  Escalation → Lateral  →  Coleta → Exfil → Impacto
                          Movement
     CYCLE 1 (IN)           CYCLE 2 (THROUGH)         CYCLE 3 (OUT)
```
