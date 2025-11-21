
---

## 📄 5. `docs/instrucoes-importacao.md`

```md
# Como Importar o Workflow no n8n

## 1. Exportando do n8n (se quiser salvar ou versionar outros fluxos)

1. Abra o workflow desejado no n8n.
2. No canto superior direito, clique nos **três pontinhos (···)**.
3. Clique em **Download** ou **Export**.
4. Isso vai gerar um arquivo `.json` do workflow.

Coloque esse arquivo na pasta `workflows/` deste repositório.

---

## 2. Importando para o n8n

1. Abra o n8n.
2. Vá até **Workflows**.
3. Clique em **Import from File**.
4. Selecione o arquivo:
   - `workflows/atendimento-nina-whatsapp.json`
5. Clique em **Import**.
6. Ajuste as credenciais necessárias (WhatsApp, Supabase, Sheets, IA).
7. Salve e, quando estiver tudo certo, clique em **Activate**.

---

## 3. Recomendações

- Mantenha uma cópia dos workflows exportados em `workflows/`.
- Sempre que fizer alterações importantes no fluxo, gere um novo export `.json` e atualize o repositório.
- Use mensagens de commit descritivas, como:
  - `feat: adiciona qualificação de lead sem IA`
  - `fix: ajusta mapeamento de score no Google Sheets`
  - `chore: atualiza workflow de atendimento whatsapp`
