---
name: "ferramenta_axe_pa11y"
description: "Testes automáticos de acessibilidade (WCAG) com axe-core e Pa11y: apanham contraste, etiquetas, foco e leitor de ecrã. Usar em toda a entrega para garantir acessibilidade AA."
---
# axe-core + Pa11y (acessibilidade) — Lara

**O que é:** o `axe-core` (Deque) é o motor de acessibilidade mais usado; o `Pa11y` corre-o pela linha de comandos. Apanham a maioria dos problemas WCAG automaticamente (o resto confirma-se à mão).

## Instalar
```bash
npm i -D @axe-core/playwright   # dentro dos testes Playwright
npm i -g pa11y pa11y-ci         # pela linha de comandos
```

## Com Playwright (uma página)
```js
import AxeBuilder from '@axe-core/playwright';
test('home acessível', async ({ page }) => {
  await page.goto('/');
  const r = await new AxeBuilder({ page }).withTags(['wcag2a','wcag2aa']).analyze();
  expect(r.violations).toEqual([]);
});
```

## Pa11y (rápido, várias páginas)
```bash
pa11y --standard WCAG2AA https://staging.exemplo-do-diego.pt
pa11y-ci --sitemap https://staging.exemplo-do-diego.pt/sitemap.xml
```

## À mão (o que a máquina não apanha)
Teclado (tab/enter), foco visível, leitor de ecrã, contraste real, textos alternativos com sentido. Registar falhas em `relatorio_de_testes.md`.
