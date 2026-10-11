# Relatório de testes — App Go Auto (PWA + endpoints)
**Equipa:** tester_equip (Hélder + Beatriz + Diogo + Mónica + Rafael + Lara) · **Ambiente:** testes (SQLite, cópia do esquema real), nunca produção · **Data:** 2026-10-11

## Como se testou
A app tem duas camadas. Testámos ambas:
1. **Back-end — 60 verificações automáticas** que exercem **todos os endpoints `/api/app/*`**, nos ramos felizes e de erro, com **duas empresas** semeadas para provar o isolamento. Correu o código real dos endpoints e dos serviços (orçamento, produção, RH, entregas, mensagem), como o pente-fino do sistema faz.
2. **Front-end — revisão da PWA** (`index.html`, `app.js`, `app.css`): botões ligados, textos, estados, formatos, fila offline, logout.

**Resultado automático: 60 OK, 0 falhas** depois das correções abaixo.

## Veredicto
**PODE PUBLICAR depois de instalar as 2 correções** (1 e 2). Sem elas, há **dois botões que não funcionam** no uso real:
- criar orçamento **com uma viatura escolhida** da lista → rebentava;
- motorista marcar uma entrega como **falhada** → nunca aceitava o motivo.

Ambas já estão corrigidas na cópia de testes e **reprovadas a verde**; falta o Diego instalar (a da app instalo eu; a do código é o Diego que sobe o zip e extrai).

---

## Achados

### 1. CRÍTICO (corrigido, aguarda instalação) — orçamento com viatura escolhida rebentava
- **O que o utilizador sentia:** o comercial escolhe uma viatura do cliente na lista e carrega em "Criar orçamento" → **erro, não cria**. (Só funcionava quando NÃO se escolhia viatura.)
- **Porquê:** a consulta filtrava a viatura por `vt.create_unit`/`vt.tenant_id`, colunas que a tabela `viatura` **não tem** (em produção MariaDB dá "Unknown column"). A viatura já pertence ao cliente, que por sua vez já está no âmbito da empresa.
- **Correção:** filtrar a viatura pela `pessoa_id` do cliente (igual ao que já se fazia na lista de viaturas). Ficheiro: `app/controller/AppApiController.php` (comercialCriarOrcamento). **Leva reinstalação do código (overlay).**
- **Confirmado:** criar orçamento com viatura escolhida passou a funcionar; totais/IVA/desconto certos.

### 2. IMPORTANTE (corrigido, aguarda instalação) — "Entrega falhada" do motorista não funcionava
- **O que o motorista sentia:** escrevia o motivo da falha e carregava em "Entrega falhada" → a app respondia sempre **"Escolha o motivo."** e não deixava avançar.
- **Porquê:** a app enviava o motivo em **texto livre**, mas a API só aceita **códigos** de uma lista fixa (Cliente ausente, Morada errada, Recusada, Danificada, Sem acesso, Outro).
- **Correção (na PWA):** o motivo passou a ser uma **lista** com esses códigos; "Outro" pede uma explicação. Ficheiros: `app/app.js` (motParagemSheet) + cache do service worker `v15 → v16`. **Instalo eu (ficheiros da app).**
- **Confirmado:** a API aceita o código e rejeita texto livre; com a lista, a falha regista bem.

### 3. MENOR (nota de esquema) — IVA por linha arredondado em serviços e horas
- **O que é:** no esquema, `servico_item.iva`, `servico_item.total_iva_item`, `servico_item.preco_com_desc_tabela` e `hora_item.iva` estão como **número inteiro** (no `produtos_item` estão decimais). O IVA guardado **nessas linhas** fica arredondado ao euro.
- **Impacto real:** **baixo.** O **total do orçamento** (subtotal, IVA, total) é calculado em código e está **correto** — foi verificado (ex.: 140 € + 23% = 172 €). Só os valores guardados linha-a-linha de serviços/horas perdem os cêntimos.
- **Recomendação:** passar essas colunas a decimais num próximo ajuste de base de dados (leva SQL). Não bloqueia publicar.

---

## O que foi verificado (e passou)

**Autenticação:** sem token → 401; token inválido → 401 (em todos os endpoints). Login sem dados → 400; `me` devolve utilizador + empresa certa.

**Comercial — orçamento:** pesquisa de clientes (com telefone/email para preencher), viaturas (marca/modelo/matrícula/cor/ano), produtos, serviços, setores (mão de obra); preço por tabela + IVA (produto, serviço, horas); criar orçamento com cliente existente **e** cliente novo (cria pessoa + contactos), com viatura escolhida **e** sem viatura ("S/ MATRÍCULA"), itens Produto+Serviço+Horas com **IVA somado** e **desconto** de negociação, e por valor à mão; detalhe com as 3 linhas; foto (válida guarda, inválida recusa); etapas; mudar para PERDIDO com motivo; guardas do envio de email (destino inválido → 400).

**Isolamento entre empresas (crítico):** a empresa B **não vê** o orçamento da A (404), **não anexa** foto à A (404), **não vê** o catálogo da A. ✓

**Operário — produção:** estado da ordem, iniciar etapa, etapa aberta, feedback técnico (vazio → 400), concluir. **RH:** tipos de ausência, pedir (sem tipo/data → 400; sem colaborador associado → 400 claro), lista dos meus pedidos.

**Motorista — entregas:** sem paragem → 400; sem viagem ativa → 404; entregar com código correto (fica ENTREGUE), código errado (aviso), falhar com motivo (código), enviar GPS, terminar viagem.

**Cliente:** mensagem vazia → 400; com OS associada fica **na OS** marcada `[Cliente]`; sem OS fica como **nota de pós-venda**.

**PWA (front-end):** 100% em **português** (sem inglês na interface); todos os botões ligados a uma ação; números em **1.234,56 €** e datas **DD/MM/AAAA**; estados "a carregar…", vazios e de erro; **fila offline** (guarda e reenvia ao voltar a rede); logout e estado de ligação ("Ligado"/"Sem ligação").

## Nota de método
O percurso foi exercido pelo próprio código dos endpoints e serviços (mais fundo que clicar no navegador). O único ponto do MariaDB sem equivalente no SQLite de testes (`CURDATE()` na procura da viagem do dia) foi coberto com um equivalente no arnês — em produção é MariaDB e funciona de origem. Arnês: `/root/work/logapp/test_app_endpoints.php`.
