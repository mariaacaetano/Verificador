# Prompts usados com IA

## Prompt 1

**Pergunta:**

> Chat, como usa o N8N?

### Resposta

O n8n é uma plataforma de automação visual (low-code) que permite conectar aplicativos como Gmail, WhatsApp, Google Sheets, bancos de dados, APIs e modelos de IA sem precisar programar muito.

Você cria um fluxo arrastando e conectando blocos (chamados de **nodes**).

### O funcionamento básico

#### 1. Escolha um gatilho (Trigger)

É o evento que inicia o fluxo.

Exemplos:

- Novo e-mail recebido
- Mensagem no WhatsApp
- Formulário preenchido
- Um horário específico
- Webhook

#### 2. Adicione ações

Cada ação faz alguma coisa.

Exemplos:

- Criar uma linha no Google Sheets
- Enviar um e-mail
- Fazer uma requisição para uma API
- Chamar um modelo da OpenAI
- Salvar dados em um banco

#### 3. Teste o fluxo

- Execute manualmente
- Veja os dados passando entre os nós
- Corrija erros

#### 4. Ative o workflow

Depois de ativado, ele roda automaticamente sempre que o gatilho acontecer.

### Exemplo simples

Imagine que alguém preenche um formulário no seu site.

```text
Formulário
    ↓
Webhook
    ↓
Google Sheets
    ↓
Enviar e-mail
    ↓
Notificar no WhatsApp
```

## Preciso saber programar?

Não necessariamente.

Você consegue fazer muita coisa apenas configurando nós.

Conhecer um pouco de:

- JSON
- APIs
- Lógica de programação
- JavaScript (para casos mais avançados)

ajuda bastante, mas não é obrigatório para começar.

## Como aprender rápido

O caminho mais eficiente costuma ser:

1. Instalar ou criar uma conta no n8n Cloud.
2. Fazer o tutorial inicial.
3. Criar fluxos simples (Gmail → Google Sheets, por exemplo).
4. Aprender Webhooks e APIs.
5. Integrar IA (OpenAI, Ollama, Claude etc.).

Como sei que você gosta de trabalhar com algoritmos e automações, o n8n pode ser muito útil para criar projetos como:

- Robôs para analisar resultados de loterias
- Agentes de IA que respondem perguntas automaticamente
- Automações de vendas
- Leitura e organização de e-mails
- Integração entre planilhas, bancos de dados e APIs
- Assistentes pessoais que executam tarefas sozinhos

Se você quiser, posso te ensinar n8n do zero em um curso prático de 1 hora, criando um assistente de IA completo passo a passo.

---

# Prompt 2

**Pergunta:**

> Consegue me dar um passo a passo de como incluir alguns nodes?

### Resposta

Vou te mostrar um passo a passo bem prático, pensando no editor visual do n8n: como adicionar um node, configurar, conectar e testar.

Exemplo básico:

```text
Webhook → Edit Fields → IF → Gmail/Sheets
```

## 1. Criar o workflow

Abra o n8n → **New Workflow**.

## 2. Adicionar o primeiro node

Clique em **Add first step** ou no botão **+**.

Pesquise por **Webhook** e selecione.

O Webhook serve para receber dados e iniciar o fluxo.

## 3. Configurar o Webhook

No node **Webhook**:

- HTTP Method: `POST`
- Path: `teste-lead`

Clique em **Test step**.

Ele vai gerar uma URL de teste.

## 4. Adicionar outro node

No Webhook, clique no **+** à direita dele.

Pesquise por **Edit Fields** ou **Set**.

Esse node serve para criar ou organizar campos que vieram do node anterior.

### Exemplo de campos

```text
nome      = {{$json.nome}}
email     = {{$json.email}}
mensagem  = {{$json.mensagem}}
```

## 5. Adicionar uma condição

Clique no **+** depois do **Edit Fields**.

Pesquise por **IF**.

Configure assim:

- **Value 1:** `{{$json.email}}`
- **Operation:** `Exists`

Assim o fluxo verifica se veio um e-mail.

## 6. Adicionar uma ação

No caminho **true** do IF, clique no **+**.

Você pode escolher, por exemplo:

- Gmail → enviar e-mail
- Google Sheets → adicionar linha
- Telegram → mandar notificação
- HTTP Request → enviar para uma API

O n8n trabalha com nodes de gatilho e de ação; ao adicionar um node, você escolhe a operação que ele vai executar.

## 7. Testar

Clique em **Execute Workflow** para rodar manualmente.

O n8n permite executar o fluxo inteiro ou partes específicas durante os testes.

## Fluxo final

Um bom primeiro projeto seria:

```text
Formulário de contato
        ↓
     Webhook
        ↓
   Edit Fields
        ↓
        IF
      ├── true  → Gmail / Google Sheets
      └── false → Encerrar ou responder erro
```
