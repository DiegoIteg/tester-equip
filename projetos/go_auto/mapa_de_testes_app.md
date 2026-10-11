# Mapa de testes — App Go Auto

## Transversal (todos os endpoints)
- Sem token → 401 "Sessão inválida".
- Token inválido/expirado → 401.
- Parâmetros em falta → 400 com mensagem clara em PT-PT.

## Login / sessão
- `POST /app/login` — sem utilizador/pass → 400; credenciais erradas → 401; utilizador com `usa_app=0` → 403; válido → token + perfil + empresa/unidade.
- `GET /app/me` — token válido → user/empresa/unidade/perfil.
- `POST /app/signup` — validação de conta (erros claros).

## Resumos (casa de cada perfil)
- `GET /app/gestor|operario|comercial|motorista|cliente/resumo` — abre sem rebentar em empresa vazia e com movimento.

## Comercial — orçamento (o processo mais rico)
- `GET /comercial/clientes?q=` — < 2 letras → vazio; ≥ 2 → lista com nif/telefone/email (preenchimento do contacto).
- `GET /comercial/viaturas?pessoa_id=` — viaturas do cliente (matrícula/marca/modelo/cor/ano).
- `GET /comercial/produtos|servicos|setores?q=` — catálogo da empresa.
- `GET /comercial/preco?pessoa_id=&tipo=P|S|H&item_id=` — preço base, desconto de tabela, IVA.
- `POST /comercial/orcamento`:
  - cliente existente (pessoa_id) **e** cliente novo (nome+nif+telefone+email → pessoa + contatos).
  - viatura existente **e** sem viatura (cria "S/ MATRÍCULA").
  - itens Produto + Serviço + Horas com preço de tabela e **IVA somado**; desconto de negociação; totais certos.
  - sem itens → usa valor escrito à mão (sem IVA).
  - sem cliente nem nome → 400.
- `GET /comercial/orcamento?id=` — detalhe com itens (P/S/H), subtotal/IVA/desconto/total, fotos, etapa, email do cliente.
- `POST /comercial/orcamento/foto` — base64 válida guarda; imagem inválida → 400; orçamento de outra empresa → 404.
- `GET /comercial/etapas` + `POST /comercial/orcamento/etapa` — muda etapa (fecha atual, abre nova); PERDIDO grava motivo.
- `POST /comercial/orcamento/email` — e-mail inválido → 400; válido → envia PDF (SMTP de testes).

## Operário — produção
- `GET /operario/ordem?orcamento_id=` — estado, etapa, pode_iniciar/concluir/pausar/retomar, opções.
- `POST /operario/iniciar|concluir|pausar|retomar` — máquina de estados.
- `POST /operario/feedback` — anexa observação; sem texto → 400.

## Operário — RH
- `GET /operario/rh/tipos` — tipos de ausência (exige_documento).
- `POST /operario/rh/pedir` — sem tipo/data → 400; utilizador sem colaborador → 400; válido → cria pedido.
- `GET /operario/rh/meus` — lista dos pedidos com estado e datas DD/MM/AAAA.

## Motorista — entregas
- `POST /motorista/entregar|falhar|posicao|terminar` — auth, validação, "sem viagem ativa" → 404; entrega com assinatura/foto/GPS.
- Nota de portabilidade: a procura da viagem do dia usa `CURDATE()` (MariaDB). Verificar em produção (MariaDB); no SQLite de testes usa-se um equivalente.

## Cliente — falar com a oficina
- `POST /cliente/mensagem` — sem texto → 400; com OS associada → fica na OS; sem OS → fica como nota de pós-venda.

## PWA (front-end)
- Todos os botões de cada ecrã ligados a uma ação.
- Textos 100% PT-PT (sem "Online/Offline/Loading/Save"...).
- Estados vazios e de erro visíveis.
- Números 1.234,56 e datas DD/MM/AAAA.
- Responsivo (telemóvel) e acessibilidade básica (foco, contraste, alvos de toque).
- Fila offline (guarda e reenvia ao voltar a rede).
