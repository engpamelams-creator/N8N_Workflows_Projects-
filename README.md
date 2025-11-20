
# 🤖 Fluxos n8n – Assistente Virtual Imobiliária (WhatsApp + IA)

Este repositório reúne os workflows do **n8n** usados para automatizar o atendimento imobiliário via **WhatsApp**, com:

- Assistente virtual (Nina) usando IA
- Qualificação automática de leads (frio, morno, quente, score 0–100)
- Registro de leads no **Supabase**
- Painel de acompanhamento em **Google Sheets**

O objetivo é mostrar, na prática, como usar **low-code + IA** para criar um funil de atendimento profissional para o mercado imobiliário.

---

## 🧩 Visão Geral do Fluxo

Fluxo principal (exemplo):

1. **WhatsApp Trigger**  
   Recebe a mensagem do cliente (via WhatsApp Cloud API).

2. **IA (Nina – atendente virtual)**  
   - Lê histórico da conversa  
   - Responde de forma humana, curta e direta  
   - Avança a qualificação (compra/aluguel, bairro, tipo, orçamento, prazo)

3. **Qualificação de Lead (Function / JS)**  
   - Analisa o texto completo  
   - Gera:
     - `resumo` da conversa  
     - `temperatura` (frio, morno, quente)  
     - `score` (0–100)  
     - `dados_importantes` (nome, necessidades, orçamento, prazo, dores)  
     - `proximos_passos` para time de vendas  

4. **Prepare Lead Data (Function / JS)**  
   - Junta:
     - telefone (WhatsApp)  
     - nome (IA + perfil do WhatsApp)  
     - resumo + temperatura + score  
   - Monta o objeto final do lead para salvar e enviar ao painel.

5. **Supabase – Create Lead**  
   - Salva o lead em uma tabela (`leads` ou similar):
     - telefone  
     - nome  
     - resumo  
     - temperatura  
     - score  
     - origem (`whatsapp`)  
     - status inicial (`novo`)  

6. **Google Sheets – Dashboard**  
   - Adiciona uma linha com:
     - lead_id (nome)  
     - telefone  
     - status  
     - temperatura  
     - score  
     - resumo da conversa  
     - próximos passos / observações  

---

## 📂 Estrutura de Pastas Sugerida

```bash
.
├─ workflows/
│  ├─ vistalar-nina-atendimento-whatsapp.json
│  ├─ vistalar-nina-qualificacao-leads.json
│  └─ vistalar-nina-dashboard-google-sheets.json
│
├─ docs/
│  ├─ fluxo-geral.md          # Explicação passo a passo dos nodes
│  ├─ mapeamento-variaveis.md # Campos usados (telefone, resumo, score etc.)
│  └─ credenciais-exemplo.md  # O que precisa configurar no n8n
│
├─ assets/
│  ├─ prints-fluxo.png        # Prints do fluxo no n8n
│  └─ exemplo-sheet.png       # Print do painel no Google Sheets
│
└─ README.md


Você pode adaptar os nomes dos arquivos .json conforme for exportando seus fluxos do n8n.

🧪 Como Importar os Workflows no n8n

Abra o n8n (Cloud, Desktop ou Docker).

Vá em Workflows → Import from File (ou “Import”).

Selecione um dos arquivos em workflows/
Ex.: vistalar-nina-atendimento-whatsapp.json

Clique em Import.

Ajuste as credenciais (WhatsApp, Supabase, Google Sheets, OpenAI).

Salve e ative o workflow.

Repita o processo para cada .json.

🔐 Credenciais / Configuração Necessária

Para rodar tudo, você precisa configurar no n8n:

WhatsApp Cloud API

Access Token

Phone Number ID

Webhook configurado no Meta

Supabase

URL do projeto

API Key

Nome da tabela (leads, lead_memory, etc.)

Google Sheets

Credencial OAuth / Service Account

Planilha criada para o painel

Aba com colunas:
lead_id, telefone, status, temperatura, score, resumo, proximos_passos (por exemplo)

OpenAI / IA Agent

API Key (se usar OpenAI diretamente)

Model configurado no node de IA (ex.: gpt-4.x ou similar)

Prompt da Nina configurado como “System / Instructions”

💡 Este repositório não contém nenhuma credencial sensível.
Use variáveis de ambiente ou credenciais do próprio n8n.

🧠 Principais Regras de Qualificação (JS)

A qualificação (sem IA) usa regras como:

Palavras de forte interesse:
“quero fechar”, “mandar proposta”, “agendar visita” → lead quente

Interesse médio:
“tenho interesse”, “gostei”, “me manda mais detalhes” → lead morno

Baixo interesse / pesquisa:
“só pesquisando”, “depois vejo”, “sem compromisso” → lead frio

Com base nisso, o script gera:

temperatura: frio | morno | quente

score: base 20 + pontos por interesse, intenção, bairro, cidade, orçamento, prazo

Isso evita gastar tokens com IA e mantém o fluxo rápido e estável.

🧾 Exemplo de Campos Enviados pro Google Sheets

Cada linha da planilha pode conter, por exemplo:

lead_id → nome do cliente (extraído do texto ou do perfil do WhatsApp)

telefone → número do WhatsApp

status → novo, em atendimento, qualificado, etc.

temperatura → FRIO | MORNO | QUENTE

score → 0–100

resumo → resumo curto da conversa

proximos_passos → sugestão gerada pelo fluxo para o time comercial

🧩 Tecnologias & Stack

n8n – Orquestração e automações

WhatsApp Cloud API – Canal de atendimento

OpenAI / n8n AI – Atendente virtual (Nina)

Supabase – Banco de dados de leads e memória

Google Sheets – Painel de acompanhamento

JavaScript (Function Nodes) – Regras de qualificação sem IA

🏷 Sugestão de Tópicos (Tags) para o GitHub

No repositório do GitHub, você pode adicionar estes tópicos:

n8n

workflow-automation

whatsapp-bot

imobiliaria

real-estate

chatbot

lead-qualification

openai

supabase

google-sheets

low-code

no-code

ai-assistant

🚀 Sobre este projeto

Este repositório nasceu da prática de construir um fluxo completo de atendimento imobiliário com IA, usando n8n, focado em:

Aprender na prática automações low-code

Aplicar IA de forma útil (não só “bonita”)

Criar algo que poderia ser usado em uma empresa real

Sinta-se à vontade para clonar, adaptar e evoluir estes workflows.
Sugestões e melhorias são super bem-vindas. 🙌


---

## 2️⃣ Estrutura de pastas (resumo visual rápido)

Você pode criar essa estrutura no seu repositório:

```bash
n8n-imobiliaria-workflows/
├─ workflows/
│  ├─ atendimento-whatsapp-nina.json
│  ├─ qualificacao-leads-regra.json
│  └─ painel-google-sheets.json
│
├─ docs/
│  ├─ fluxo-geral.md
│  ├─ mapeamento-variaveis.md
│  └─ credenciais-exemplo.md
│
├─ assets/
│  ├─ fluxo-n8n.png
│  ├─ exemplo-planilha.png
│
└─ README.md
