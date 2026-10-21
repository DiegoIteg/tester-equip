# Memória — Rafael (Desempenho e Carga)

> Formato: `- AAAA-MM-DD | Tipo | Descrição | Projeto/Ficheiros`.

- 2026-10-11 | Aprendizagem | Agente criado. Testa qualquer projeto do Diego; nada que mude dados reais corre em produção; login pelo Diego; veredicto antes de publicar | `CLAUDE.md`
- 2026-10-17 | Aprendizagem | Go Auto v37 (TenantScope/PesquisaSegura): SEM regressão no servidor — n.º de consultas igual ao de 67be25a e tempos dentro do ruído (±10%); pesquisa de combo +~8 ms (HMAC). Lentidão de Compras vem de N+1 já existente em ComprasService::estadoEncomenda (7 consultas por encomenda × todas as encomendas nos cartões) | scratchpad desempenho/, app/service/compras/ComprasService.php:98-105
- 2026-10-17 | Aprendizagem | Contar consultas SQL numa cópia: PDO::ATTR_STATEMENT_CLASS com classe contadora ativada por variável de ambiente (QCOUNT) no ramo sqlite do TConnection da cópia | desempenho/qstmt.php
