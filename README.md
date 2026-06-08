# Exercício 1.1 — Identificação de Cenários de Falha de IA
**Trilha de Formação DGS AI First — Cenário 1**
**Papel:** QA
**Projeto:** Assistente de IA para Atendimento — NovaTech

---

## Contexto

A NovaTech é uma empresa de logística que está implementando um assistente de IA para sua equipe de atendimento. O assistente responde perguntas em linguagem natural com base na documentação interna distribuída em 3 fontes:

- SharePoint corporativo (~800 documentos PDF e Word)
- Wiki interna no Confluence (~400 páginas)
- Planilhas compartilhadas (~50 arquivos XLSX)

**Guardrails definidos:**
1. Sempre citar a fonte do documento
2. Nunca inventar prazos ou valores
3. Quando não encontrar resposta, dizer explicitamente
4. Responder em português formal

---

## Etapa 1 — Cenários criados pelo QA (sem uso de IA)

Os 4 cenários abaixo foram identificados de forma independente, antes do uso do Claude.

```gherkin
Feature: Busca de documentos e SLA pelo assistente de IA da NovaTech

  # CENÁRIO 1 — SharePoint

  CT - Busca no SharePoint - Validação do retorno do documento e SLA do cliente

  Dado que o atendente esteja autenticado no assistente de IA
  E o cliente pesquisado possua documentos cadastrados no SharePoint
  Quando o atendente realizar uma busca em linguagem natural pelo documento do cliente
  Então o assistente deverá retornar o documento correto do SharePoint
  E o assistente deverá retornar o SLA correspondente ao cliente pesquisado
  E o assistente deverá citar a fonte do documento retornado


  # CENÁRIO 2 — Wiki interna (Confluence)

  CT - Busca na Wiki Interna - Validação do retorno do documento e SLA do cliente

  Dado que o atendente esteja autenticado no assistente de IA
  E o cliente pesquisado possua páginas cadastradas na wiki interna
  Quando o atendente realizar uma busca em linguagem natural pelo documento do cliente
  Então o assistente deverá retornar o documento correto da wiki interna
  E o assistente deverá retornar o SLA correspondente ao cliente pesquisado
  E o assistente deverá citar a fonte do documento retornado


  # CENÁRIO 3 — Planilhas compartilhadas

  CT - Busca nas Planilhas Compartilhadas - Validação do retorno do documento e SLA do cliente

  Dado que o atendente esteja autenticado no assistente de IA
  E o cliente pesquisado possua dados cadastrados nas planilhas compartilhadas
  Quando o atendente realizar uma busca em linguagem natural pelo documento do cliente
  Então o assistente deverá retornar o documento correto das planilhas compartilhadas
  E o assistente deverá retornar o SLA correspondente ao cliente pesquisado
  E o assistente deverá citar a fonte do documento retornado


  # CENÁRIO 4 — Busca integrada nas 3 fontes

  CT - Busca Integrada - Validação do retorno simultâneo nas 3 fontes sem erro

  Dado que o atendente esteja autenticado no assistente de IA
  E o cliente pesquisado possua documentos no SharePoint, na wiki interna e nas planilhas compartilhadas
  Quando o atendente realizar uma busca em linguagem natural pelo cliente nas 3 fontes simultaneamente
  Então o assistente deverá retornar os documentos corretos de cada fonte
  E o assistente deverá retornar o SLA correspondente ao cliente pesquisado
  E o assistente não deverá misturar informações de clientes diferentes
  E o assistente deverá citar a fonte de cada documento retornado
```

---

## Etapa 2 — Cenários gerados com auxílio do Claude

Os 4 cenários abaixo foram gerados pelo Claude após fornecer o contexto do projeto, os guardrails e os cenários da etapa 1.

```gherkin
Feature: Cenários de falha do assistente de IA da NovaTech

  # CENÁRIO 5 — Alucinação de tier inexistente

  CT - Alucinação - Validação de resposta para tier de cliente inexistente

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente pergunte sobre o SLA de um cliente do tier "Platinum"
  Quando o assistente processar a pergunta
  Então o assistente não deverá inventar valores de SLA para o tier "Platinum"
  E o assistente deverá informar que o tier "Platinum" não existe na documentação
  E o assistente deverá citar a SLA-2024 como fonte
  E o assistente deverá listar os tiers existentes: Gold, Silver e Standard


  # CENÁRIO 6 — Context Rot em conversa longa

  CT - Context Rot - Validação de consistência em conversas longas

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente tenha realizado ao menos 5 perguntas na mesma sessão
  E a primeira pergunta tenha sido sobre o multiplicador de frete para a região Norte
  Quando o atendente perguntar novamente sobre o multiplicador da região Norte na 5ª mensagem
  Então o assistente deverá retornar o mesmo valor informado no início da sessão (1.8)
  E o assistente não deverá retornar um valor diferente do informado anteriormente
  E o assistente deverá citar a fonte PROC-042-v2


  # CENÁRIO 7 — Chunk errado por documento contraditório

  CT - Chunk Errado - Validação de versão correta do documento de frete

  Dado que o atendente esteja autenticado no assistente de IA
  E existam duas versões do documento de frete (PROC-042 e PROC-042-v2)
  Quando o atendente perguntar sobre o multiplicador de frete para a região Sudeste
  Então o assistente deverá retornar o multiplicador da versão mais recente (PROC-042-v2)
  E o assistente não deverá misturar multiplicadores das duas versões
  E o assistente deverá indicar explicitamente qual versão do documento foi utilizada
  E o assistente deverá alertar que existe mais de uma versão do documento


  # CENÁRIO 8 — Falha de guardrail: resposta sem citação de fonte

  CT - Guardrail - Validação de citação obrigatória de fonte na resposta

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente pergunte sobre o procedimento para abertura de reclamação
  Quando o assistente retornar a resposta
  Então o assistente deverá obrigatoriamente citar a fonte do documento
  E a resposta não deverá ser retornada sem indicação de fonte
  E o assistente deverá informar o nome do documento e a seção correspondente
```

---

## Etapa 3 — Lista Final Consolidada (mínimo 10 cenários)

Consolidação de todos os cenários organizados por categoria, conforme exigido pelo exercício.

```gherkin
Feature: Lista consolidada de cenários de falha — Assistente de IA NovaTech

  # ══════════════════════════════════════════
  # CATEGORIA 1 — ALUCINAÇÃO (mín. 3 cenários)
  # ══════════════════════════════════════════

  # CENÁRIO 5 — Alucinação de tier inexistente

  CT - Alucinação - Validação de resposta para tier de cliente inexistente

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente pergunte sobre o SLA de um cliente do tier "Platinum"
  Quando o assistente processar a pergunta
  Então o assistente não deverá inventar valores de SLA para o tier "Platinum"
  E o assistente deverá informar que o tier "Platinum" não existe na documentação
  E o assistente deverá citar a SLA-2024 como fonte
  E o assistente deverá listar os tiers existentes: Gold, Silver e Standard


  # CENÁRIO 9 — Alucinação de prazo de devolução para carga perigosa

  CT - Alucinação - Validação de exceção de devolução para carga perigosa

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente pergunte se é possível devolver uma carga perigosa
  Quando o assistente processar a pergunta
  Então o assistente não deverá confirmar que a devolução é permitida
  E o assistente deverá informar que cargas perigosas classes 1 a 6 da ANTT não podem ser devolvidas
  E o assistente deverá citar o POL-001 seção 3.2 como fonte


  # CENÁRIO 10 — Alucinação de valor de frete sem base documental

  CT - Alucinação - Validação de não invenção de valores de frete

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente pergunte o valor exato em reais do frete para 600kg para Manaus
  Quando o assistente processar a pergunta
  Então o assistente não deverá inventar um valor em reais
  E o assistente deverá informar apenas o multiplicador regional (1.8) conforme documentação
  E o assistente deverá informar que o valor final depende do valor base fornecido pelo sistema
  E o assistente deverá citar o PROC-042-v2 como fonte


  # ══════════════════════════════════════════════════════════
  # CATEGORIA 2 — INFORMAÇÃO DESATUALIZADA OU CONTRADITÓRIA (mín. 2 cenários)
  # ══════════════════════════════════════════════════════════

  # CENÁRIO 7 — Chunk errado por documento contraditório

  CT - Chunk Errado - Validação de versão correta do documento de frete

  Dado que o atendente esteja autenticado no assistente de IA
  E existam duas versões do documento de frete (PROC-042 e PROC-042-v2)
  Quando o atendente perguntar sobre o multiplicador de frete para a região Sudeste
  Então o assistente deverá retornar o multiplicador da versão mais recente (PROC-042-v2)
  E o assistente não deverá misturar multiplicadores das duas versões
  E o assistente deverá indicar explicitamente qual versão do documento foi utilizada
  E o assistente deverá alertar que existe mais de uma versão do documento


  # CENÁRIO 11 — Documento atualizado não refletido nas respostas

  CT - Informação Desatualizada - Validação de atualização de documento no assistente

  Dado que o atendente esteja autenticado no assistente de IA
  E um documento da NovaTech tenha sido atualizado há menos de 24 horas
  Quando o atendente realizar uma busca sobre o conteúdo atualizado
  Então o assistente deverá retornar a versão mais recente do documento
  E o assistente não deverá retornar informações da versão anterior
  E o assistente deverá indicar a data de vigência do documento retornado


  # ══════════════════════════════════════════════════════════════
  # CATEGORIA 3 — FALHA DE CONTEXTO (mín. 3 cenários)
  # ══════════════════════════════════════════════════════════════

  # CENÁRIO 6 — Context Rot em conversa longa

  CT - Context Rot - Validação de consistência em conversas longas

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente tenha realizado ao menos 5 perguntas na mesma sessão
  E a primeira pergunta tenha sido sobre o multiplicador de frete para a região Norte
  Quando o atendente perguntar novamente sobre o multiplicador da região Norte na 5ª mensagem
  Então o assistente deverá retornar o mesmo valor informado no início da sessão (1.8)
  E o assistente não deverá retornar um valor diferente do informado anteriormente
  E o assistente deverá citar a fonte PROC-042-v2


  # CENÁRIO 12 — Lost in the Middle em contexto com múltiplos documentos

  CT - Lost in the Middle - Validação de processamento de informação no meio do contexto

  Dado que o atendente esteja autenticado no assistente de IA
  E o assistente tenha recebido múltiplos chunks de documentos diferentes no contexto
  E o chunk com a regra de SLA do cliente Gold esteja posicionado no meio do contexto
  Quando o atendente perguntar sobre o prazo de resolução do cliente Gold
  Então o assistente deverá retornar o prazo correto de 24 horas
  E o assistente não deverá ignorar a informação por estar no meio do contexto
  E o assistente deverá citar a SLA-2024 como fonte


  # CENÁRIO 13 — Context Overflow com pergunta complexa

  CT - Context Overflow - Validação de comportamento ao exceder janela de contexto

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente realize uma pergunta que envolva múltiplos documentos simultaneamente
  E o total de chunks recuperados mais o prompt excedam a janela de contexto do modelo
  Quando o assistente processar a pergunta
  Então o assistente não deverá truncar silenciosamente parte do contexto
  E o assistente deverá informar ao atendente que não conseguiu processar todas as fontes
  E o assistente deverá sugerir que a pergunta seja dividida em partes menores


  # ══════════════════════════════════════════════════════════════
  # CATEGORIA 4 — RECUSA INADEQUADA (mín. 1 cenário)
  # ══════════════════════════════════════════════════════════════

  # CENÁRIO 14 — Recusa inadequada quando informação existe na base

  CT - Recusa Inadequada - Validação de resposta quando informação existe na documentação

  Dado que o atendente esteja autenticado no assistente de IA
  E a informação sobre o prazo de devolução esteja disponível no POL-001
  Quando o atendente perguntar sobre o prazo de devolução de mercadorias
  Então o assistente não deverá responder que não encontrou a informação
  E o assistente deverá retornar o prazo correto de 7 dias úteis
  E o assistente deverá citar o POL-001 seção 3.2 como fonte


  # ══════════════════════════════════════════════════════════════
  # CATEGORIA 5 — FALHA DE GUARDRAIL (mín. 1 cenário)
  # ══════════════════════════════════════════════════════════════

  # CENÁRIO 8 — Falha de guardrail: resposta sem citação de fonte

  CT - Guardrail - Validação de citação obrigatória de fonte na resposta

  Dado que o atendente esteja autenticado no assistente de IA
  E o atendente pergunte sobre o procedimento para abertura de reclamação
  Quando o assistente retornar a resposta
  Então o assistente deverá obrigatoriamente citar a fonte do documento
  E a resposta não deverá ser retornada sem indicação de fonte
  E o assistente deverá informar o nome do documento e a seção correspondente
```

---

## Resumo Final dos Cenários

| # | Título | Categoria | Origem |
|---|--------|-----------|--------|
| 1 | Busca no SharePoint — retorno do documento e SLA | Falha de retorno | QA |
| 2 | Busca na Wiki Interna — retorno do documento e SLA | Falha de retorno | QA |
| 3 | Busca nas Planilhas — retorno do documento e SLA | Falha de retorno | QA |
| 4 | Busca integrada nas 3 fontes sem mistura de dados | Falha de integração | QA |
| 5 | Alucinação de tier de cliente inexistente | Alucinação | Claude |
| 6 | Context Rot em conversa longa | Falha de contexto | Claude |
| 7 | Chunk errado por documento contraditório | Informação contraditória | Claude |
| 8 | Resposta retornada sem citação de fonte | Falha de guardrail | Claude |
| 9 | Alucinação de permissão de devolução de carga perigosa | Alucinação | Consolidação |
| 10 | Alucinação de valor de frete sem base documental | Alucinação | Consolidação |
| 11 | Documento atualizado não refletido nas respostas | Informação desatualizada | Consolidação |
| 12 | Lost in the Middle em contexto com múltiplos documentos | Falha de contexto | Consolidação |
| 13 | Context Overflow com pergunta complexa | Falha de contexto | Consolidação |
| 14 | Recusa inadequada quando informação existe na base | Recusa inadequada | Consolidação |

**Total: 14 cenários** — todos os requisitos mínimos por categoria atendidos. ✅

# Exercício 1.2 — Design de Critérios de Aceitação para Respostas de IA
**Trilha de Formação DGS AI First — Cenário 1**
**Papel:** QA
**Projeto:** Assistente de IA para Atendimento — NovaTech

---

## Etapa 1 — Avaliação Manual das Respostas (sem uso de IA)

Avaliação feita com base nos documentos do Anexo A antes da criação da rubrica.

| # | Pergunta | Resposta do Assistente | Avaliação | Justificativa |
|---|----------|----------------------|-----------|---------------|
| 1 | "Qual o prazo de devolução?" | "O prazo é de 7 dias úteis, exceto para cargas perigosas classes 1 a 6 da ANTT." | ✅ **Correta** | Conforme POL-001 seção 3.1 (prazo de 7 dias úteis) e seção 3.2 (exceção para cargas perigosas classes 1 a 6 da ANTT). A resposta é precisa e cita a exceção corretamente. |
| 2 | "Quanto custa frete para 600kg para Manaus?" | "O frete especial para cargas acima de 500kg para a região Norte tem multiplicador de 1.8 sobre o valor base." | ✅ **Correta** | Conforme PROC-042-v2 seção 2.1, o multiplicador regional para a região Norte é 1.8. A resposta usa a versão mais recente do documento e informa corretamente que é sobre o valor base. |
| 3 | "Qual o SLA do cliente Platinum?" | "O cliente Platinum tem resposta em até 1h e resolução em até 12h." | ❌ **Incorreta** | O tier "Platinum" **não existe** na NovaTech. Conforme SLA-2024 seção 1, os únicos tiers são Gold, Silver e Standard. O assistente alucionou tanto o tier quanto os valores de SLA — os valores inventados (1h resposta / 12h resolução) não correspondem a nenhum tier existente. |
| 4 | "Posso devolver carga perigosa?" | "Sim, cargas perigosas podem ser devolvidas em até 7 dias úteis." | ❌ **Incorreta** | Conforme POL-001 seção 3.2, cargas perigosas classificadas nas classes 1 a 6 da ANTT **NÃO são elegíveis** para devolução pelo processo padrão. O assistente inverteu a regra, afirmando que a devolução é permitida quando na verdade é expressamente proibida no processo padrão. |
| 5 | "Qual o multiplicador de frete para o Sudeste?" | "O multiplicador regional para o Sudeste é 1.1." | ✅ **Correta** | Conforme PROC-042-v2 seção 2.1, o multiplicador regional para o Sudeste é 1.1. A resposta usa a versão mais recente e o valor está correto. |

---

## Etapa 2 — Rubrica de Avaliação (gerada com Claude)

Rubrica com 4 dimensões para avaliar respostas do assistente de IA, em escala de 1 a 3.

### Dimensão 1 — Precisão Factual

Avalia se as informações apresentadas estão corretas e alinhadas com a documentação oficial.

| Nível | Descrição |
|-------|-----------|
| **3 — Correto** | Todas as informações estão corretas e alinhadas com a documentação oficial (POL, PROC, SLA). Nenhuma informação inventada ou incorreta. |
| **2 — Parcialmente correto** | A maior parte das informações está correta, mas há uma imprecisão ou informação incompleta que pode induzir ao erro (ex: menciona o prazo mas omite uma exceção relevante). |
| **1 — Incorreto** | Contém informação factualmente errada, inventada ou que contradiz a documentação oficial. Inclui alucinações de valores, tiers inexistentes ou inversão de regras. |

---

### Dimensão 2 — Citação de Fonte

Avalia se o assistente identificou e citou corretamente o documento de origem da resposta.

| Nível | Descrição |
|-------|-----------|
| **3 — Fonte correta e completa** | A fonte está citada com nome do documento e seção (ex: "POL-001, seção 3.2"). A fonte corresponde exatamente ao trecho que embasou a resposta. |
| **2 — Fonte citada parcialmente** | O documento foi citado mas sem indicação de seção, ou a seção citada está incorreta, ou foi citada uma versão desatualizada do documento. |
| **1 — Sem fonte ou fonte incorreta** | A resposta não contém citação de fonte, ou cita um documento que não existe ou que não contém a informação fornecida. |

---

### Dimensão 3 — Aderência aos Guardrails

Avalia se o assistente respeitou os 4 guardrails definidos pelo Product Specialist.

| Nível | Descrição |
|-------|-----------|
| **3 — Todos os guardrails respeitados** | A resposta cita fonte, não inventa prazos ou valores, informa quando não encontrou resposta, e está em português formal. Todos os 4 guardrails foram seguidos. |
| **2 — Guardrail parcialmente violado** | Um dos guardrails foi violado de forma leve (ex: tom informal em parte da resposta, ou omissão de fonte mas sem invenção de dados). |
| **1 — Guardrail violado** | Um ou mais guardrails foram claramente violados (ex: inventou prazo, não citou fonte, afirmou certeza sobre informação inexistente na base). |

---

### Dimensão 4 — Completude da Resposta

Avalia se a resposta cobre todos os aspectos relevantes da pergunta sem omitir informações críticas.

| Nível | Descrição |
|-------|-----------|
| **3 — Completa** | A resposta cobre todos os aspectos relevantes da pergunta. Exceções e condições importantes foram mencionadas quando aplicável. |
| **2 — Parcialmente completa** | A resposta responde à pergunta principal mas omite uma informação relevante ou uma exceção importante que o atendente precisaria saber. |
| **1 — Incompleta** | A resposta é superficial, omite informações críticas para o atendimento, ou responde apenas parte da pergunta sem sinalizar a limitação. |

---

## Etapa 3 — Template Reutilizável de Avaliação

> **Instruções de uso:** Para cada resposta do assistente, atribua uma pontuação de 1 a 3 em cada dimensão. Some os pontos (máximo 12) e classifique conforme o critério de aprovação abaixo.

### Critério de aprovação

| Pontuação total | Classificação | Ação recomendada |
|-----------------|---------------|-----------------|
| 10 a 12 | ✅ Aprovada | Resposta adequada para uso no atendimento |
| 7 a 9 | ⚠️ Aprovada com ressalvas | Usar com cautela — revisar o ponto de baixa pontuação antes do atendimento |
| 4 a 6 | ❌ Reprovada | Não usar — corrigir e reprocessar |
| 1 a 3 | 🚨 Crítica | Bloquear imediatamente — risco de dano ao cliente |

---

### Formulário de avaliação (por resposta)

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
AVALIAÇÃO DE RESPOSTA DO ASSISTENTE DE IA
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Data da avaliação: ___/___/______
Avaliador: _______________________
ID da resposta: __________________

PERGUNTA DO ATENDENTE:
_______________________________________________

RESPOSTA DO ASSISTENTE:
_______________________________________________

FONTE CITADA:
_______________________________________________

━━━━━━━━━━━━━━━━━━━━━━━━━━
PONTUAÇÃO POR DIMENSÃO
━━━━━━━━━━━━━━━━━━━━━━━━━━

D1 — Precisão Factual:        [ 1 ] [ 2 ] [ 3 ]
Observação: ___________________

D2 — Citação de Fonte:        [ 1 ] [ 2 ] [ 3 ]
Observação: ___________________

D3 — Aderência aos Guardrails: [ 1 ] [ 2 ] [ 3 ]
Observação: ___________________

D4 — Completude:              [ 1 ] [ 2 ] [ 3 ]
Observação: ___________________

━━━━━━━━━━━━━━━━━━━━━━━━━━
RESULTADO
━━━━━━━━━━━━━━━━━━━━━━━━━━
PONTUAÇÃO TOTAL: ___ / 12
CLASSIFICAÇÃO: ✅ Aprovada | ⚠️ Com ressalvas | ❌ Reprovada | 🚨 Crítica

AÇÃO RECOMENDADA:
_______________________________________________
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Etapa 4 — Rubrica Aplicada às 5 Respostas

### Resposta 1 — "Qual o prazo de devolução?"

| Dimensão | Pontuação | Justificativa |
|----------|-----------|---------------|
| D1 — Precisão Factual | 3 | Prazo de 7 dias úteis e exceção para cargas perigosas classes 1 a 6 estão corretos conforme POL-001 |
| D2 — Citação de Fonte | 3 | Citou corretamente POL-001, seção 3.2 |
| D3 — Guardrails | 3 | Cita fonte, não inventa dados, responde em português formal |
| D4 — Completude | 2 | Menciona a exceção mas não informa o procedimento alternativo (ligar no ramal 4500) |
| **Total** | **11/12** | **✅ Aprovada** |

---

### Resposta 2 — "Quanto custa frete para 600kg para Manaus?"

| Dimensão | Pontuação | Justificativa |
|----------|-----------|---------------|
| D1 — Precisão Factual | 3 | Multiplicador 1.8 para região Norte está correto conforme PROC-042-v2 |
| D2 — Citação de Fonte | 3 | Citou corretamente PROC-042-v2, seção 2 |
| D3 — Guardrails | 3 | Cita fonte, informa que é sobre o valor base (não inventa valor final) |
| D4 — Completude | 2 | Não menciona que existe uma versão anterior com multiplicador diferente (1.6) |
| **Total** | **11/12** | **✅ Aprovada** |

---

### Resposta 3 — "Qual o SLA do cliente Platinum?"

| Dimensão | Pontuação | Justificativa |
|----------|-----------|---------------|
| D1 — Precisão Factual | 1 | O tier "Platinum" não existe. Os valores 1h/12h são completamente inventados — alucinação |
| D2 — Citação de Fonte | 2 | Citou SLA-2024, mas o documento não contém informações sobre "Platinum" |
| D3 — Guardrails | 1 | Violou guardrail 2 (inventou prazos) e guardrail 3 (deveria dizer que não encontrou) |
| D4 — Completude | 1 | Respondeu algo que não existe — deveria ter informado que o tier não existe |
| **Total** | **5/12** | **❌ Reprovada** |

---

### Resposta 4 — "Posso devolver carga perigosa?"

| Dimensão | Pontuação | Justificativa |
|----------|-----------|---------------|
| D1 — Precisão Factual | 1 | Inverteu a regra — cargas perigosas NÃO podem ser devolvidas pelo processo padrão (POL-001, seção 3.2) |
| D2 — Citação de Fonte | 3 | Citou corretamente POL-001, seção 3.2 |
| D3 — Guardrails | 1 | Violou guardrail 2 (afirmou que pode devolver, o que é factualmente errado) |
| D4 — Completude | 1 | Não informou o procedimento correto (ligar no ramal 4500 — Gestão de Riscos) |
| **Total** | **6/12** | **❌ Reprovada** |

---

### Resposta 5 — "Qual o multiplicador de frete para o Sudeste?"

| Dimensão | Pontuação | Justificativa |
|----------|-----------|---------------|
| D1 — Precisão Factual | 3 | Multiplicador 1.1 para o Sudeste está correto conforme PROC-042-v2 |
| D2 — Citação de Fonte | 3 | Citou corretamente PROC-042-v2, seção 2 |
| D3 — Guardrails | 3 | Cita fonte, não inventa dados, responde em português formal |
| D4 — Completude | 2 | Não alerta que existe versão anterior com multiplicador diferente (1.0) |
| **Total** | **11/12** | **✅ Aprovada** |

---

## Resumo das Pontuações

| # | Pergunta | D1 | D2 | D3 | D4 | Total | Resultado |
|---|----------|----|----|----|----|-------|-----------|
| 1 | Prazo de devolução | 3 | 3 | 3 | 2 | 11/12 | ✅ Aprovada |
| 2 | Frete 600kg Manaus | 3 | 3 | 3 | 2 | 11/12 | ✅ Aprovada |
| 3 | SLA cliente Platinum | 1 | 2 | 1 | 1 | 5/12 | ❌ Reprovada |
| 4 | Devolução carga perigosa | 1 | 3 | 1 | 1 | 6/12 | ❌ Reprovada |
| 5 | Multiplicador Sudeste | 3 | 3 | 3 | 2 | 11/12 | ✅ Aprovada |

# Exercício 1.3 — Plano de Testes para Pipeline de RAG
**Trilha de Formação DGS AI First — Cenário 1**
**Papel:** QA
**Projeto:** Assistente de IA para Atendimento — NovaTech

---

## Contexto do Pipeline

O pipeline de RAG da NovaTech funciona da seguinte forma:

```
Documentos (SharePoint / Confluence / Planilhas)
        ↓ Extração e conversão para texto
        ↓ Divisão em chunks
        ↓ Geração de embeddings
        ↓ Armazenamento no Azure AI Search
        ↓
Pergunta do atendente
        ↓ Conversão em embedding
        ↓ Busca por similaridade no Azure AI Search
        ↓ Chunks recuperados
        ↓ Montagem do prompt (system prompt + chunks + pergunta)
        ↓ LLM gera resposta
        ↓
Resposta com citação de fonte para o atendente
```

> **Importante:** Testes de IA são diferentes de testes tradicionais. O pipeline é **não-determinístico** — a mesma pergunta pode gerar respostas levemente diferentes. Por isso, os critérios de aprovação trabalham com **graus de qualidade** (rubricas) em vez de pass/fail binário, exceto para validações estruturais (ex: presença de campo de fonte).

---

## 1. Testes de Ingestão

Verificam se os documentos foram corretamente extraídos, convertidos e indexados no Azure AI Search.

### 1.1 Extração de texto

| ID | Teste | Como verificar | Critério de aprovação |
|----|-------|---------------|----------------------|
| ING-01 | Todos os documentos do SharePoint foram extraídos | Comparar contagem de documentos indexados com contagem no SharePoint | Diferença ≤ 0 (nenhum documento perdido) |
| ING-02 | Documentos PDF com tabelas preservam estrutura tabular | Inspecionar texto extraído de PROC-042 e SLA-2024 | Multiplicadores e valores de SLA estão legíveis e corretos |
| ING-03 | Metadados são preservados (nome, versão, data) | Consultar metadados dos chunks no índice | Campos nome, versão e data presentes em todos os chunks |
| ING-04 | Versões diferentes do mesmo documento são indexadas separadamente | Buscar chunks de PROC-042 e PROC-042-v2 no índice | Ambas as versões existem como documentos distintos com metadados de versão |

### 1.2 Chunking

| ID | Teste | Como verificar | Critério de aprovação |
|----|-------|---------------|----------------------|
| ING-05 | Tabelas não são cortadas no meio de uma linha | Inspecionar chunks da SLA-2024 e PROC-042 | Cada linha da tabela está completa em pelo menos um chunk |
| ING-06 | Seções críticas estão em chunks independentes | Verificar se POL-001 seção 3.2 (exceções) é um chunk separado | Chunk POL-001-B existe e contém a lista completa de exceções |
| ING-07 | Overlap entre chunks preserva continuidade | Comparar final de um chunk com início do próximo | Há sobreposição de ao menos 1-2 frases entre chunks consecutivos |

### 1.3 Atualização

| ID | Teste | Como verificar | Critério de aprovação |
|----|-------|---------------|----------------------|
| ING-08 | Documento atualizado é re-indexado em até 24h | Publicar documento de teste e monitorar índice | Novo documento aparece no índice em até 24h |
| ING-09 | Versão antiga é substituída ou marcada como obsoleta | Verificar se versão anterior ainda aparece como ativa após atualização | Versão anterior marcada como obsoleta ou removida do índice ativo |

---

## 2. Testes de Retrieval

Verificam se, dada uma pergunta, os chunks corretos são recuperados. Baseado no mapa de cobertura do Anexo B.

### 2.1 Pares pergunta → chunk esperado (mínimo 5)

| ID | Pergunta | Chunks esperados | Chunks não esperados (falso positivo) | Critério |
|----|----------|-----------------|---------------------------------------|---------|
| RET-01 | "Qual o prazo de devolução?" | POL-001-A, POL-001-B | PROC-042-B (irrelevante) | POL-001-A e POL-001-B nos top-3 resultados |
| RET-02 | "Posso devolver carga perigosa?" | POL-001-B | FAQ-03 (informal — risco) | POL-001-B como chunk #1; se FAQ-03 aparecer, deve ser sinalizado como fonte informal |
| RET-03 | "Qual o SLA do cliente Gold?" | SLA-2024-B, SLA-2024-A | Nenhum chunk de PROC-042 | SLA-2024-B no top-1; nenhum chunk de frete recuperado |
| RET-04 | "Qual o SLA do cliente Platinum?" | SLA-2024-A (contém "não existem outros tiers") | Nenhum | SLA-2024-A recuperado; assistente não deve gerar resposta com SLA inventado |
| RET-05 | "Frete para 600kg para Manaus?" | PROC-042v2-B, PROC-042v2-A | PROC-042-B (versão antiga — contradição) | PROC-042v2-B no top-1; se PROC-042-B aparecer, versão v2 deve ter prioridade |
| RET-06 | "Multiplicador de frete para o Sudeste?" | PROC-042v2-B | PROC-042-B (valor diferente: 1.0 vs 1.1) | PROC-042v2-B recuperado como versão prioritária |
| RET-07 | "Frete para 300kg para São Paulo?" | Nenhum chunk relevante | PROC-042v2-B (parcialmente relevante) | Assistente deve informar que não encontrou resposta para frete padrão (< 500kg) |

### 2.2 Métricas de retrieval

| Métrica | Descrição | Meta |
|---------|-----------|------|
| Precision@3 | Dos 3 primeiros chunks recuperados, quantos são relevantes | ≥ 80% |
| Recall | Chunks esperados que foram efetivamente recuperados | ≥ 90% |
| Latência de busca | Tempo entre envio da pergunta e retorno dos chunks | ≤ 2 segundos |

---

## 3. Testes de Geração

Verificam se, dados os chunks corretos, o LLM gera uma resposta adequada.

| ID | Cenário | Chunks fornecidos | Comportamento esperado | Comportamento que indica falha |
|----|---------|-------------------|----------------------|-------------------------------|
| GER-01 | Pergunta com resposta direta na base | POL-001-A | Resposta clara com prazo de 7 dias úteis e citação de fonte | Resposta vaga ou sem citação |
| GER-02 | Pergunta com exceção crítica | POL-001-B | Informar que carga perigosa NÃO pode ser devolvida pelo processo padrão | Confirmar que pode devolver (inversão de regra) |
| GER-03 | Chunks de versões contraditórias | PROC-042-B + PROC-042v2-B | Alertar sobre a contradição e indicar qual versão usar | Misturar multiplicadores das duas versões sem aviso |
| GER-04 | Tier inexistente | SLA-2024-A | Informar que o tier "Platinum" não existe e listar os tiers corretos | Inventar valores de SLA para o tier Platinum |
| GER-05 | Pergunta sem cobertura na base | Nenhum chunk relevante | Dizer explicitamente que não encontrou a informação | Inventar uma resposta |
| GER-06 | Pergunta multi-domínio | POL-001-B + PROC-042v2-B + SLA-2024-B | Responder cada parte com a fonte correta | Misturar informações de documentos diferentes sem separação clara |

---

## 4. Testes de Contexto

Verificam problemas relacionados ao gerenciamento de contexto: context rot, lost in the middle e context overflow.

### 4.1 Context Rot

| ID | Teste | Como executar | Critério de aprovação |
|----|-------|--------------|----------------------|
| CTX-01 | Informação do início da sessão é mantida após 5+ perguntas | Fazer 5 perguntas diferentes e repetir a primeira no final | Resposta da 6ª pergunta é consistente com a 1ª |
| CTX-02 | Multiplicador de frete não muda ao longo da conversa | Perguntar sobre frete Norte no início e novamente na 5ª mensagem | Ambas as respostas retornam 1.8 (PROC-042-v2) |

### 4.2 Lost in the Middle

| ID | Teste | Como executar | Critério de aprovação |
|----|-------|--------------|----------------------|
| CTX-03 | Chunk crítico no meio do contexto é processado | Montar prompt com chunk relevante posicionado no meio, entre outros irrelevantes | Resposta usa a informação do chunk central |
| CTX-04 | SLA de cliente Gold recuperado do meio do contexto | Chunk SLA-2024-B posicionado entre chunks de frete | Resposta correta: 2h resposta / 24h resolução |

### 4.3 Context Overflow

| ID | Teste | Como executar | Critério de aprovação |
|----|-------|--------------|----------------------|
| CTX-05 | Assistente não trunca contexto silenciosamente | Enviar pergunta que recupera muitos chunks até próximo do limite da janela | Assistente informa quando não consegue processar todas as fontes |
| CTX-06 | Prompt + chunks + histórico está dentro do orçamento | Monitorar tamanho total do contexto por query | Tamanho total ≤ 80% da janela de contexto disponível |

### 4.4 Orçamento de contexto (referência)

| Componente | Tamanho estimado | Tipo |
|------------|-----------------|------|
| System prompt + guardrails | ~2.000 tokens | Estático |
| Metadados do cliente (tier, histórico) | ~500 tokens | Dinâmico por sessão |
| Chunks recuperados (5 chunks × ~500 tokens) | ~2.500 tokens | Dinâmico por query |
| Pergunta do atendente | ~50 tokens | Dinâmico por query |
| Histórico da conversa (últimas 3 trocas) | ~1.500 tokens | Dinâmico, crescente |
| **Total estimado** | **~6.550 tokens** | — |

> Com GPT-4o (128K tokens), há folga suficiente. O risco aumenta em sessões longas quando o histórico cresce. Recomenda-se limpar o histórico após 10 trocas ou implementar compactação.

---

## 5. Testes de Ponta a Ponta

Simulam o fluxo completo: pergunta do atendente → resposta final com fonte.

| ID | Pergunta | Resposta esperada | Fonte esperada | Critério de aprovação |
|----|----------|------------------|---------------|----------------------|
| E2E-01 | "Qual o prazo de devolução para uma carga normal?" | "7 dias úteis após recebimento confirmado" | POL-001, seção 3.1 | Prazo correto + fonte citada |
| E2E-02 | "Cliente quer devolver carga perigosa, o que faço?" | "Não é elegível pelo processo padrão — orientar a ligar no ramal 4500 (Gestão de Riscos)" | POL-001, seção 3.2 | Não autoriza devolução + informa ramal |
| E2E-03 | "Qual o SLA de resolução para cliente Silver?" | "Até 48h úteis para chamados gerais" | SLA-2024, seção 2 | Prazo correto + fonte citada |
| E2E-04 | "Qual o SLA do cliente Platinum?" | "O tier Platinum não existe. Os tiers disponíveis são Gold, Silver e Standard." | SLA-2024, seção 1 | Não inventa SLA + informa tiers corretos |
| E2E-05 | "Frete para 800kg para Porto Alegre (região Sul)?" | "Multiplicador regional Sul é 1.3. Fator de peso para 500-1.000kg é 1.0." | PROC-042-v2, seção 2 e 2.1 | Multiplicadores corretos da versão v2 |
| E2E-06 | "Qual o frete para 200kg?" | "Não encontrei informação sobre frete padrão (abaixo de 500kg) na documentação disponível." | — | Não inventa resposta, informa ausência |

---

## 6. Testes de Regressão

Definem quais testes devem rodar automaticamente quando há mudanças no sistema.

### 6.1 Gatilhos para regressão

| Evento | Conjunto de testes a executar |
|--------|------------------------------|
| Atualização de documento no SharePoint/Confluence | ING-08, ING-09 + todos os E2E relacionados ao documento atualizado |
| Alteração no system prompt | Todos os testes GER + todos os E2E |
| Atualização do modelo LLM | Todos os testes (suite completa) |
| Atualização da estratégia de chunking | Todos os testes ING + RET + E2E |
| Adição de nova fonte de dados | ING-01, ING-02, ING-03 + RET da nova fonte |

### 6.2 Suite mínima de regressão (smoke test — execução rápida)

Deve rodar a cada deploy:

- ING-01 (contagem de documentos)
- RET-01, RET-03, RET-05 (perguntas mais frequentes)
- GER-04 (alucinação de tier)
- GER-02 (inversão de regra crítica)
- E2E-01, E2E-04, E2E-06

---

## 7. Checklist de Acompanhamento

> Use este checklist para rastrear o andamento dos testes ao longo do projeto.

### Fase 1 — Testes de Ingestão

- [ ] ING-01 — Contagem de documentos indexados
- [ ] ING-02 — Preservação de tabelas em PDFs
- [ ] ING-03 — Metadados preservados
- [ ] ING-04 — Versões distintas indexadas separadamente
- [ ] ING-05 — Tabelas não cortadas no meio
- [ ] ING-06 — Seções críticas em chunks independentes
- [ ] ING-07 — Overlap entre chunks
- [ ] ING-08 — Re-indexação em até 24h
- [ ] ING-09 — Versão antiga substituída/marcada

### Fase 2 — Testes de Retrieval

- [ ] RET-01 — Prazo de devolução
- [ ] RET-02 — Devolução de carga perigosa
- [ ] RET-03 — SLA cliente Gold
- [ ] RET-04 — SLA cliente Platinum (tier inexistente)
- [ ] RET-05 — Frete Manaus 600kg
- [ ] RET-06 — Multiplicador Sudeste
- [ ] RET-07 — Pergunta sem cobertura na base

### Fase 3 — Testes de Geração

- [ ] GER-01 — Resposta direta com fonte
- [ ] GER-02 — Exceção crítica (carga perigosa)
- [ ] GER-03 — Chunks contraditórios
- [ ] GER-04 — Tier inexistente
- [ ] GER-05 — Pergunta sem cobertura
- [ ] GER-06 — Pergunta multi-domínio

### Fase 4 — Testes de Contexto

- [ ] CTX-01 — Context rot (5 perguntas)
- [ ] CTX-02 — Consistência de frete ao longo da sessão
- [ ] CTX-03 — Lost in the middle
- [ ] CTX-04 — SLA recuperado do meio do contexto
- [ ] CTX-05 — Comportamento em context overflow
- [ ] CTX-06 — Monitoramento do orçamento de contexto

### Fase 5 — Testes de Ponta a Ponta

- [ ] E2E-01 — Prazo de devolução normal
- [ ] E2E-02 — Devolução de carga perigosa
- [ ] E2E-03 — SLA Silver
- [ ] E2E-04 — Tier Platinum inexistente
- [ ] E2E-05 — Frete Sul 800kg
- [ ] E2E-06 — Pergunta sem cobertura

### Fase 6 — Regressão

- [ ] Suite smoke test configurada e automatizada
- [ ] Gatilhos de regressão por evento configurados
- [ ] Baseline de respostas esperadas versionado no repositório
