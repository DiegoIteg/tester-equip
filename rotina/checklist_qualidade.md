# Checklist de qualidade (antes de publicar)

## Funcional
- [ ] Todas as ligações funcionam (sem 404); `tel:`, `mailto:`, WhatsApp, redes da marca, PDFs.
- [ ] Todos os botões fazem o que dizem; nada morto.
- [ ] Formulários: obrigatórios, validações, mensagens de erro em PT-PT, envio real (dados de teste) e chegada.
- [ ] Fluxos completos de ponta a ponta.
- [ ] Estados vazio / a carregar / erro / sucesso.

## Conteúdo PT-PT
- [ ] Textos em português de Portugal; sem lorem ipsum.
- [ ] Números 1.234,56; datas DD/MM/AAAA.
- [ ] Sem dados de exemplo (telefones 123-456-7890, moradas fictícias à vista).

## Dispositivos
- [ ] Telemóvel, tablet, computador (360/390/768/1280/1440 px); Chrome/Safari/Edge.
- [ ] Nada cortado, sobreposto ou ilegível.

## Desempenho
- [ ] Lighthouse ≥ 90 no telemóvel; carrega depressa.

## Acessibilidade
- [ ] Contraste AA, teclado, foco visível, textos alternativos, etiquetas nos formulários.

## Legal (sites)
- [ ] Cookies (aceitar e recusar), Política de Privacidade, Livro de Reclamações, NIF e morada no rodapé.

## Back-end (Go Auto e sistemas)
- [ ] Contas e cálculos certos; permissões e escopo multi-empresa; regressão verde.
