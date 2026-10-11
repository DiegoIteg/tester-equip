---
name: "ferramenta_k6"
description: "Testes de carga e stress com k6 (Grafana): simula muitos utilizadores e mede se aguenta. Usar em ambiente de testes, nunca em produção, antes de lançamentos ou campanhas."
---
# k6 (carga e stress) — Rafael

**O que é:** a ferramenta de testes de carga moderna mais usada; escreve-se o cenário em JavaScript e mede-se tempos e erros com muitos utilizadores simultâneos.

## ATENÇÃO
Só em **ambiente de testes/staging** do Diego. Carga em produção pode derrubar o site e conta como ataque se for contra terceiros.

## Instalar
```bash
brew install k6   # ou binário de github.com/grafana/k6/releases
```

## Cenário
```js
import http from 'k6/http';
import { check, sleep } from 'k6';
export const options = {
  stages: [ { duration: '1m', target: 50 }, { duration: '2m', target: 50 }, { duration: '1m', target: 0 } ],
  thresholds: { http_req_duration: ['p(95)<800'], http_req_failed: ['rate<0.01'] },
};
export default function () {
  const r = http.get('https://staging.exemplo-do-diego.pt/');
  check(r, { 'status 200': (res) => res.status === 200 });
  sleep(1);
}
```
```bash
k6 run carga.js
```

## Entrega
Até quantos utilizadores aguenta bem, onde começa a falhar, e o que está a limitar (base de dados, CPU). Útil antes de uma campanha do Bruno ou de um lançamento.
