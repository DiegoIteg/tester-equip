# Equipa de Testes do Diego

> 6 agentes, cada um com diretrizes, personalidade e memória (`equipa/<pasta>/AGENTE.md` + `memory.md`). Testam **vários projetos**, uma pasta por projeto em `projetos/`. **Conversam entre si** na `conversa.md` de cada projeto (`protocolo_de_conversa.md`). Formato Claude Code em `_subagentes/`.

## Organograma
```
                 DIEGO (decide o que se testa, recebe o veredicto, faz logins)
                                        │
                        HÉLDER — Líder de Testes (plano, prioridades, veredicto)
                                        │
 ┌──── FUNCIONAL ────┐   ┌──── TÉCNICO ────┐   ┌──── EXPERIÊNCIA ────┐
 BEATRIZ  Funcionais     DIOGO  Automação/API    MÓNICA  Dispositivos/browsers
                                                 RAFAEL  Desempenho e carga
                                                 LARA    Acessibilidade e dados de teste
```

| Agente | Função | Chame-o quando… |
| :--- | :--- | :--- |
| **Hélder** `direcao_de_testes` | Plano de testes, prioridades, consolida, dá o veredicto | Início e fim de cada pedido |
| **Beatriz** `testes_funcionais` | Fluxos, botões, ligações, formulários, estados, mensagens PT-PT | Qualquer site, app ou ecrã |
| **Diogo** `testes_automaticos` | Playwright, testes de back-end/API, suites de regressão | Automatizar, testar back-end, correr regressão |
| **Mónica** `compatibilidade` | Telemóvel/tablet/computador, Chrome/Safari/Edge, responsivo | Antes de publicar |
| **Rafael** `desempenho` | Lighthouse, tempos de resposta, consultas lentas | Quando está lento ou antes de lançar |
| **Lara** `acessibilidade_e_dados` | WCAG AA, estados vazios/erro, dados de teste fictícios | Em toda a entrega |

## O ciclo de cada pedido
```
Âmbito (Hélder) → Mapa de testes → Execução (Beatriz, Diogo, Mónica, Rafael, Lara) →
Relatório (Crítico/Importante/Menor) → Veredicto (Hélder) → Diego → Reteste + teste de fumo → Memória
```
Pedidos pequenos (um ecrã, um botão) podem ser diretos, mas o relatório e o veredicto mantêm-se.

## Harmonia da equipa
- Cada falha vai para `projetos/<projeto>/relatorio_de_testes.md`: passo a passo, esperado, obtido, gravidade, print.
- Em projetos grandes, as frentes correm em paralelo (subagentes), cada um a ler o seu `AGENTE.md`, juntando tudo na `conversa.md`.
