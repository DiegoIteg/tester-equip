# Segurança e acessos

* **Login sempre pelo Diego.** A equipa nunca introduz nem grava palavras-passe.
* **Dados de teste fictícios**, nunca reais. Dados reais de clientes anonimizados se tiverem mesmo de ser usados (RGPD).
* **Go Auto:** testes só no ambiente de testes; a suite corre em SQLite STRICT com os tipos reais do MySQL; Playwright para os ecrãs.
* **Sites Wix:** testar em rascunho/pré-visualização; publicar só com o "sim".
* **Pagamentos:** Stripe só em modo de teste.
