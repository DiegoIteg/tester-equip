# Go Auto — Fase 4 (QA) — Relatório e Veredicto

> 2026-10-12. Equipa de Testes (Hélder, Beatriz, Diogo, Mónica, Rafael, Lara). Ambiente de testes.
> Esta fase é a última para o "pode comercializar". Classificação: **Crítico** (impede usar / perde dados → não publica) · **Importante** (atrapalha, há volta → corrigir antes) · **Menor** (cosmético).

## 1. QA automático / estático — FEITO

### Integridade do menu e rotas ✅
- **169/169** controladores referenciados no `menu.xml` **existem** — **zero links partidos**.
- Os 5 ecrãs com `onShow` (Nichos, Motivos de perda, Origem de cliente, etc.) herdam o método de `CatalogoSimplesList` → funcionam.

### Falhas encontradas
**IMPORTANTE**
- **I-1 · Interface em Português do Brasil, não de Portugal (sistémico).** É o maior obstáculo para vender em Portugal. Centenas de termos de origem brasileira nos ecrãs: "cadastrar", "usuário", "arquivo", "endereço", "tela", "senha" (~460 "usuário", ~360 "cadastro" só em `app/control`). A maioria vem dos ecrãs CRUD gerados e da tradução do próprio Adianti. Ex.: `crm/CadastroDeClientesList.php`, `admin/SystemDataImportForm.php`. → **Precisa de um projeto de localização PT-PT** (ver secção 4).
- **I-2 · Botão "Cadastrar" morto nos Grupos/Perfis.** `SystemGroupFormList` chamava `SystemGroupForm::onShow` (inexistente) → "Method not found". **JÁ CORRIGIDO** (passa a `onEdit`, como o resto do sistema) — entra no próximo pacote.
- **I-3 · Documentos brasileiros no registo público.** `cadastrar.php` mostra "CNPJ (empresa BR)" e "CPF (pessoa BR)" — numa oficina portuguesa devia ser **NIF/NIPC**; a validação do NIF é fraca (aceita ≥9 dígitos sem validar o dígito de controlo).

**MENOR**
- 4 listas (em 288) sem mensagem de "sem registos" (ecrã em branco quando vazio).
- Restos de `console.log`/lorem ipsum em ficheiros do tema AdminLTE (não ligados aos ecrãs reais).
- Ferramentas Builder com dados de exemplo — mas **já bloqueadas em produção**.

**Verificado e OK:** sem botões mortos além do I-2; sem dados-lixo (123-456-7890, teste@) nos ecrãs de produção; `painel.php` e `cadastrar.php` em PT-PT com € e datas corretos; sem TODO/FIXME em produção.

## 2. Teste ao vivo (fluxos críticos) — A FAZER (precisa da tua sessão)
Login→painel · criar/gravar orçamento · criar cliente · gerar PDF · pesquisa/autocomplete · logout. Também: **compatibilidade** (telemóvel/tablet, Chrome/Safari/Edge), **desempenho** (tempos de carga) e **acessibilidade** (contraste, teclado). → Requer o sistema a correr e a tua sessão iniciada (o login é sempre teu).

## 3. Veredicto (provisório, após o estático)
- **Funcionalmente, a base está sã:** menu íntegro, um único botão morto (já corrigido), sem dados-lixo nos ecrãs.
- **O bloqueio real para o mercado português é o I-1 (PT-BR → PT-PT).** Um ERP português cheio de "cadastrar/usuário/tela" não passa no cliente.
- **Ainda não "pode publicar"** até: (a) localização PT-PT, (b) I-3 (NIF/NIPC no registo), e (c) o teste ao vivo dos fluxos confirmar que tudo grava bem.

## 4. Próximos passos propostos
1. **Projeto de localização PT-PT** (o grande): traduzir os termos dos ecrãs e da framework para PT-PT, de forma controlada (glossário: cadastro→registo, usuário→utilizador, arquivo→ficheiro, endereço→morada, tela→ecrã, senha→palavra-passe, etc.), sem partir código. Proponho fazer por módulos, com revisão.
2. **I-3:** trocar CNPJ/CPF por NIF/NIPC no registo e validar o dígito de controlo do NIF.
3. **Teste ao vivo** dos fluxos críticos na tua sessão + compatibilidade/desempenho/acessibilidade.
