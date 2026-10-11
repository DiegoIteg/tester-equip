---
name: "ferramenta_api"
description: "Testes de APIs: contrato (Schemathesis a partir do OpenAPI), coleções (Newman/Postman) e pedidos diretos. Usar para testar o back-end e as integrações do Diego."
---
# Testes de API (contrato e integração) — Diogo

**O que é:** três abordagens muito usadas para testar APIs — contrato automático a partir do OpenAPI (Schemathesis), coleções (Newman/Postman) e pedidos diretos.

## Contrato automático (a partir do OpenAPI/Swagger)
```bash
pipx install schemathesis
schemathesis run https://staging.exemplo-do-diego.pt/openapi.json --checks all
```
Gera entradas automaticamente e apanha respostas erradas, 500, e dados fora do esquema.

## Coleções (Postman → linha de comandos)
```bash
npm i -g newman
newman run colecao.postman_collection.json -e ambiente_teste.json
```

## Pedidos diretos (rápido)
```bash
# httpx ou curl para casos pontuais
curl -s -X POST https://staging.exemplo-do-diego.pt/api/orcamentos -H 'Content-Type: application/json' -d '{"cliente":1}' | jq .
```

## No Go Auto
Testar as rotas/endpoints com dados de teste, incluindo **permissões e escopo multi-empresa** (um utilizador de uma empresa não deve ler dados de outra). Registar falhas em `relatorio_de_testes.md`.

## Regras
Ambiente de testes; dados fictícios; login pelo Diego; nunca escrever/apagar dados reais.
