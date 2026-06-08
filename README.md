# dgs-ai-first

Repositório de entrega das atividades práticas da **Trilha de Certificação AI First — DGS**, programa de formação da DB1 Global Software para o novo SDLC AI First.

---

## Sobre a Trilha

A trilha prepara os papéis de engenharia da DGS — DMs, Product Specialists, Lead Engineers, Devs e QAs — para operar no modelo AI First. O período de formação vai de maio a junho de 2026, com 3 cenários práticos e uma prova de certificação ao final.

**Papel:** QA
**Participante:** [Seu nome]

---

## Estrutura do Repositório

```
dgs-ai-first/
├── main          → este README geral
├── cenario-1     → Fase de Entendimento e Contexto (entrega: 06/06)
├── cenario-2     → a definir (entrega: 18/06)
└── cenario-3     → a definir (entrega: 27/06)
```

---

## Cenários

### ✅ Cenário 1 — Fase de Entendimento e Contexto
**Branch:** `cenario-1` | **Prazo:** 06/06/2026

**Tópicos cobertos:**
- Fundamentos de IA Generativa
- Engenharia de Prompt
- Engenharia de Contexto
- RAG (Retrieval-Augmented Generation)

**Contexto base:** A NovaTech, empresa de logística, contratou a DB1 para construir um assistente de IA que permite ao time de atendimento consultar documentação interna em linguagem natural. O assistente responde perguntas sobre prazos, fretes, devoluções e SLAs com base em ~1.250 documentos distribuídos em SharePoint, Confluence e planilhas.

**Entregas (papel QA):**

| Arquivo | Descrição |
|---------|-----------|
| `exercicio-1.1-qa-cenarios-falha.md` | Identificação de cenários de falha de IA — lista com 14 cenários em Gherkin organizados em 5 categorias (alucinação, informação contraditória, falha de contexto, recusa inadequada, falha de guardrail) |
| `exercicio-1.2-qa-criterios-aceitacao.md` | Design de critérios de aceitação — rubrica com 4 dimensões, template reutilizável e avaliação das 5 respostas simuladas do assistente |
| `exercicio-1.3-qa-plano-testes-rag.md` | Plano de testes para pipeline de RAG — cobrindo ingestão, retrieval, geração, contexto, ponta a ponta e regressão |

**Ferramentas utilizadas:** Claude (chat)

---

### 🔜 Cenário 2 — a definir
**Branch:** `cenario-2` | **Prazo:** 18/06/2026

Conteúdo será liberado em breve pelo instrutor.

---

### 🔜 Cenário 3 — a definir
**Branch:** `cenario-3` | **Prazo:** 27/06/2026

Conteúdo será liberado em breve pelo instrutor.

---

## Ferramentas da Trilha

| Ferramenta | Uso |
|------------|-----|
| Claude (chat) | Todos os exercícios de QA |
| Claude Cowork | Organização de artefatos e templates |
| GitHub Copilot | Disponível para Devs e Tech Leads |

---

## Referências

- [Trilha de Formação — DGS AI First (PDF)](./Trilha_de_Formação_-_DGS_AI_First.pdf)
- [Planilha de Acompanhamento — DGS](https://db1global.sharepoint.com/:x:/s/engineers.it/IQCc4aV4AZvyQqB4ZadUwJakAV2sAJWhvxBI2dNkslH-rgE)
