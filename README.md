# Workflow de Enriquecimento e Geração de Mensagens com IA

## Visão Geral

Este projeto consiste em uma automação baseada em workflow para processamento de dados de clientes, enriquecimento de informações, utilização de um modelo de linguagem (LLM) para geração de conteúdo personalizado e envio automatizado de e-mails.

O processo é iniciado por um webhook que recebe informações coletadas por um RPA desenvolvido em Python. Os dados passam por etapas de normalização, enriquecimento, cruzamento de informações e tratamento antes de serem enviados para uma LLM. Por fim, o conteúdo gerado é enviado automaticamente aos clientes via Gmail.

---

## Arquitetura da Solução

### 1. Coleta de Dados (RPA Python)

Um RPA desenvolvido em Python realiza a coleta de informações em páginas HTML utilizando as bibliotecas:

- `requests`
- `BeautifulSoup`

#### Responsabilidades

- Acessar as fontes de dados via HTTP;
- Realizar parsing do HTML;
- Extrair informações relevantes dos clientes;
- Estruturar os dados em formato JSON;
- Enviar os dados para o workflow através de uma requisição `POST`.

---

### 2. Recebimento dos Dados

O workflow é iniciado através de um **Webhook**, responsável por receber os dados enviados pelo RPA.

Os dados recebidos são utilizados como base para toda a execução subsequente.

---

### 3. Bifurcação do Fluxo

Após o recebimento dos dados pelo webhook, o fluxo é dividido em dois caminhos paralelos:

#### Caminho 1

Os dados recebidos seguem diretamente para um nó de **Merge**, onde posteriormente serão cruzados com informações complementares.

#### Caminho 2

Os mesmos dados são enviados para um nó de **HTTP Request**, responsável por consultar uma fonte externa e obter um arquivo CSV contendo informações adicionais sobre os perfis dos clientes.

---

### 4. Normalização do CSV

Após o recebimento do CSV, um **Code Node** realiza:

- Leitura dos dados recebidos;
- Tratamento de inconsistências;
- Padronização dos campos;
- Conversão para uma estrutura compatível com o restante do fluxo.

Os dados normalizados são enviados ao nó **Merge**.

---

### 5. Cruzamento de Informações

No nó **Merge**, ocorre o processo de associação (match) entre:

- Dados recebidos pelo webhook;
- Dados obtidos através do CSV.

O objetivo é consolidar as informações dos clientes em uma única estrutura para processamento posterior.

---

### 6. Preparação para a LLM

Após o merge, os dados passam por etapas de transformação antes de serem enviados ao modelo de linguagem.

#### Code Node - Preparação dos Dados

Responsável por:

- Organizar os campos necessários;
- Selecionar apenas informações relevantes;
- Estruturar o payload utilizado pela IA.

#### Code Node - Normalização da Entrada

Realiza ajustes adicionais na estrutura de dados:

- Padronização de textos;
- Limpeza de caracteres especiais;
- Ajustes de nomenclatura;
- Preparação para composição do prompt.

---

### 7. Processamento pela LLM

Os dados normalizados são inseridos em um prompt customizado e enviados para o modelo de linguagem (LLM).

#### Objetivo da LLM

- Interpretar os dados do cliente;
- Gerar conteúdo personalizado;
- Produzir mensagens contextualizadas para comunicação automatizada.

---

### 8. Pós-processamento da Resposta da IA

Após o retorno da LLM, um novo **Code Node** realiza o tratamento da resposta.

#### Atividades executadas

- Ajuste de formatação;
- Extração do conteúdo relevante;
- Validação de campos obrigatórios;
- Estruturação do texto final para envio.

---

### 9. Validação das Mensagens

Um nó condicional (**IF**) realiza validações antes do envio do e-mail.

Exemplos:

- Existência de destinatário válido;
- Presença de conteúdo gerado;
- Validação de regras de negócio.

Somente registros aprovados seguem para envio.

---

### 10. Envio de E-mails

O envio é realizado através do nó nativo do **Gmail**.

#### Funcionalidades

- Envio automatizado de mensagens;
- Personalização baseada na resposta da LLM;
- Comunicação individualizada para cada cliente.

---

## Fluxo Resumido

```text
RPA Python
    ↓
Webhook
    ↓
 ┌───────────────┬────────────────────┐
 │               │                    │
 │               ↓                    │
 │         HTTP Request               │
 │               ↓                    │
 │       Normalização CSV             │
 │               ↓                    │
 └──────────→ Merge ←─────────────────┘
                  ↓
      Preparação dos Dados
                  ↓
      Normalização Entrada LLM
                  ↓
               LLM
                  ↓
     Normalização Pós-LLM
                  ↓
                 IF
                  ↓
               Gmail
                  ↓
              Cliente
```

---

## Tecnologias Utilizadas

### Coleta de Dados

- Python
- Requests
- BeautifulSoup

### Orquestração

- n8n

### Integrações

- Webhook
- HTTP Request
- Gmail

### Inteligência Artificial

- Large Language Model (LLM)

### Processamento

- Code Nodes (JavaScript)

---

## Benefícios da Solução

- Automatização completa do processo de coleta e comunicação.
- Enriquecimento de dados através de múltiplas fontes.
- Personalização de conteúdo utilizando IA.
- Redução de atividades operacionais manuais.
- Escalabilidade no envio de comunicações para clientes.
- Centralização da lógica de negócio em um único workflow.
