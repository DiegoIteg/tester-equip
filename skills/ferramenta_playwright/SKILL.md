---
name: "ferramenta_playwright"
description: "Testes de ponta a ponta no navegador com Playwright (o mais usado): grava, corre em Chrome/Firefox/Safari, faz prints e guarda o rasto para depurar. Usar para automatizar fluxos de sites e apps."
---
# Playwright (E2E) — Diogo / Beatriz

**O que é:** a ferramenta de testes de navegador mais usada hoje. Corre em Chromium, Firefox e WebKit (Safari), grava testes, tira prints e guarda um *trace* para ver o que aconteceu.

## Instalar
```bash
npm init playwright@latest        # cria a estrutura e instala os navegadores
# ou em Python: pip install playwright && playwright install
```

## Gravar um teste a clicar (codegen)
```bash
npx playwright codegen https://staging.exemplo-do-diego.pt
```

## Correr
```bash
npx playwright test                 # todos
npx playwright test --project=webkit --project="Mobile Safari"
npx playwright test --trace on      # guarda o rasto para depurar
npx playwright show-report
```

## Padrão de um teste (fluxo + estados)
```js
import { test, expect } from '@playwright/test';
test('formulário de contacto envia', async ({ page }) => {
  await page.goto('/contacto');
  await page.getByLabel('Nome').fill('Teste QA');
  await page.getByLabel('Email').fill('teste@exemplo.pt');
  await page.getByRole('button', { name: 'Enviar' }).click();
  await expect(page.getByText('Obrigado')).toBeVisible();
});
```

## Regras
Ambiente de testes; login pelo Diego; dados de teste fictícios; nunca ações que enviam/pagam/apagam em produção.
