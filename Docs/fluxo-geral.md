
---

## 📄 3. `docs/fluxo-geral.md`

Crie o arquivo `docs/fluxo-geral.md`:

```md
# Fluxo Geral – Assistente Imobiliária com n8n

Este documento descreve, de forma simplificada, cada parte do fluxo principal usado para atendimento via WhatsApp.

---

## 1. WhatsApp Trigger

- Node que recebe a mensagem do cliente vinda da **WhatsApp Cloud API**.
- Saída típica:
  - `messages[0].from` → telefone
  - `messages[0].text.body` → mensagem
  - `contacts[0].profile.name` → nome do perfil do WhatsApp

---

## 2. IA – Nina (Atendente Virtual)

- Node de IA (Message a Model / AI Agent).
- Prompt configurado para:
  - Atender como **Nina, atendente virtual de uma imobiliária**.
  - Responder em português, de forma curta e humana.
  - Fazer **sempre 1 pergunta** para avançar a qualificação.
  - Coletar: compra/aluguel, cidade/bairro, tipo, orçamento, prazo.
  - Encerrar com a tag `[FIM_DO_ATENDIMENTO]` quando tiver tudo.

---

## 3. Node de Qualificação de Lead (JS, sem IA)

- Node Function que:
  - Lê o histórico da conversa (`history`).
  - Define:
    - `resumo`
    - `temperatura` (`frio`, `morno`, `quente`)
    - `score` (0–100)
    - `dados_importantes` (nome, necessidades, orçamento, prazo, dores)
    - `proximos_passos`

- Usa expressões regulares e regras de negócio para evitar uso desnecessário de IA.

---

## 4. Node "Prepare Lead Data"

- Junta:
  - Telefone (do WhatsApp Trigger)
  - Nome (IA → `dados_importantes.nome` ou `contacts[0].profile.name`)
  - Resumo, temperatura, score, motivo_classificacao
- Retorna um objeto único, pronto para ser usado em:
  - Supabase
  - Google Sheets

---

## 5. Supabase – Create Lead

- Recebe o objeto do "Prepare Lead Data".
- Insere na tabela `leads`:
  - `telefone`
  - `nome`
  - `resumo`
  - `temperatura`
  - `score`
  - `status`
  - `origem`

---

## 6. Google Sheets – Dashboard

- Recebe o mesmo objeto do "Prepare Lead Data".
- Cria uma nova linha com:
  - `lead_id` → nome do cliente
  - `telefone`
  - `status`
  - `temperatura`
  - `score`
  - `resumo`
  - `proximos_passos`

---

Este fluxo pode ser estendido com:

- Memória do cliente (tabela `lead_memory`)
- Reengajamento (mensagens após 24h via template)
- Integração com CRM externo
- Notificações para time comercial (e-mail, Slack, etc.)


Ela responde com texto normal e, em outro fluxo (ou etapa), um agente/Function extrai um JSON com:

{
  "resumo": "",
  "temperatura": "",
  "score": 0,
  "motivo_classificacao": "",
  "dados_importantes": {
    "nome": "",
    "empresa": "",
    "necessidades": "",
    "orcamento": "",
    "prazo": "",
    "dores": ""
  },
  "proximos_passos": ""
}

Node "Qualificação de Lead" (JS – Regras)

Entrada:

item.json.history → texto completo da conversa

Saída (item.json recebe):

resumo

temperatura

temperatura_sheet (FRIO/MORNO/QUENTE)

score

motivo_classificacao

dados_importantes

proximos_passos

Node "Prepare Lead Data"

Entrada:

qual → saída da qualificação

WhatsApp Trigger → telefone e nome de perfil

Saída (lead):

{
  "telefone": "55119...",
  "nome": "Nome do contato",
  "resumo": "Resumo da conversa...",
  "temperatura": "morno",
  "score": 62,
  "motivo_classificacao": "Score 62 e classificado como morno...",
  "origem": "whatsapp",
  "status": "novo"
}

Google Sheets

No node do Sheets, os campos podem ser mapeados assim:

lead_id → {{ $node["Prepare Lead Data"].json["nome"] }}

telefone → {{ $node["Prepare Lead Data"].json["telefone"] }}

status → {{ $node["Prepare Lead Data"].json["status"] }}

temperatura → {{ $node["Prepare Lead Data"].json["temperatura"] }}

score → {{ $node["Prepare Lead Data"].json["score"] }}

resumo → {{ $node["Prepare Lead Data"].json["resumo"] }}