# Automação de Diagnóstico de Vulnerabilidades com IA — Auditoria SSL/TLS
O workflow backend é responsável por receber os dados enviados pelo site, executar a análise SSL/TLS do domínio informado, processar os dados com IA, gerar o relatório e enviar o resultado por e-mail.

## Fluxo geral:
<img width="1774" height="887" alt="image" src="https://github.com/user-attachments/assets/4254d4d0-2ed5-4fca-bb58-dc58e84037a3" />


## 1. Webhook
O node Webhook é a porta de entrada do backend. Ele recebe os dados enviados pelo formulário do site:
´´´bash 
{
 "email": "usuario@email.com",
 "domain": "google.com"
 }
´´´

### 1.1 Configuração principal:

- Método: POST
- Path: /analisar ou ID do webhook
- Respond: Using Respond to Webhook Node
- Esse node permite que o workflow seja acionado automaticamente quando o usuário clica em “Analisar servidor” no site.
- Função no projeto:
- Coletar o e-mail do usuário
- Coletar o domínio que será analisado
- Iniciar automaticamente o processo de auditoria

## 2. HTTP Request
O node HTTP Request faz a comunicação com a API pública do SSL Labs. Ele envia o domínio recebido no Webhook para o endpoint da Qualys SSL Labs:

´´´bash
https://api.ssllabs.com/api/v3/analyze
´´´

### 2.1 Parâmetros usados:
- host = domínio recebido do formulário
- publish = off
- Exemplo de expressão usada no parâmetro host:
  ´´´bash
{{$json.body.domain || $json.query.domain}}
  ´´´
  Esse node retorna dados técnicos sobre o servidor, como:

### 2.2 Host analisado
- Porta 443
- Status da análise
- Endereços IP encontrados
- Nome dos servidores
- Nota SSL Labs
- Informações dos endpoints
- Configurações SSL/TLS
- Possíveis vulnerabilidades

### 2.3 Função no projeto:

Coletar informações técnicas do servidor alvo Analizar protocolos criptográficos SSL/TLS Obter dados reais de segurança do domínio


## 3. AI Agent

O node AI Agent recebe os dados técnicos retornados pelo SSL Labs e transforma essas informações em um relatório compreensível. Ele usa o modelo Google Gemini Chat Model conectado ao node. A função da IA é interpretar o JSON técnico e gerar uma análise contendo:

- Coleta de informações do sistema alvo
- Vulnerabilidades identificadas
- Classificação de risco
- Recomendações de correção
- Conclusão

O prompt instrui a IA a atuar como um agente de auditoria de Segurança da Informação. Exemplo de instrução usada:

´´´´bash
Você é um Agente de Auditoria de Segurança da Informação.
Faça a análise dos dados coletados de um servidor web via SSL Labs.
Gere um relatório em HTML contendo:
Coleta de informações do sistema alvo
Vulnerabilidades identificadas
Classificação de risco
Recomendações de correção
Conclusão
Não use markdown.
Retorne apenas HTML.
´´´

Também foi adicionada uma instrução para formatar a seção de endpoints como tabela:
´´´text
Na seção "1. COLETA DE INFORMAÇÕES DO SISTEMA ALVO", gere uma tabela HTML usando class="ssl-table".
Função no projeto:
Interpretar os dados técnicos
Identificar vulnerabilidades
Classificar os riscos
Sugerir ações corretivas
Gerar relatório legível para o usuário
´´´

## 4. Google Gemini Chat Model 
Esse node fica conectado ao AI Agent como modelo de linguagem. Ele é responsável por executar a análise textual usando inteligência artificial.
Configuração geral:
- Modelo: Gemini
- Função: Chat Model do AI Agent

Ele não recebe diretamente o usuário final, mas fornece a capacidade de IA para o node AI Agent.

### 4.1 Função no projeto:
- Fornecer inteligência artificial ao processo
- Transformar dados técnicos em análise de segurança
- Gerar explicações e recomendações





5. Edit Fields
O node Edit Fields organiza a saída gerada pela IA.
Ele cria um campo chamado: dados_servidor
Esse campo recebe o conteúdo gerado pelo AI Agent: {{$json.output}}
A ideia é padronizar o nome do campo para ser usado nos nodes seguintes, como Gmail e Respond to Webhook.
Função no projeto:
Organizar a saída da IA
Criar um campo único com o relatório final
Facilitar o envio do relatório por e-mail e retorno para o site


6. Gmail — Send a message
O node Gmail envia o relatório gerado para o e-mail informado pelo usuário no formulário.
O campo To utiliza o e-mail recebido pelo Webhook:
{{$node["Webhook"].json.body.email}}
O campo Message utiliza o relatório organizado pelo Edit Fields:
{{$node["Edit Fields"].json.dados_servidor}}
Configuração geral:
Resource: Message
Operation: Send
Email Type: HTML
Como o relatório é gerado em HTML, o e-mail chega formatado visualmente para o usuário.
Função no projeto:
Enviar automaticamente o relatório para o usuário
Entregar o resultado da auditoria por e-mail
Registrar uma saída prática do processo automatizado

7. Respond to Webhook
O node Respond to Webhook devolve uma resposta para o site após a execução do backend.
Ele permite que o relatório também apareça na tela do usuário, além de ser enviado por e-mail.
Configuração usada:
Respond With: Text
Response Body: relatório gerado pela IA

Expressão usada:
{{$node["Edit Fields"].json.dados_servidor}}
Assim, o frontend recebe o conteúdo HTML do relatório e exibe na página.
Função no projeto:
Retornar o relatório para o site
Permitir visualização do resultado na tela
Finalizar a requisição do formulário web







Funcionamento completo
Quando o usuário acessa o site e preencher o formulário:
E-mail: usuario@email.com
Domínio: google.com
O site envia os dados para o Webhook do backend.
O backend então:
Recebe e-mail e domínio pelo Webhook
Envia o domínio para a API do SSL Labs
Recebe os dados técnicos da análise SSL/TLS
Envia esses dados para o AI Agent
A IA interpreta os dados e gera um relatório
O Edit Fields organiza o relatório
O Gmail envia o relatório para o usuário
O Respond to Webhook devolve o relatório para aparecer no site

Relação com os requisitos do trabalho
O backend atende aos requisitos da atividade da seguinte forma:
Coletar informações do sistema alvo: Webhook + HTTP Request.

Identificar vulnerabilidades: HTTP Request + AI Agent.

Classificar e apresentar riscos: AI Agent.

Sugerir medidas corretivas: AI Agent.

Gerar relatório: AI Agent + Gmail + Respond to Webhook.

Automatizar o processo:N8N integrando todos os nodes.
Resumo técnico
Webhook:
Recebe os dados do usuário.

HTTP Request:
Consulta a API SSL Labs e coleta dados técnicos do servidor.

AI Agent:
Analisa os dados e gera relatório de segurança.

Google Gemini Chat Model:
Modelo de IA usado pelo agente.

Edit Fields:
Organiza o relatório em um campo padronizado.

Gmail:
Envia o relatório para o e-mail informado.

Respond to Webhook:
Retorna o relatório para ser exibido no site.

Esse workflow simula um processo automatizado de auditoria SSL/TLS, desde a coleta de dados até a entrega do relatório final ao usuário.

