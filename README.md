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
