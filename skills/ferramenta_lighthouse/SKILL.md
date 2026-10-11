---
name: "ferramenta_lighthouse"
description: "Medição de velocidade, boas práticas, SEO e acessibilidade com Lighthouse e Lighthouse CI (com limites que falham o build). Usar para garantir sites rápidos (≥90 no telemóvel)."
---
# Lighthouse + Lighthouse CI (desempenho) — Rafael

**O que é:** a ferramenta da Google para medir desempenho, boas práticas, SEO e acessibilidade. O **Lighthouse CI** impõe limites (orçamentos) que fazem falhar se o site piorar.

## Instalar
```bash
npm i -g lighthouse @lhci/cli
```

## Medir
```bash
lighthouse https://staging.exemplo-do-diego.pt --preset=desktop --view
lighthouse https://staging.exemplo-do-diego.pt --form-factor=mobile --throttling.cpuSlowdownMultiplier=4 --output=html --output-path=lh-mobile.html
```
Olhar os Core Web Vitals: LCP (< 2,5 s), CLS (< 0,1), INP (< 200 ms). Alvo da casa: **≥ 90 no telemóvel**.

## Orçamentos (falha se piorar)
```bash
lhci autorun --collect.url=https://staging.exemplo-do-diego.pt \
  --assert.assertions.categories:performance=0.9
```

## Recomendações típicas
Imagens pesadas (converter para WebP/AVIF), JS a mais, fontes a bloquear, sem cache. Entregar as 3-5 melhorias de maior impacto.
