# Relatório de testes — Go Auto (pente-fino de ponta a ponta)
**Equipa:** tester_equip (Hélder + equipa) · **Ambiente:** testes (cópia), nunca produção · **Data:** 2026-10-11

## Como se testou (como um cliente acabado de contratar)
1. **Empresa nova de raiz** no ambiente de testes, com modelo de negócio montado (linhas de receita, setor, nicho, filiais).
2. **Varrimento a todas as telas do sistema** — as 259 telas do catálogo dos 8 módulos (Comercial, Produção, Financeiro, Compras, Stock, RH, Logística, Business) — em dois cenários:
   - **Cliente novo** (quase vazio): cada tela tem de abrir bem logo no primeiro dia.
   - **Com movimento**: orçamentos, financeiro, plano anual e um pedido de entrega criados.
3. **Fluxo de ponta a ponta** (orçamento → produção → stock → financeiro → faturação → logística → entrega → reentrega → devolução), percorrido e validado pelas suites automáticas (~1000 verificações).
4. **Jornada visual:** prints das telas principais do percurso.

## Veredicto
**PODE USAR.** O sistema está sólido: **nenhuma tela rebenta** — 259 telas, cliente novo e com movimento, 0 crashes. Os fluxos de negócio passam de ponta a ponta. As suites automáticas estão todas verdes:
- Outbound 133, Logística 114, Business 52, Planeamento 53, DRE 30, Menu/Dono 14.
- Financeiro 35 + 32, Compras 47, Devoluções 41, Stock 98, RH 17 + 185, E-mail 22, Envio 17, Perfis 57.

## Achados (poucos, como deve ser)

### Menor / contexto
- **4 geradores de PDF rebentam se abertos sem um registo:** `PDFOrcamentoDocument`, `PDFConsolidadoDocument` (Comercial), `PDFOSForm` (Produção), `ReciboContaItemDocument` (Financeiro). Precisam do n.º do registo (`key`) e só são chamados a partir de um orçamento/OS/recibo — **não é um defeito que o cliente encontre no uso normal**. Melhoria: mostrar uma mensagem amigável (em vez de erro técnico) se alguém lá chegar sem contexto.

### Importante (estrutural — já no backlog técnico)
- **Preços de orçamento com números fixos por filial** (`GrupoVendas::PRODUTOS/SERVICOS/HORAS`, `SetorEmpresa::COMERCIAL`): hoje funciona na sua empresa; só dá problema quando houver um **2.º cliente SaaS**. Corrigir **antes do primeiro cliente SaaS**.
- **Faturação sem um serviço único testável:** gerar o financeiro a partir do orçamento está espalhado por ecrãs com muitos parâmetros. Recomenda-se centralizar a cadeia orçamento → conta a receber num serviço próprio, com testes — fica mais seguro e mais fácil de manter.

### Pendente de negócio
- **NIF do cliente para as guias Moloni** não existe na ficha de pessoa. Decisão do Diego + integração.

### Melhorias já planeadas (Logística 20.2)
- Motorista sem rede (offline), janelas horárias de entrega, levantamento na loja, transportadoras.

## Prints da jornada
`shots/comercial.png`, `shots/orcamentos.png`, `shots/financeiro.png`, `shots/torre.png`, `shots/direcao.png`.

## Nota de método
O varrimento renderiza cada tela e apanha erros reais (exceções, SQL, variáveis indefinidas). Não houve nenhum. O fluxo de clicar ecrã a ecrã no navegador real da produção não se faz (regra: Go Auto só em testes, login sempre do Diego); o percurso foi exercido pelo próprio código das telas e ações, que é mais fundo.
