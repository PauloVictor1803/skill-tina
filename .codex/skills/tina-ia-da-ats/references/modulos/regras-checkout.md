### Dificuldade 12: Regras Operacionais Críticas do Resulth Checkout (Contingência e Inutilização)
* **Regra de Retransmissão de Contingência e Tecla F6:**
  - Notas em contingência offline (`T`) são transmitidas **automaticamente em segundo plano** pelo Checkout assim que a conexão restabelece.
  - A tecla `<F6 - Reenviar Rejeitada / Contingência>` aceita **apenas** notas com status **`R` (Rejeitada em contingência)**. Se o operador tentar teclar F6 numa nota em contingência pura (`T`), o sistema emite o bloqueio *"Somente notas com status rejeitado em contingência podem ser retransmitidas"*.
  - Se a nota ficar agarrada/presa em contingência sem envio automático, o chamado deve ser transferido para o **Suporte Técnico N2 da ATS** para alterar o sinalizador de `T` para `R`, habilitando o operador a teclar F6 no caixa.
* **Regra de Inutilização Direta no Checkout:**
  - Para notas com status *"Aguardando Resposta"*, na consulta oficial à SEFAZ (**F12** » **F8** » Consultar), se o fisco retornar que a nota não consta na base, o **próprio Resulth Checkout pergunta na tela se o operador deseja inutilizar a numeração**.
  - Ao confirmar com "Sim", o **Checkout inutiliza a numeração ali mesmo na hora**, permitindo ao operador registrar e passar a venda novamente para o cliente sem duplicidade fiscal e sem necessidade de ir à Retaguarda ou Web.

### Dificuldade 20: Saneamento Mandatorio de Rotinas do Resulth ECF / PAF-ECF Obsoletas
* **O que acontece:** O RAG recupera artigos antigos da epoca do Resulth ECF (ex.: citando o antigo executavel Config ECF ou modulo Config) para duvidas de emissao de cupom fiscal, induzindo a assistente virtual a orientar o cliente a buscar menus e executaveis inexistentes, como ocorreu no ticket #113840.
* **Causa:** Acervo legado com paginas que nao foram devidamente arquivadas na transicao do PAF-ECF para o **Resulth Checkout moderno**.
* **Regra de Ouro:**
  - O Resulth Checkout moderno substituiu integralmente o Resulth ECF.
  - No Checkout, a identificacao de CPF/CNPJ na nota e feita unicamente via tecla **F5** no caixa ou na importacao da pre-venda (com **F4** para confirmar e **F5** para pular), e as configuracoes gerais ficam em <F12> » Configuracoes ou tecla **F1**.
  - Sempre que for identificada uma pagina legada fazendo referencia ao Config ECF, modulo Config ou PAF-ECF, ela deve ser imediatamente arquivada/apagada da base ativa do Tina Console via apagar_pagina e substituida pela rotina moderna correspondente do Resulth Checkout.

