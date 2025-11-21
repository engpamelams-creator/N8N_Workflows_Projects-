# Mapeamento de Variáveis entre os Nodes

Este documento mostra como os principais campos são passados de node para node.

---

## WhatsApp Trigger → IA / Fluxo

- `messages[0].from` → telefone do cliente
- `messages[0].text.body` → mensagem atual do cliente
- `contacts[0].profile.name` → nome do perfil do WhatsApp

---

## IA (Nina) → Qualificação

A IA recebe um texto no formato:

```text
Histórico: <mensagens anteriores>
Mensagem: <mensagem atual>
