# 🏛️ Governança e Regulação

**Plataforma:** TryHackMe  
**Categoria:** Blue Team / GRC / Conformidade  
**Data:** 19/07/2026

---

## Visão Geral

Segurança da informação não é só sobre controles técnicos — ela também exige uma estrutura
organizada de governança e conformidade. Essa sala cobre os fundamentos de como organizações
gerenciam segurança através de políticas, regulamentos e frameworks como GRC, GDPR, NIST, ISO 27001 e SOC 2.

---

## 📌 Terminologias Principais

| Termo | Definição |
|-------|----------|
| **Governança** | Gerenciar e dirigir uma organização para atingir seus objetivos e garantir conformidade com leis e padrões |
| **Regulação** | Regra ou lei aplicada por um órgão regulador para garantir conformidade e proteger contra danos |
| **Conformidade (Compliance)** | Estado de aderência às leis, regulamentos e padrões que se aplicam a uma organização |

---

## 🏢 Governança de Segurança da Informação

Estrutura de políticas, métodos e diretrizes projetada para garantir a **confidencialidade,
integridade e disponibilidade** dos ativos de informação. Gerenciada pela alta administração e inclui:

- **Estratégia:** Desenvolver uma estratégia de segurança alinhada com os objetivos de negócio
- **Políticas e procedimentos:** Governar o uso e proteção dos ativos de informação
- **Gerenciamento de riscos:** Identificar ameaças e implementar medidas de mitigação
- **Medição de desempenho:** Estabelecer KPIs para medir a efetividade do programa
- **Conformidade:** Garantir aderência às regulamentações relevantes

---

## 📋 Frameworks de Segurança da Informação

| Componente | Descrição |
|-----------|----------|
| **Política** | Declaração formal que define metas e princípios da organização |
| **Padrão** | Documento estabelecendo requisitos específicos para um processo ou produto |
| **Diretriz** | Recomendações e melhores práticas (não obrigatórias) |
| **Procedimento** | Conjunto de passos específicos para realizar uma tarefa |
| **Baseline** | Padrões mínimos de segurança que a organização deve atender |

### Desenvolvendo Documentos de Governança — Etapas

1. **Identificar escopo e propósito** — o que o documento cobre e por quê
2. **Pesquisa e revisão** — leis, regulamentos e padrões relevantes
3. **Redigir o documento** — claro, conciso, acionável e alinhado com os objetivos
4. **Revisão e aprovação** — partes interessadas, equipes de compliance e gestão
5. **Implementação e comunicação** — treinar funcionários, definir responsabilidades
6. **Revisão e atualização** — monitorar conformidade e ajustar conforme necessário

---

## ⚖️ Framework GRC (Governança, Risco e Conformidade)

Abordagem holística que integra governança, gerenciamento de riscos e conformidade.

| Componente | Descrição |
|-----------|----------|
| **Governança** | Define a direção através de estratégia, políticas e monitoramento |
| **Gerenciamento de Riscos** | Identifica, avalia e prioriza riscos e implementa controles |
| **Conformidade** | Garante que obrigações legais, regulatórias e setoriais sejam atendidas |

### Desenvolvendo um Programa GRC — Etapas

1. Definir escopo e objetivos
2. Conduzir avaliação de riscos
3. Desenvolver políticas e procedimentos
4. Estabelecer processos de governança
5. Implementar controles (firewalls, IDS, IPS, SIEM, treinamentos)
6. Monitorar e medir desempenho
7. Melhoria contínua

### GRC no Setor Financeiro — Exemplo

| Atividade | Exemplos |
|----------|---------|
| **Governança** | Política antissonegação, relatórios financeiros, gestão de crises |
| **Gerenciamento de Riscos** | Fraudes financeiras, credenciais roubadas, cartões falsos |
| **Conformidade** | PCI-DSS, GLBA, SSL/TLS, gestão de patches, campanhas de phishing |

---

## 🌍 Principais Leis e Regulamentos

| Lei/Regulamento | Domínio | Descrição |
|----------------|--------|----------|
| **GDPR** | Privacidade de Dados | Regulamento da UE para proteção de dados pessoais de cidadãos europeus |
| **HIPAA** | Saúde | Lei dos EUA para proteção de informações de saúde |
| **PCI-DSS** | Financeiro | Manuseio seguro de dados de titulares de cartão (CHD) |
| **GLBA** | Financeiro | Protege informações pessoais não públicas (NPI) de clientes financeiros |

### GDPR — Detalhes Importantes

- Aplica-se a qualquer organização que processe dados pessoais de residentes da UE
- Exige **consentimento** antes de coletar dados
- Dados devem ser **mínimos** e **adequadamente protegidos**

**Níveis de penalidade:**

| Nível | Violações | Multa Máxima |
|-------|----------|-------------|
| **Nível 1** | Coleta não intencional, compartilhamento sem consentimento | 4% da receita anual OU €20 milhões (o que for maior) |
| **Nível 2** | Notificações de violação, políticas de segurança | 2% da receita anual OU €10 milhões (o que for maior) |

### PCI-DSS — Detalhes Importantes
- Criado por Visa, MasterCard e American Express
- **CHD** = Cardholder Data (Dados do Titular do Cartão)
- Exige controle de acesso rigoroso, WAFs, criptografia e monitoramento

---

## 🇺🇸 NIST 800-53

Publicação do Instituto Nacional de Padrões e Tecnologia dos EUA com catálogo de controles
de segurança organizados em **20 famílias de controles**.

| Controle | Categoria |
|---------|---------|
| Proteção de Mídia | Físico |
| Resposta a Incidentes | Administrativo |

### Melhores Práticas de Conformidade — Fases
1. **Descobrir** — identificar e catalogar ativos de dados e ameaças
2. **Mapear** — correlacionar ativos identificados com as famílias do 800-53
3. **Governar** — criar estrutura de governança e atribuir responsabilidades
4. **Monitorar** — avaliar conformidade e conduzir auditorias

### NIST 800-63B
Diretrizes para **identidade digital** — autenticar e verificar identidades de pessoas que acessam serviços digitais.
Cobre senhas, biometria, tokens e gerenciamento seguro de credenciais.

---

## 📊 ISO/IEC 27001

Padrão internacional para um **Sistema de Gestão de Segurança da Informação (SGSI)**.
Desenvolvido pela ISO e IEC.

### Componentes Principais

| Componente | Descrição |
|-----------|----------|
| **Escopo** | Limites do SGSI — ativos e processos cobertos |
| **Política de segurança da informação** | Documento de alto nível definindo a abordagem de segurança |
| **Avaliação de riscos** | Identificar e avaliar riscos à tríade CIA |
| **Tratamento de riscos** | Selecionar e implementar controles para reduzir riscos identificados |
| **Declaração de Aplicabilidade (SoA)** | Especifica quais controles são aplicáveis |
| **Auditoria interna** | Auditorias periódicas para garantir que o SGSI opera efetivamente |
| **Revisão da gestão** | Revisão regular do desempenho do SGSI |

---

## 🔍 SOC 2 (Service Organization Control 2)

Desenvolvido pela **AICPA** — avalia a efetividade dos controles de segurança de dados de uma empresa.
Baseado na **tríade CIA** mais **Privacidade**.

### Quando é usado?
Provedores de serviços que interagem com dados de clientes — empresas de nuvem, SaaS, etc.

### Planejando uma Auditoria SOC 2 — Etapas
1. Determinar o escopo
2. Escolher um auditor qualificado
3. Planejar cronograma e critérios da auditoria
4. Preparar — revisar controles, identificar lacunas
5. Conduzir a auditoria — entrevistas, revisão de documentos, testes de controles
6. Receber o relatório com descobertas e recomendações

### Controles Genéricos do SOC 2

| Controle | O que verifica |
|---------|--------------|
| **Segurança** | Proteção contra acesso não autorizado |
| **Disponibilidade** | Sistema permanece disponível conforme acordado |
| **Confidencialidade** | Dados protegidos contra divulgação não autorizada |
| **Integridade do processamento** | Processamento é completo, válido e pontual |
| **Privacidade** | Informações pessoais são tratadas adequadamente |

---

## 🧠 Resumo dos Conceitos-Chave

| Conceito | Definição |
|---------|----------|
| **Regulação** | Regra aplicada por um órgão regulador |
| **GRC** | Governança, Risco e Conformidade — framework integrado |
| **GDPR** | Lei de proteção de dados da UE — multa máxima 4% receita (Nível 1) |
| **HIPAA** | Proteção de dados de saúde nos EUA |
| **PCI-DSS** | Segurança de dados de pagamento — CHD = Dados do Titular do Cartão |
| **NIST 800-53** | Catálogo de controles de segurança dos EUA — 20 famílias |
| **ISO 27001** | Padrão internacional de SGSI |
| **SOC 2** | Framework de auditoria para organizações de serviços (AICPA) |
| **Tratamento de riscos** | Componente ISO 27001 para implementar controles que reduzem riscos |
| **Disponibilidade** | Controle SOC 2 que mostra que o sistema permanece disponível |

---

## ✅ Respostas da Sala

| Pergunta | Resposta |
|----------|---------|
| Uma regra aplicada por um órgão regulador é chamada de? | Regulation |
| HIPAA tem como alvo qual domínio? | Healthcare |
| A etapa que envolve monitorar conformidade e ajustar com base em feedback? | Review and update |
| Um conjunto de passos específicos para realizar uma tarefa é chamado de? | Procedures |
| Componente GRC para identificar e priorizar riscos? | Risk Management |
| É importante monitorar o desempenho de uma política? | Yea |
| Multa máxima GDPR Nível 1 (percentual)? | 4% |
| No PCI-DSS, CHD significa? | Cardholder Data |
| NIST 800-53 — Proteção de Mídia fica em qual categoria? | Physical |
| NIST 800-53 — Resposta a Incidentes fica em qual categoria? | Administrative |
| Fase NIST que correlaciona ativos e permissões identificados? | Map |
| Componente ISO 27001 para implementar controles que reduzem riscos? | Risk treatment |
| Controle SOC 2 que mostra que o sistema permanece disponível? | Availability |
