# Exercícios 3.1 e 3.2 — Fase de Governança e Validação
**Trilha de Formação DGS AI First — Cenário 3**
**Papel:** QA
**Projeto:** Assistente de IA para Atendimento — NovaTech (`novatech-assistant`)

---

## Contexto

O assistente está em staging, acessível por 5 atendentes-piloto. 12% das respostas em teste interno estavam incorretas (alucinação, documento desatualizado, chunk errado). Não há structured output garantindo campos obrigatórios (fonte, confiança). Testes de integração cobrem ~75% do código. Demo para a diretoria em 2 semanas. Antes do go-live, o QA precisa: (1) aplicar revisão crítica a uma amostra de respostas do assistente com a rubrica criada no cenário 2, e (2) revisar se os testes de integração gerados por IA realmente testam o que deveriam.

---

## Exercício 3.1 — Revisão crítica das respostas do assistente

### Rubrica aplicada (4 dimensões, 1–3 cada, do cenário 2)

**D1** — Precisão Factual · **D2** — Citação de Fonte · **D3** — Aderência a Guardrails · **D4** — Completude

### Avaliação — 8 respostas em staging

| # | Pergunta | D1 | D2 | D3 | D4 | Total | Resultado | Justificativa |
|---|---|---|---|---|---|---|---|---|
| 1 | Prazo devolução | 3 | 3 | 3 | 3 | 12/12 | ✅ Aprovada | 7 dias + exceção de carga perigosa, ambos corretos e citados (POL-001) |
| 2 | Devolução carga perigosa | 3 | 3 | 3 | 2 | 11/12 | ✅ Aprovada | Correto e cita fonte; completude em 2 — não menciona o canal específico (ramal 4500, Gestão de Riscos), só "escalar supervisor" |
| 3 | SLA Gold resolução | 3 | 3 | 3 | 3 | 12/12 | ✅ Aprovada | 24h correto para Gold (chamados gerais), fonte citada |
| 4 | SLA Platinum | 3 | 2 | 3 | 2 | 10/12 | ✅ Aprovada (com ressalva) | Reconhece que o tier não existe (não alucina) — mas não cita a SLA-2024 como fonte e não lista os tiers válidos (Gold/Silver/Standard) |
| 5 | Frete 600kg Manaus | 3 | 3 | 3 | 3 | 12/12 | ✅ Aprovada | 1.8 é o multiplicador Norte vigente (PROC-042-v2), fonte correta |
| 6 | Frete 600kg sem destino | 1 | 2 | 1 | 1 | 5/12 | ❌ **Reprovada** | Assumiu "Sudeste" sem o atendente informar o destino — alucinação de dado de entrada, não de conteúdo documental |
| 7 | Receita de bolo | 3 | 3 | 3 | 3 | 12/12 | ✅ Aprovada | Recusa apropriada por escopo, sem inventar nem fingir que sabe |
| 8 | "What is the return policy?" | 3 | 3 | 1 | 2 | 9/12 | ❌ **Reprovada** | Guardrail de idioma (português formal) violado — respondeu em inglês |

**Segunda avaliação (Claude, cruzada com a análise acima):** confirma as mesmas 2 reprovações (#6 e #8) e os mesmos scores nas demais. Ponto de atenção adicional identificado na segunda passada: a resposta #4 (SLA Platinum), embora aprovada, é o tipo de resposta que se beneficiaria de structured output — se `source_document` fosse um campo obrigatório do schema, a ausência de citação já teria sido pega automaticamente antes de chegar ao atendente, sem depender de revisão manual.

### Relatório de qualidade (consolidado)

| Métrica | Valor |
|---|---|
| Respostas avaliadas | 8 |
| Aprovadas | 6 (75%) |
| Reprovadas | 2 (25%) — #6 e #8 |
| Score médio (0–12) | 10,4 |
| Categoria das reprovações | Assunção de dado não fornecido (#6); violação de guardrail de idioma (#8) |

**Parecer de go-live:** Não recomendo ir ao ar sem correção. As duas reprovações não são ruído estatístico — são as duas falhas mais perigosas para um assistente de atendimento: (a) preencher lacuna de informação que o usuário não deu, com aparência de certeza; (b) ignorar um guardrail de comunicação de forma sistemática, não aleatória. Condição para aprovar o go-live:
1. Adicionar verificação (idealmente via structured output) que rejeita respostas de frete sem `região` explícita na pergunta ou no contexto da sessão — o modelo deve perguntar de volta, não assumir.
2. Adicionar verificação de idioma na camada de guardrails (regra determinística, não apenas instrução de prompt) antes de retornar a resposta ao atendente.
3. As demais 6 respostas (75%) sustentam ir a piloto controlado após as duas correções acima — não bloqueiam o go-live por si só.

---

## Exercício 3.2 — Revisão crítica dos testes gerados por IA

### Avaliação dos 3 testes

| Teste | O que testa | O que falha em testar | Risco se "passar" com código errado |
|---|---|---|---|
| **1 — assertions vagas** | Que o endpoint responde 200 com corpo não-vazio | Não verifica nenhum conteúdo — não checa prazo, fonte, ou relevância da resposta | Endpoint pode retornar qualquer alucinação e o teste passa, porque `toBeDefined()` só confirma existência, não correção |
| **2 — dados irreais** | Validação de input vazio (400) — legítimo como edge case | Nenhum teste da suíte usa uma pergunta real de logística (carga perigosa, SLA, frete) | Lógica de busca/geração pode estar quebrada e nenhum teste do arquivo detecta, porque nenhum exercita o caminho principal com dado de domínio |
| **3 — mock que mascara bug** | Que o endpoint de feedback retorna 200 | `mockCreate` nunca é conectado à implementação real — a assertion `toHaveBeenCalled()` verifica uma função que o código de produção nem chama | Se a validação de input do feedback fosse removida, o teste continuaria passando — falsa segurança de que o feedback é validado e persistido |

**Ponto de atenção (inconsistência de contexto):** os 3 testes usam `jest` (`jest.fn()`, sintaxe supertest), mas o `AGENTS.md`/Anexo C do projeto definem **Vitest**. Isso não é só estilo — sem os globals do Jest habilitados, esses testes provavelmente nem executam no CI configurado para Vitest. É um sinal de que o agente gerou testes com o framework mais comum em seu treinamento, não o que o projeto de fato usa — e ninguém confirmou isso antes do merge.

**Segunda avaliação (Claude, cruzada):** confirma os 3 problemas e a inconsistência jest/Vitest. Adição da segunda passada: o Teste 3 é o mais perigoso dos três — os testes 1 e 2 pelo menos falham em cobrir algo (visível como lacuna), mas o Teste 3 ativamente engana quem lê o relatório de cobertura, porque parece testar persistência e validação quando não testa nenhuma das duas.

### Teste 1 — reescrito (Vitest + msw, verificando conteúdo)

```typescript
import { describe, it, expect, beforeAll, afterAll, afterEach } from 'vitest';
import { setupServer } from 'msw/node';
import { queryHandler } from '../../../src/functions/query/handler';
import { mockSearchReturnsChunk, mockCompletionAnswersWith } from '../../fixtures/handlers';
import { buildQueryRequest } from '../../fixtures/queries';

const server = setupServer();
beforeAll(() => server.listen({ onUnhandledRequest: 'error' }));
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

describe('queryHandler', () => {
  it('should return the correct return deadline and cite POL-001 when asked about standard return policy', async () => {
    // Arrange
    server.use(
      mockSearchReturnsChunk('POL-001-A'),
      mockCompletionAnswersWith('POL-001-A'),
    );
    const request = buildQueryRequest({ question: 'Qual o prazo de devolução para produtos standard?' });

    // Act
    const result = await queryHandler(request);

    // Assert
    expect(result.status).toBe(200);
    expect(result.body.answer).toMatch(/7\s*dias/);
    expect(result.body.source_document).toBe('POL-001');
  });
});
```

**Melhorias aplicadas:** framework corrigido para Vitest (consistente com o AGENTS.md); dependências externas isoladas via `msw` em vez de chamar a app real; assertions verificam o valor de negócio (prazo de 7 dias) e a fonte citada, não apenas a existência de uma resposta.

### Comparação — avaliação própria vs. Claude

| Ponto | Avaliação própria | Claude (2ª passada) | Convergência |
|---|---|---|---|
| Teste 1 insuficiente | Sim | Sim | Total |
| Teste 2 incompleto (sem dado de domínio) | Sim | Sim | Total |
| Teste 3 perigoso (mock mascara bug) | Sim | Sim, com nota adicional de que é o mais grave dos 3 | Parcial — Claude aprofundou a priorização de risco |
| Inconsistência jest/Vitest | Identificada | Confirmada | Total |
