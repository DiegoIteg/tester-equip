# Plano de testes — App Go Auto (PWA + endpoints)
**Equipa:** tester_equip (Hélder + equipa) · **Ambiente:** testes (SQLite cópia do esquema real), nunca produção · **Login:** sempre do Diego · **Data:** 2026-10-11

## Porquê
O sistema (259 telas) já teve pente-fino. A **app** (PWA no `/app/` + os endpoints `/api/app/*`) foi construída e testada passo a passo durante a obra, mas ainda **não teve uma passagem formal e completa da equipa de testes**. É isso que este plano cobre: todas as nuances, processos e botões.

## Âmbito
1. **Back-end — todos os endpoints `/api/app/*`** (o coração da app): autenticação, validação de entradas, e cada processo de negócio (orçamento, produção, RH, entregas, mensagem do cliente), nos ramos felizes **e** de erro.
2. **Isolamento entre empresas** (multi-empresa/SaaS): um comercial da empresa A não vê nem mexe em dados da empresa B.
3. **Front-end — a PWA**: todos os botões ligados, textos em PT-PT (sem inglês), estados vazios/erro, formato de números (1.234,56) e datas (DD/MM/AAAA), responsivo, acessibilidade básica, fila offline.

## Fora de âmbito (e porquê)
- A lógica profunda dos serviços (preços, produção, faturação, entregas) já está verde no pente-fino do sistema (~1000 verificações). Aqui testa-se a **camada da app** por cima desses serviços.
- Cliques no navegador real **da produção** não se fazem (regra: Go Auto só em testes, login sempre do Diego). O percurso é exercido pelo próprio código dos endpoints, que é mais fundo.

## Critério "passa / não passa"
- **Crítico (não pode publicar):** endpoint rebenta no uso normal, grava mal, deixa entrar sem sessão, ou deixa uma empresa ver/mexer noutra.
- **Importante (corrigir antes de publicar):** validação errada, total/IVA errado, mensagem em inglês, botão sem ação.
- **Menor (corrigir quando der):** cosmético, nota de portabilidade sem impacto em produção.

## Ambiente
- Esquema real `itest_scripts/schema_novo.sql` (SQLite STRICT) → `test.db` na ligação `iteg`.
- Duas empresas semeadas (tenant 1 e 2) para provar o isolamento.
- Token real JWT (mesma `seed` do sistema), como a app faz.
- SMTP de testes local para o envio de e-mail (não sai nada para fora).
