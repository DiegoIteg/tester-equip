# Documento-prompt — correções do pente-fino (Go Auto)
**Gerado por:** tester_equip · **Data:** 2026-10-11
**Como usar:** cada bloco é um prompt pronto a dar ao agente/equipa indicada. **Nada se corrige nem instala sem o "sim" do Diego**; no Go Auto, a instalação é sempre feita pelo Diego (SQL + zip pelo Webmin).

---
## 1. Mensagem amigável nos geradores de PDF sem registo
**Para:** engenharia do Go Auto (+ `tester_equip` reteste)
**Gravidade:** Menor
**Prompt:**
> Nos geradores `PDFOrcamentoDocument`, `PDFConsolidadoDocument`, `PDFOSForm` e `ReciboContaItemDocument`: quando são abertos sem `key` (sem registo), em vez de lançar exceção técnica, mostrar uma mensagem simples em PT-PT ("Abra este documento a partir de um registo") e voltar atrás. Confirmar que no menu/links eles só aparecem a partir de um registo. Entregar no formato Go Auto (zip + LEIA-ME) para o Diego instalar. Reteste: abrir os 4 sem contexto e confirmar mensagem amigável; abrir a partir de um registo e confirmar o PDF.

## 2. Preços de orçamento com ids fixos por filial (antes do 1.º cliente SaaS)
**Para:** `agencia_seguranca/revisao_de_codigo` (Paulo) + engenharia do Go Auto
**Gravidade:** Importante (estrutural)
**Prompt:**
> Rever o cálculo de preços dos orçamentos e substituir os ids fixos `GrupoVendas::PRODUTOS/SERVICOS/HORAS` e `SetorEmpresa::COMERCIAL` por uma resolução por empresa/filial (pelo nome/flag da linha de receita e do setor da própria empresa), para não misturar dados quando houver um 2.º cliente SaaS. Mapear todos os sítios que usam estes ids (`OrcamentoFechamentoService`, `OrcamentoForm`, `TabelaPrecoForm`, `CadHoraForm`, formulários de stock). Propor plano de alteração ao Diego antes de mexer; entregar no formato Go Auto com testes de isolamento multi-empresa. Reteste pela `tester_equip` (um utilizador de uma empresa não vê preços/dados de outra).

## 3. Serviço único de faturação (orçamento → conta a receber)
**Para:** engenharia do Go Auto
**Gravidade:** Importante
**Prompt:**
> Centralizar a geração do financeiro a partir do orçamento (hoje espalhada por `GerarFinanceiroConsolidadoForm`, `ConsolidacaoOrcamentosForm`, ações da `OrcamentoList`) num serviço único `OrcamentoFaturacaoService::gerar($orcamentoId, $opcoes)` que crie as contas a receber de forma consistente e testável, e passar os ecrãs a chamá-lo. Escrever testes da cadeia orçamento→conta_item. Plano ao Diego antes; entrega no formato Go Auto.

## 4. NIF do cliente para as guias Moloni
**Para:** Diego (decisão) + `agencia_digital/engenheiro_de_integracoes` (Nuno)
**Gravidade:** Pendente de negócio
**Prompt:**
> As guias de transporte Moloni precisam do NIF do cliente, que não existe na ficha de pessoa. Propor ao Diego: acrescentar o campo NIF à pessoa (com validação PT) e usá-lo na emissão da guia; definir o que fazer quando o cliente não tem NIF. Entrega no formato Go Auto depois do "sim".

## 5. Logística 20.2 (melhorias já planeadas)
**Para:** engenharia do Go Auto (com `tester_equip`)
**Gravidade:** Melhoria planeada
**Prompt:**
> Avançar a Logística 20.2: motorista sem rede (offline), janelas horárias de entrega, levantamento na loja e transportadoras. Já validado para depois do Business. Plano ao Diego, entrega no formato Go Auto, reteste de fluxo pela `tester_equip`.

---
### Resumo para o Diego
O sistema passou no pente-fino: **nada rebenta**. As correções acima são de robustez e de preparação para crescer (vários clientes SaaS), não problemas que travem o uso hoje. Diga qual(is) quer que eu avance — faço o plano e, com o seu "sim", entrego para instalar.
