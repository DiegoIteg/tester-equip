# Memória — Equipa de Testes

> Formato: `- AAAA-MM-DD | Tipo | Descrição | Projeto/Ficheiros`.

- 2026-10-11 | Decisão | Equipa de Testes criada: 6 agentes que testam qualquer projeto do Diego (Go Auto, Agência Digital, clientes). Veredicto "pode / não pode publicar" | `CLAUDE.md`
- 2026-10-11 | Decisão | Go Auto testa-se em ambiente de testes, com a suite em SQLite STRICT + Playwright; nunca em produção com dados reais | `go_auto/CLAUDE.md`
- 2026-10-11 | Preferência | Diego não é programador: cada falha é explicada pelo que o utilizador sentiria, em PT-PT | geral
- 2026-10-11 | Aprendizagem | Skills de ferramentas de topo adicionadas: Playwright (E2E), axe-core+Pa11y (acessibilidade), Lighthouse+CI (desempenho), k6 (carga, só testes), Schemathesis/Newman (API) | `skills/`
- 2026-10-11 | Resolvido | Pente-fino à APP Go Auto (PWA + endpoints /api/app/*): 60 verificações, 0 falhas após 2 correções. Veredicto: pode publicar depois de instalar. | `projetos/go_auto/relatorio_de_testes_app.md`, `/root/work/logapp/test_app_endpoints.php`
- 2026-10-11 | Falha | CRÍTICO (corrigido): criar orçamento com viatura escolhida rebentava — consulta usava vt.create_unit/tenant_id que a tabela viatura não tem (MariaDB: "Unknown column"). Correção: filtrar viatura por pessoa_id. Leva reinstalação do overlay. | `AppApiController::comercialCriarOrcamento`
- 2026-10-11 | Falha | IMPORTANTE (corrigido): "Entrega falhada" do motorista enviava motivo em texto livre, mas a API exige códigos (AUSENTE/MORADA/RECUSADA/DANIFICADA/ACESSO/OUTRO) → botão nunca funcionava. Correção na PWA: lista de motivos + obs para "Outro"; SW v15→v16. | `app/app.js` motParagemSheet
- 2026-10-11 | Pendente | MENOR: servico_item.iva/total_iva_item/preco_com_desc_tabela e hora_item.iva são INTEGER no esquema (produtos_item é REAL) → arredonda cêntimos nas linhas guardadas. Totais do orçamento OK (calculados em PHP). Passar a decimal num ajuste SQL futuro. | esquema
- 2026-10-11 | Aprendizagem | Arnês de endpoints da app: boot init.php + SQLite test.db; token JWT real (seed do application.ini); Request lê de $_REQUEST; ob_start() para http_response_code() funcionar em CLI; keep-connection (config iteg.php) p/ registar CURDATE/NOW (MariaDB) no SQLite. | `/root/work/logapp/test_app_endpoints.php`
