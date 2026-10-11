# Ferramentas e como usar

| Ferramenta | Para quê | Notas |
| :--- | :--- | :--- |
| Playwright | Percorrer ecrãs, testar fluxos, prints em vários tamanhos | Ambiente de testes; login pelo Diego |
| Lighthouse | Velocidade e boas práticas no telemóvel | Alvo ≥ 90 |
| Harness de back-end (Go Auto) | Suite em SQLite STRICT com os tipos reais do MySQL + servidor PHP local | Nunca em produção; dados fictícios |
| Servidor SMTP de teste | Confirmar envio de e-mails sem enviar a sério | Em testes |

**Nunca** se usam dados reais nem se fazem testes que enviam/pagam/apagam em produção.
