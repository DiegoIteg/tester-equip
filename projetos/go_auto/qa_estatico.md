# QA estática — Go Auto (ERP, PHP 8.3 + Adianti 7.5)

- **Projeto:** Go Auto — `/root/work/itest`
- **Fase:** 4 (QA), passagem **estática** (leitura + grep; nada foi alterado nem executado)
- **Equipa:** Beatriz (funcional), Lara (dados/acessibilidade), Diogo (back-end)
- **Data:** 2026-10-12
- **Âmbito desta passagem:** código de **produção** (`app/control`, `app/service`, `app/model`, `app/templates` e ficheiros PHP próprios do Go Auto na raiz). Excluídos os ficheiros de dados/seed (`app/database/*.sql`, `.db`).
- **Nota de método:** cada suspeita foi **confirmada** no código antes de ser afirmada. Falsos positivos típicos de Adianti (métodos herdados de classes-pai e de *traits*, máscaras de input, `mt_rand`) foram verificados e descartados.

---

## Resumo do veredicto

| Gravidade | Nº |
| :--- | :--- |
| **Crítico** | 0 |
| **Importante** | 3 |
| **Menor** | 4 |

Nesta passagem **estática** não se encontrou nenhum defeito Crítico (que impeça usar ou perca dados). O tema dominante, e o maior obstáculo à comercialização **em Portugal**, é a **língua**: a maioria dos ecrãs de CRUD do Adianti está em **português do Brasil**, não PT-PT. Há também **um botão morto** (erro ao clicar) e **opções de documento brasileiras** no registo público. Falta validação dinâmica (clicar, submeter, navegar) para fechar o veredicto — ver "Limitações".

---

## CRÍTICO
Nenhum encontrado nesta passagem estática.
(Recomenda-se confirmar em testes dinâmicos os fluxos de guardar orçamento, pagamento Stripe/Mercado Pago e envio de e-mail, que a leitura de código não cobre.)

---

## IMPORTANTE

### I-1 — Interface em português do Brasil (não PT-PT) — sistémico
O produto é vendido em Portugal (sinais claros: `cadastrar.php` com "NIF (Portugal)", EUR, "palavra-passe", "utilizador", "teste grátis 30 dias", "oficina"), mas a esmagadora maioria dos ecrãs herdados do Adianti continua em PT-BR. É o que um cliente português nota de imediato em quase todos os ecrãs.

Dimensão (ocorrências só em `app/control/`, texto maioritariamente visível): `usuário/usuario` ≈ 460, `cadastro/cadastrar` ≈ 360, `arquivo` 41, `endereço` 24, `tela` 19, `senha` 23.

Termos a corrigir: **cadastrar → registar**, **usuário → utilizador**, **arquivo → ficheiro**, **endereço → morada**, **tela → ecrã**, **senha → palavra-passe**, **celular → telemóvel**.

Piores casos (texto em botões, etiquetas e títulos — vê-se no ecrã):
- `app/control/crm/CadastroDeClientesList.php:190` botão **"Cadastrar"** (e :204 "Atualizar", :211 "Limpar filtros")
- `app/control/crm/TarefasFormView.php:39` e `:43` etiquetas **"Usuário de atualização/criação:"**
- `app/control/stock_/InventarioForm.php:136` etiqueta **"Usuário"**
- `app/control/admin/SystemDataImportForm.php:82` etiqueta **"Arquivo:"**
- `app/control/crm/ViaturaList.php:155`, `stock_/SubGrupoList.php:134`, `stock_/FamiliaProdutoList.php:134`, `stock_/GrupoProdutoList.php:136`, `crm/CadServicosList.php:158`, `crm/TabelaPrecoList.php:141`, `crm/CadHoraList.php:137`, `admin/CatalogoSimplesList.php:58` — todos botão **"Cadastrar"** (padrão repetido em ~288 ecrãs de lista)
- Mensagens de erro em PT-BR mostradas ao utilizador, ex.: `cadastrar.php:91` "O CPF **informado** é inválido" (PT-PT: "indicado").

Observação: o `menu.xml` e os ecrãs **novos** feitos à medida (`painel.php`, `cadastrar.php`) já estão em PT-PT — a localização foi feita por cima, mas o miolo das telas CRUD do Adianti ficou por traduzir. Fica como ação de localização transversal.

### I-2 — Botão "Cadastrar" morto no ecrã de Grupos de permissões
`app/control/recursos_humanos/SystemGroupFormList.php:130` — o botão **"Cadastrar"** chama `new TAction(['SystemGroupForm', 'onShow'])`, mas a classe `SystemGroupForm` (`app/control/admin/SystemGroupForm.php`) **não tem o método `onShow`** (só define `__construct`, `onSave` e `onEdit`) e não o herda (estende `TPage`). Confirmado no motor do Adianti (`lib/adianti/core/AdiantiCoreApplication.php:198-214`): quando o método não existe, é lançada a exceção **"Method SystemGroupForm::onShow not found"**. 
**O que o utilizador sente:** ao clicar em "Cadastrar" para criar um novo grupo, aparece um erro em vez de abrir o formulário. Há volta (criar via outro caminho/edição), por isso **Importante** e não Crítico, mas é um botão partido num ecrã de administração.

### I-3 — Opções de documento brasileiras no registo público
`cadastrar.php:258-259` — o formulário público de criação de conta (mercado PT, por omissão "NIF (Portugal)") mostra também **"CNPJ (empresa BR)"** e **"CPF (pessoa BR)"** no seletor de tipo. Para uma oficina portuguesa é confuso e pode levar à escolha errada. A validação do NIF existe mas é fraca (só conta ≥ 9 dígitos, `cadastrar.php:94`), enquanto CPF/CNPJ têm validação de dígito completa. Rever se o mercado BR é mesmo suportado; se não, remover as duas opções e reforçar a validação de NIF.

---

## MENOR

### M-1 — Listas sem estado "sem registos" (sistémico, cosmético)
Só **4 de ~288** ecrãs de lista têm mensagem explícita de vazio. Nos restantes, o TDataGrid do Adianti mostra o cabeçalho e uma área de grelha em branco quando não há resultados — o utilizador fica sem saber se "não há nada" ou se "falhou a carregar". Não bloqueia; é acabamento de UX. Recomenda-se mensagem padrão "Sem registos a apresentar".

### M-2 — Ferramentas de desenvolvimento (Builder) com dados e e-mail de exemplo
`app/service/builder/database/BuilderDatabaseService.php` e `BuilderDatabaseSystemService.php` (dezenas de linhas) contêm o e-mail do autor do framework `lcstomasi@gmail.com`; `app/service/builder/database/BuilderDatabaseManagerService.php:635-637` gera dados fictícios (`usuario{n}@exemplo.com`, telefones `(11) 9xxxx-xxxx`). **Mitigado:** o Builder e outras ferramentas de dev estão bloqueadas em produção por `index.php:10` (exige o ficheiro `app/config/.allow_devtools`). Não é visível ao cliente, mas convém confirmar que o *flag* está desligado no servidor de produção antes de comercializar.

### M-3 — Restos de demonstração em ficheiros de tema (vendored)
`console.log(...)` e páginas de exemplo nos temas AdminLTE que acompanham o Adianti: `app/templates/theme3/js/app.js:635`, `theme3/js/pages/dashboard.js:201/205`, `theme4/js/pages/examples/sign-in.js:4` (e sign-up, forgot-password), `theme4/js/pages/maps/google.js:25`; `app/resources/swagger.html:167/186/187`. Além disso, `app/resources/page_sample.html` contém **"Lorem ipsum"** mas é órfão (nenhum controller o referencia). São ficheiros de template/demo que vão no pacote mas não estão ligados a ecrãs do Go Auto. Limpeza recomendada, sem impacto funcional.

### M-4 — `print_r`/dumps em código de produção (inócuos, verificados)
- `app/control/saas_admin/SaasGatewayPagamentoWebhookForm.php:107` e `:142` — `print_r($dados, true)` **guardado num campo** (`info_webhook`) para registo, não é eco para o ecrã. OK.
- `app/control/log/SystemSessionDumpView.php:26` — `<pre>...print_r...` é um ecrã de diagnóstico de administração (intencional). OK.
- `app/control/cadastros_saas/SaasUsuarioForm.php:221` — `var_dump` **comentado**. OK.
- `app/service/ProducaoTempoService.php:318`, `app/service/ReservaStockService.php:109/145/148` — `die(...)` são guardas legítimas de execução só por linha de comandos. OK.
Sem ação; listado para rastreabilidade.

---

## O que foi verificado e está BEM
- **Botões/ligações mortos (item 6):** varrimento de todas as `TAction(['Classe','método'])` em `app/control`. Candidatos iniciais (onExport*, onReload, onEdit, onShowCurtainFilters…) confirmados como **herdados de classes-pai ou de *traits*** (`AdiantiStandardListExportTrait`, `TStandardList`, `TStandardForm`) — falsos positivos. Resta **apenas** o I-2.
- **Números/datas PT-PT (item 5):** os ecrãs novos à medida formatam corretamente — `painel.php:8` `number_format(...,2,',','.') . ' €'` e meses/datas em PT. (Os ecrãs Adianti dependem da configuração regional; recomenda-se confirmação dinâmica.)
- **`cadastrar.php`:** boa qualidade — anti-CSRF, *honeypot*, escape anti-XSS, NIF por omissão, PT-PT. A única ressalva é o I-3.
- **TODO/FIXME em produção:** nenhum em `app/control`/`app/service`.
- **Dados fictícios (123-456-7890, teste@, @admin.net, "João da Silva"):** nenhum em código de produção visível.

## Limitações desta passagem
Passagem **estática** (leitura de código). Não substitui os testes dinâmicos de: guardar/editar orçamento, pagamentos (Stripe em modo de teste, Mercado Pago), envio de e-mails, validação de obrigatórios nos formulários Adianti em execução, responsivo e acessibilidade. Recomenda-se a passagem funcional (Beatriz) e automática (Diogo, Playwright) em ambiente de testes antes do veredicto final "Pode publicar".
