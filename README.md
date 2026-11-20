# Laboratório de Integração e Automação de CRM com n8n

Este projeto consiste em um ambiente prático para simulação, teste e validação de fluxos de integração entre sistemas de captura de leads e plataformas de CRM utilizando o n8n e arquitetura orientada a eventos via Webhooks.

O objetivo do laboratório é demonstrar padrões de recebimento de payloads, validação de integridade de dados, roteamento condicional de eventos e comunicação com serviços externos via chamadas HTTP REST.

## Arquitetura do Fluxo

O fluxo implementado (`CRM Lead Routing Workflow`) opera sob a seguinte estrutura sequencial:

1. Webhook (Trigger): Escuta requisições HTTP do tipo POST no endpoint `/webhook-test/crm-lead`, atuando como ponto de ingestão de eventos.
2. Nó Condicional (If): Avalia a integridade e as propriedades do payload recebido, validando se o atributo `status` corresponde a `new_lead`.
3. Ramificação de Sucesso (True): Direciona leads válidos para um nó de HTTP Request (GET), simulando a persistência ou notificação para uma API externa.
4. Ramificação Alternativa (False): Desvia leads que não cumprem o critério de qualificação ou já existentes para um nó utilitário (No Operation), finalizando o ciclo sem processamento desnecessário.

## Estrutura do Projeto

```text
n8n-crm-integration-lab/
├── payloads/
│   └── n8n-crm-integration-lab.postman_collection.json  # Coleção do Postman para validação dos cenários
├── workflows/
│   └── CRM Lead Routing Workflow.json                   # Definição exportada do fluxo no n8n
├── .gitignore                                           # Proteção contra arquivos locais e credenciais
├── docker-compose.yml                                   # Orquestração do container do n8n com volume persistente
└── README.md                                            # Documentação técnica do projeto
```

## Pré-requisitos

- Docker e Docker Compose instalados.
- Postman (ou cliente HTTP equivalente) para disparo das requisições de teste.

## Como Executar

1. Iniciar o ambiente n8n via Docker:
   ```bash
   docker compose up -d
   ```
2. Acessar a interface gráfica no navegador:
   ```text
   http://localhost:5678
   ```
3. Importar o fluxo:
   - Na interface do n8n, acesse o menu principal e selecione "Import from File".
   - Selecione o arquivo localizado em `workflows/CRM Lead Routing Workflow.json`.

## Como Testar

1. No Postman, importe a collection localizada em:
   `payloads/n8n-crm-integration-lab.postman_collection.json`
2. Cenários disponíveis para validação:
   - `POST - New Lead (Success Flow)`: Envia um payload qualificado com status `new_lead`, direcionando a execução para a ramificação positiva do fluxo.
   - `POST - Alternative Status (Fallback Flow)`: Envia um payload com status alternativo (`existing_client`), ativando a rota de descarte controlado no nó NoOp.
