# 🤖 Fluxos n8n – Assistente Virtual Imobiliária (WhatsApp + IA)

Este repositório reúne os workflows do **n8n** usados para automatizar o atendimento de uma imobiliária via **WhatsApp**, combinando:

- Assistente virtual (Nina) com IA
- Qualificação automática de leads (frio, morno, quente, score 0–100)
- Registro de leads em **Supabase**
- Painel de acompanhamento em **Google Sheets**
- Regras em JavaScript para reduzir consumo de tokens de IA

O objetivo é mostrar, na prática, como usar **low-code + IA** para criar um funil de atendimento profissional no mercado imobiliário.

---

## 🧩 Visão Geral do Fluxo

Fluxo principal (exemplo simplificado):

1. **WhatsApp Trigger (Meta / WhatsApp Cloud API)**  
   Recebe mensagens de clientes que buscam imóveis.

2. **Nina – Atendente Virtual (IA)**  
   - Responde em tom humano, curto e direto  
   - Faz perguntas para qualificar:  
     - Compra ou aluguel  
     - Cidade / bairro  
     - Tipo de imóvel  
     - Orçamento  
     - Prazo para mudar  

3. **Qualificação de Lead (Node Function – JS, sem IA)**  
   - Analisa todo o histórico da conversa  
   - Detecta sinais de interesse  
   - Gera:
     - `resumo` da conversa  
     - `temperatura` do lead: `frio`, `morno`, `quente`  
     - `score` (0–100) baseado em interesse, intenção, localização, orçamento, prazo  
     - `dados_importantes` (nome, necessidades, orçamento, prazo, dores)  
     - `proximos_passos` sugeridos para o time de vendas  

4. **Prepare Lead Data (Node Function – JS)**  
   - Centraliza as informações:
     - telefone (WhatsApp)  
     - nome (IA ou perfil do WhatsApp)  
     - resumo, temperatura e score  
   - Monta o objeto final do lead para salvar no Supabase e enviar ao painel.

5. **Supabase – Create Lead**  
   - Salva o lead em uma tabela, por exemplo `leads`:
     - `telefone`  
     - `nome`  
     - `resumo`  
     - `temperatura`  
     - `score`  
     - `origem` (ex.: `whatsapp`)  
     - `status` (ex.: `novo`)  

6. **Google Sheets – Dashboard de Leads**  
   - Cria uma linha no painel com:
     - `lead_id` (nome da pessoa)  
     - `telefone`  
     - `status`  
     - `temperatura`  
     - `score`  
     - `resumo` da conversa  
     - `proximos_passos`  

---

## 📂 Estrutura deste Repositório

```bash
.
├─ workflows/
│  └─ atendimento-nina-whatsapp.json   # Workflow exportado do n8n
│
├─ docs/
│  ├─ fluxo-geral.md                   # Explica cada parte do fluxo
│  ├─ mapeamento-variaveis.md          # Quais campos são usados em cada node
│  └─ instrucoes-importacao.md         # Como importar o .json no n8n
│
├─ assets/
│  ├─ fluxo-n8n.png                    # Print do fluxo dentro do n8n
│  └─ dashboard-google-sheets.png      # Print da planilha de leads
│
├─ .gitignore
└─ README.md



🧪 Como Importar o Workflow no n8n

Veja o passo a passo detalhado em docs/instrucoes-importacao.md
, mas o resumo é:

Abra o n8n (Cloud, Desktop ou Docker).

Vá em Workflows → Import from File.

Selecione workflows/atendimento-nina-whatsapp.json.

Ajuste as credenciais:

WhatsApp Cloud API

Supabase

Google Sheets

OpenAI / n8n AI Agent (se estiver usando IA)

Salve e ative o workflow.

🔐 Dependências & Credenciais

Para rodar o fluxo completo, você precisa configurar no n8n:

WhatsApp Cloud API

Access Token

Phone Number ID

Webhook configurado no painel da Meta

Supabase

URL do projeto

API Key

Tabela de leads (ex.: leads)

Tabela de memória (opcional, ex.: lead_memory)

Google Sheets

Credenciais de acesso (OAuth ou Service Account)

Planilha com colunas como:
lead_id, telefone, status, temperatura, score, resumo, proximos_passos

OpenAI / IA Agent (opcional)

API Key

Modelo configurado (ex.: gpt-4.x ou outro compatível)

⚠ Este repositório não inclui nenhuma credencial.
Use apenas variáveis de ambiente e credenciais internas do n8n.

🧠 Lógica de Qualificação (sem IA)

A qualificação de leads é feita com regras em JavaScript dentro de um node Function, para evitar gasto de tokens quando não é necessário.

Exemplos de critérios:

Lead quente:

Fala em “fechar”, “mandar proposta”, “agendar visita”

Tem orçamento e prazo bem definidos

Lead morno:

Demonstra interesse, faz perguntas, mas ainda está avaliando

Lead frio:

Diz que está “só pesquisando”, “vendo por enquanto”, “talvez no futuro”

A partir disso o fluxo define:

temperatura: frio | morno | quente

score: base 20 + pontos por intenção, tipo, localização, orçamento, prazo

📊 Exemplo de Linha no Dashboard (Google Sheets)

Cada lead pode gerar uma linha como:

lead_id	telefone	status	temperatura	score	resumo	proximos_passos
João	551199999…	novo	QUENTE	88	Cliente quer alugar apto no bairro X.	Entrar em contato hoje, enviar opções de apartamentos.
🧩 Tecnologias Utilizadas

n8n – Orquestração e automações

WhatsApp Cloud API – Canal de entrada dos leads

Supabase – Banco de dados (leads, memória do cliente)

Google Sheets – Dashboard de acompanhamento

OpenAI / n8n AI Agent – Assistente virtual (Nina)

JavaScript (Function Nodes) – Regras de qualificação e preparação de dados