# Gotchas, Pegadinhas e Maiores Dificuldades na Base da Tina

Este documento cataloga os erros operacionais mais frequentes observados durante o trabalho prático com o Tina Console e a base de conhecimento ATS, e as regras estritas para evitá-los.

---

## 1. Troca / Inversão de Conteúdo (PROC x FEWSHOT)
* **Sintoma:** Ao consultar uma Few-Shot no Tina, ela contém um procedimento gigante de 30 KB com tabelas e telas; ao consultar o Procedimento, ele contém um texto curto de 6 KB.
* **Causa Raiz:** Salvar arquivos invertendo os caminhos remotos durante rotinas automatizadas.
* **Prevenção:**
  - Conferir sempre o campo `type:` no cabeçalho antes de salvar (`type: procedimento` vs `type: few-shot`).
  - Procedimentos possuem mais de 15 KB e seções aprofundadas. Few-shots são concisos e possuem menos de 10 KB.

---

## 2. Slugs Remotos e Links Quebrados (Wiki-links `[[...]]`)
* **Sintoma:** Links internos no rodapé ou corpo do texto aparecem em vermelho no Tina Console.
* **Causa Raiz:** Usar o nome do arquivo local do disco (ex: `[[procedimentos/PROC-emissao-nfe-de-venda-resulth-windows]]`).
* **Prevenção:**
  - O Tina Console exige o caminho exato do documento na árvore do Tina, **sem extensão `.md`**.
  - Exemplo correto: `[[procedimentos/procedimento-de-emissao-de-nota-fiscal-eletronica-nf-e-de-venda-modelo-55-no-res|Título]]`.

---

## 3. Imagens Locais vs Storage Remoto da Tina
* **Sintoma:** Procedimento publicado com imagens que não carregam para o cliente no chat ou no console.
* **Causa Raiz:** Usar caminhos relativos de disco (ex: `./foto.png`) ou preencher `arquivo: midia/2026/10/foto.png` sem ter feito o upload.
* **Prevenção:**
  - Toda imagem deve ser convertida para Base64 e enviada via MCP `enviar_midia_base64`.
  - O storage da Tina gera uma hash (ex.: `midia/2026/10/pesquisa-status-nfe-f05a1681.png`).
  - É essa chave exata que deve constar no bloco `midias:` do YAML.
  - Sincronizar os números das fotos (`[1]`, `[2]`, etc.) no texto do procedimento.

---

## 4. Estrutura Modular Local Autocontida por Assunto
* **Diretriz de Trabalho:** No seu computador, mantenha sempre o pacote completo dentro da pasta do tema:
  `procedimentos/<slug-do-assunto>/`
  - `PROC-*.md` (Procedimento detalhado)
  - `FEWSHOT-*.md` (Exemplo de resposta rápida para a Tina)
  - `*_transcricao.txt` (Transcrição da aula da Universidade ATS)
  - `*.png` (Prints e capturas de tela originais)
* **Regra com a Nuvem:** A separação em `procedimentos/` e `few-shot/` é feita unicamente no Tina Console durante o upload pelo MCP. Localmente, manter tudo agrupado garante 100% de integridade e agilidade de manutenção.

---

## 5. Diferença entre Devolução de NF-e vs Devolução de Cupom Fiscal (NFC-e)
* **Sintoma:** Cliente alega que digita o número do cupom no Documento de Origem e o Resulth diz que não encontrou a venda.
* **Causa Raiz:** No campo `Tipo` da janela Documento de Origem, o padrão é Nota Fiscal. Se a venda foi NFC-e/Cupom, o tipo precisa ser alterado para `Cupom Fiscal`.
* **Prevenção:** Enfatizar esse aviso em destaque visual em qualquer procedimento de devolução.

---

## 6. Expiração de Tokens OAuth e Auditoria
* **Sintoma 1 (Tina MCP):** `server name tina failed to load: calling "initialize": sending "initialize": Unauthorized`.
* **Causa Raiz 1:** A sessão OAuth 2.0 da ATS Central expirou.
* **Solução Imediata:** Rodar no terminal:
  ```bash
  python ats.py auth-tina
  ```
  Isso inicia o listener na porta 8080, abre o navegador com PKCE na página de autorização (`https://auth.central.atsinformatica.com.br/oauth/authorize`), e ao autorizar atualiza automaticamente `C:\Users\paulo.magalhaes\.gemini\antigravity-ide\mcp_oauth_tokens.json`.

* **Sintoma 2 (Google Sheets):** `HTTP Error 401: Unauthorized` ao rodar `python ats.py sync` contra o Google Sheets.
* **Causa Raiz 2:** O token OAuth do Google Sheets expira em 1 hora.
* **Prevenção:**
  - O script `ats.py` possui fallback que gera o Excel local em `planilhas/Relatorio_ATS_v3.xlsx`.
  - Sempre manter `dados/metadados_base.json` e `dados/auditoria.json` populados, garantindo que o Sheets seja atualizado assim que o token for renovado.

---

## 7. Diretriz Fiscal de Responsabilidade do Suporte ATS
* **Regra Inviolável:** A ATS presta suporte ao ERP Resulth e não consultoria tributária.
* **Como redigir:**
  - Oriente sempre o campo da tela onde o cliente deve informar alíquotas ou CST/CSOSN.
  - Explique a lógica sistêmica (Simples Nacional deduz receita bruta; Regime Normal toma crédito de ICMS/PIS/COFINS).
  - Inclua sempre o aviso recomendando que o cliente valide as alíquotas com o contador da empresa.

---

## 8. Curadoria Estrita de Links no "## Veja também" (Regra da Árvore de Conexões)
* **Sintoma:** Um procedimento de qualquer módulo linkando artigos aleatórios ou de outros módulos sem conexão direta com o que o usuário está executando.
* **Causa Raiz:** Preenchimento genérico ou automático sem enxergar a rotina como um nó em um grafo/árvore de processos.
* **Regra Universal da Árvore de Conexões (Válida para Todos os Módulos do ERP):**
  Cada rotina é um **nó de uma árvore** com 4 direções estruturadas de conexão:

```text
                     [ Galho Acima: Raiz / Pré-Requisito ]
                   "O que precisa estar configurado para rodar?"
                     (Cadastros base, Parâmetros, Amarrações fiscais)
                                       │
                                       ▼
 [ Galho Lateral: Alternativa/Tela ] ◄───► [ PROCEDIMENTO ATUAL ] ◄───► [ Conexão Espelho ]
   "Outra forma de fazer ou rotina             (A rotina em foco)          (Seu FEWSHOT irmão)
    que divide a mesma tela no ERP"
                                       │
                                       ▼
                    [ Galho Abaixo: Desdobramento do Ciclo ]
                   "Qual é o próximo passo natural do processo?"
                     (Conclusão, Estorno, Próxima rotina encadeada)
```

* **Aplicação Prática da Árvore em Qualquer Assunto:**
  1. **Galho Acima (Cadastros que Alimentam a Tela):** A tela do sistema exige algum campo ou parâmetro obrigatório? Linke a rotina que cadastra essa informação (ex.: *Tipo de Operação*, *Amarração de CFOP/CST/CSOSN*, *Contas Caixas*, *Parâmetros da Filial*).
  2. **Galho Lateral (Alternativas Operacionais e Telas Compartilhadas):** Outra rotina que atinge o mesmo objetivo de negócio ou **que abre exatamente a mesma tela no Resulth** (ex.: *Painel de Notas* ↔ *Entrada Manual por XML*, pois ambas abrem a tela de *Entrada de Produtos*; *Venda Direta* ↔ *Importação de Pré-Venda*; *Relatório Desktop* ↔ *Relatório Web*).
  3. **Galho Abaixo (Desdobramento Imediato do Fluxo):** O que acontece imediatamente após concluir a rotina com sucesso ou em caso de estorno (ex.: após *Entrada*, a *Confirmação de Estoque* ou *Devolução de Compra*; após *Venda*, o *DANFE/XML*, *Cancelamento* ou *Devolução*).
  4. **Vínculo Espelho:** Todo `PROC` se conecta ao seu `FEWSHOT` de atendimento rápido, e vice-versa.
* **Proibição Rígida de Pular Ramos:** Nunca linkar um galho de outra árvore funcional que não tenha conexão com a rotina que o cliente está operando.

---

## 9. Proibição de Procedimentos de Banco de Dados (SQL, Tabelas, Colunas, Bugcheck, Corrupção) — Transferência Obrigatória
* **O que não deve ser feito:** Colocar comandos SQL (`SELECT`, `INSERT`, `UPDATE`, `DELETE`, `ALTER TABLE`), orientações de IBExpert ou procedimentos de reparo estrutural de tabelas, colunas e banco de dados nos artigos da base ou respostas da IA.
* **Causa Raiz de Risco:** Clientes tentando executar comandos de banco sem conhecimento técnico podem corromper irreversivelmente os dados contábeis, fiscais e de estoque da empresa.
* **Regra de Ouro Inviolável:**
  - Ocorrências envolvendo:
    - **Banco de dados corrompido** (*checksum error*, *wrong page type*, *gfix*);
    - **Bugcheck**, deadlocks ou travamentos do motor de banco de dados;
    - Criação ou recriação de banco de dados a partir de scripts SQL;
    - Divergências estruturais em tabelas, colunas, chaves primárias, triggers ou sequenciais;
  - **NÃO devem ter procedimentos técnicos expostos nos documentos da Tina.**
  - O documento e a assistente virtual devem orientar expressamente a **transferência imediata para a equipe de suporte técnico humano (N1)**, sem citar nomenclaturas técnicas como "DBA" ou "N2" ao cliente, preservando a base de dados com backup prévio de segurança.

---

## 10. Proibição de Notação Matemática LaTeX (`$$...$$` ou `$...$`)
* **Sintoma:** O documento exibe código cru como `$$\text{F12 (Outras Funções)} \longrightarrow \text{F8 (Pesquisar NFC-e)}$$` na tela da Central.
* **Causa Raiz:** O renderizador web do Tina Console e os parsers RAG não possuem suporte a KaTeX/LaTeX para manuais operacionais.
* **Regra de Ouro:** NUNCA use `$$` ou notação matemática LaTeX. Atalhos e menus devem usar Markdown puro e setas limpas:
  - *Correto:* `**F12 (Outras Funções)** » **F8 (Pesquisar NFC-e)**` ou `F12 » F8 (Pesquisar NFC-e)`.
  - *Proibido:* `$$\text{F12} \longrightarrow \text{F8}$$`.

---

## 11. Proibição de Tags HTML Não Renderizadas (`<kbd>`, etc.)
* **Sintoma:** O texto na Central exibe tags cruas como `<kbd>F8</kbd>` ou `<kbd>F8</kbd>)`.
* **Causa Raiz:** O Tina Console não processa a tag `<kbd>`.
* **Regra de Ouro:** NUNCA use `<kbd>`. Indique teclas e botões sempre em negrito Markdown:
  - *Correto:* Tecla **F8**, atalho **F12**, botão **Consultar**, tecla **Enter**.
  - *Proibido:* `<kbd>F8</kbd>`, `<kbd>Enter</kbd>`.

---

## 12. Proibição de Imagens Inline com Caminho Relativo (`![alt](midia/...)`)
* **Sintoma:** O procedimento na Central exibe caixas retangulares com ícone de imagem quebrada e o título da imagem.
* **Causa Raiz:** O visualizador do Tina Console não serve arquivos estáticos através de caminhos relativos locais (`midia/...`) inseridos no corpo do markdown. Além disso, encher o corpo do texto de imagens polui a leitura do operador.
* **Regra de Ouro:**
  - As mídias devem ser cadastradas **exclusivamente no cabeçalho YAML (`midias:`)**, contendo o `id`, a chave oficial no storage (`arquivo: midia/YYYY/MM/...`), a `descricao` e o gatilho `quando_enviar`.
  - O Tina Console gerencia essas mídias automaticamente na aba lateral de Mídias, e a IA Tina as envia pelo chat com a ferramenta `enviar_midia` no momento exato da dúvida.
  - O corpo do documento deve ser 100% textual, limpo e direto, sem tags inline `![...](midia/...)`.

---

## 13. Proibição de Travessão Longo (—, Em Dash) e Hífens como Separadores de Orações (WhatsApp)
* **Sintoma:** A IA envia mensagens com travessões longos (`—`) ou hífens no meio de frases para pausas dramáticas ou explicativas (ex: *"A transmissão é rápida — basta apertar F6 — e a nota vai."*).
* **Causa Raiz:** O travessão longo (`—`) é um vício clássico de alinhamento de modelos de linguagem treinados em literatura e textos formais em inglês. No WhatsApp brasileiro, **ninguém usa travessão longo no celular**. Sua presença entrega imediatamente que o texto foi gerado por robô, afastando o usuário e quebrando o tom humanizado da Tina.
* **Regra de Ouro:**
  - NUNCA use o travessão longo (`—`) nem hífens para fazer pausas ou separar orações.
  - Escreva como um brasileiro digita no celular: use frases curtas separadas por ponto final, vírgulas simples ou parênteses.
  - No prompt do sistema da Tina, mantenha sempre a regra explícita na seção `## Isto aqui e WhatsApp` com exemplos de "Proibido" vs. "Correto":
    - *Proibido:* *"A consulta é simples — você acessa o menu F12 — e verifica o status."*
    - *Correto:* *"A consulta é simples. Acesse o menu F12 e verifique o status."*

---

## 14. Fluxo de Autenticação OAuth PKCE no Tina Console MCP (`auth_tina.js`) e Resolução de Erros
* **Sintomas:**
  - O navegador exibe `ERR_CONNECTION_REFUSED` ao clicar em "Autorizar" no endereço `http://localhost:8080/callback?code=...`.
  - O terminal registra `HTTP 400: {"error":"invalid_grant","error_description":"PKCE não confere"}` e encerra o script.
* **Causas Raiz:**
  1. O navegador tenta entregar o código na porta 8080, mas o servidor local `node scripts/auth_tina.js` não estava rodando ou já tinha sido encerrado.
  2. No protocolo OAuth com PKCE (S256), cada execução gera um par único de `code_verifier` e `code_challenge`. Quando o usuário recarrega uma página de erro anterior (`F5`), o parâmetro `code` enviado pertence a uma sessão anterior que não confere com o novo `code_verifier`.
  3. Se o script fechar o processo (`process.exit(1)`) logo no primeiro erro, a porta 8080 cai antes do usuário autorizar a nova janela aberta.
* **Regra de Ouro e Procedimento de Recuperação:**
  - O servidor `auth_tina.js` não deve morrer no primeiro erro capturado; ele deve continuar ouvindo na porta 8080 e exibir uma mensagem instrutiva na tela.
  - Ao iniciar o script de autenticação, o usuário **deve utilizar a nova aba/janela aberta** (ou abrir o link direto gerado no log do terminal) e clicar em **"Autorizar"**, nunca dando F5 na URL de callback que já falhou.
  - O script deve persistir o token em `mcp_oauth_tokens.json` tanto sob a chave da URL oficial do recurso (`https://mcp.central.atsinformatica.com.br/tina/mcp`) quanto sob o slug `tina`.

---

## 15. Tratamento de Access Violation e Erros Internos de Sistema (Proibição de RAG Forçado)
* **Sintoma:** Ao receber um print com `Access violation at address ... in module 'ResulthBusiness.exe'`, a Tina pesquisa na base, encontra um caso específico de inconsistência de CFOP (`TS-nfe-access-violation-salvar-cabecalho-cfop`) e assume que qualquer Access Violation é erro de CFOP, forçando o cliente a alterar cadastros tributários e gerando atrito por vários turnos até admitir a falha.
* **Causa Raiz:** *Access violation* no Delphi/Windows é um ponteiro nulo (`nil pointer dereference`, ex.: `Read of address 00000014` = offset de 20 bytes a partir de ponteiro nulo). Trata-se de um erro de compilação/código do sistema, campo nulo não tratado no banco ou bloqueio de antivírus. O cliente comum não sabe o que é isso e não tem capacidade de resolver bugs de código.
* **Regra de Ouro:**
  - Erros de sistema como *Access violation*, *Bugcheck*, travamentos de executável e exceções de memória **NÃO são resolvidos pelo cliente**.
  - A Tina NUNCA deve tentar buscar no RAG para associar o erro a cadastros, tributação ou CFOP, nem colocar o cliente para auditar telas do sistema.
  - Ao identificar *Access violation* ou *Bugcheck* (seja em texto ou print), **transferir imediatamente para o Suporte Técnico** via ferramenta `transferir_para_humano`, tranquilizando o cliente com uma resposta curta e cordial.





## 16. Criação ou Edição de Conteúdo sem Validação Técnica e Mineração de Dores no NotebookLM
* **Sintoma:** Procedimento ou few-shot redigido com base em suposições, caminhos de menu imprecisos ou respondendo em linguagem excessivamente técnica/acadêmica que o cliente não compreende ou não busca no RAG.
* **Causa Raiz:** Elaborar conhecimento sem consultar a verdade sistêmica documentada e sem minerar as dúvidas reais que o cliente realmente traz no suporte técnico.
* **Regra de Ouro (O Ciclo de 2 Cadernos no Gemini Notebook / NotebookLM):**
  1. **Validação Técnica (Caderno `🎫 Base de conhecimentos com IA - ATS INFORMATICA` - ID `13cdb6b1-759d-4a52-83ec-56dbfd2ec71a`):**
     - Consultar via `notebook_query` o processo completo oficial, menus superiores (`Windows » ...`), pré-requisitos cadastrais e amarrações de regras.
  2. **Mineração de Linguagem Real e Dores (Caderno `📋 Histórico de Atendimentos do Suporte Técnico` - ID `c308482f-2e83-4986-9d55-10a41081adbe`):**
     - Consultar com o prompt padrão: *"Como o cliente entra em contato a respeito de [TEMA] e quais as maiores dúvidas deles sobre isso? Cite frases e perguntas reais usadas pelos clientes pfv, termos coloquiais, erros relatados e dificuldades mais comuns reportadas ao suporte técnico."*
  3. **Incorporação Direta:**
     - No `PROC`: Popular a tabela de Vocabulário RAG com as frases literais e jargões minerados.
     - No `FEWSHOT`: Elaborar as 6 a 10 perguntas da seção de "Dúvidas Mais Frequentes do Suporte Técnico" com base nas ocorrências reais registradas no histórico.

---

## 17. Chaves de Mídia sem Hash e Erro MinIO NoSuchKey (Ticket #113506)
* **Sintoma:** Cliente recebe ícone de imagem quebrada no Blip Desk / WhatsApp ao ser acionada a ferramenta `enviar_midia`. Ao abrir a URL pré-assinada, o navegador exibe XML `<Error><Code>NoSuchKey</Code><Message>The specified key does not exist.</Message></Error>`.
* **Causa:** O frontmatter do procedimento foi preenchido com nome de arquivo bruto local sem hash (ex: `midia/2026/10/Pesquisa_status_nfe.png`). No MinIO S3, a chave oficial contém hash único de integridade (ex: `midia/2026/10/pesquisa-status-nfe-f05a1681.png`).
* **Prevenção Mandatória:**
  1. Nunca preencher chaves de mídia manualmente.
  2. Sempre utilizar `python ats.py upload-midias` ou `enviar_midia_base64` e copiar a chave com hash.
  3. `python ats.py validar` valida obrigatoriamente regex `-[0-9a-f]{8}`.

---

## 18. Proibição Estrita de Níveis de Suporte ("N1", "N2", "N3", "Tier 2")
* **Sintoma:** O agente redige procedimentos com termos como *"triagem N1/N2"*, *"escalonar para o Suporte N2 / DBA"* ou tenta classificar chamados por níveis técnicos.
* **Causa Raiz:** Vício de linguagem técnica que expõe divisões internas de atendimento desnecessárias para o cliente e para a operação.
* **Regra de Ouro:**
  - **NUNCA citar níveis de suporte** em nenhum documento (`PROC`, `FEWSHOT`, prompt ou mensagem de atendimento).
  - Referir-se sempre de forma profissional e direta como **"Suporte Técnico da ATS"** ou **"equipe de suporte técnico / equipe técnica especializada"**.
  - Frase padrão mandatória em caso de transbordo: *"Este erro indica uma inconsistência estrutural ou de infraestrutura no banco de dados. O procedimento técnico NÃO deve ser executado pelo usuário/cliente. O chamado DEVE ser transferido/escalonado para o **Suporte Técnico da ATS** (pois o mesmo possui competência para tal) para análise e intervenção segura com backup prévio."*

---

## 19. Timeout de 180s em Consultas Síncronas no Gemini Notebook (`notebook_query`)
* **Sintoma:** A chamada `notebook_query` no caderno `📋 Histórico de Atendimentos do Suporte Técnico` falha com `timed out after 180.0s`.
* **Causa Raiz:** O caderno possui 14 fontes com mais de 50 mil atendimentos reais, exigindo tempo de síntese que ultrapassa o timeout de chamadas HTTP síncronas do MCP.
* **Regra de Ouro:**
  - **Sempre utilizar o par assíncrono:**
    1. Iniciar com `notebook_query_start(notebook_id=..., query=..., timeout=180)` para receber o `query_id` imediato.
    2. Aguardar a resposta consultando `notebook_query_status(query_id=...)` via timer/schedule.
  - Isso evita travar o contexto do agente e garante o retorno completo das dores e frases literais dos clientes.

---

## 20. Isolamento de Arquivos Legados em Pasta Dedicada ("melhorias legado/")
* **Sintoma:** Risco de misturar arquivos em revisão com os 54 procedimentos oficiais já homologados, mídias associadas e metadados na raiz de `procedimentos/`.
* **Causa Raiz:** Salvar revisões do acervo legado de 999 páginas diretamente nas pastas de produção local.
* **Regra de Ouro:**
  - **Os nossos 54 procedimentos e few-shots são 100% BLINDADOS E INTOCÁVEIS.**
  - Qualquer trabalho de curadoria, melhoria e revisão das 999 páginas legadas deve ser salvo **obrigatoriamente na pasta `melhorias legado/<slug-ou-numero>/`**.
  - O controle de status e evolução deve ser registrado na 7ª aba do Google Sheets (**`Melhorias e Backlog`**) através de `dados/melhorias_backlog.json`.

---

## 21. Manutenção de Páginas Técnicas como `audience: analyst` (Não Migrar para `all`)
* **Sintoma:** Tentativa equivocada de converter artigos com diagnóstico de rede, DLLs (`midas.dll`), arquivos `.dir`, portas Firebird e comandos avançados para `audience: all`.
* **Causa Raiz:** Acreditar que toda página precisa ser visível para a Tina responder ao cliente no chat.
* **Regra de Ouro:**
  - Páginas marcadas como `analyst` contêm instruções técnicas restritas que **jamais devem ser executadas por clientes leigos no WhatsApp/Chat**.
  - Se a página for puramente técnica/interna, **mantenha estritamente `audience: analyst`**.
  - Ela só deve ser usada para alimentar um procedimento público (`audience: all`) se houver uma rotina operacional correspondente que o cliente execute com segurança pelas telas do ERP, sem mexer em infraestrutura.



### Dificuldade 27: Proibição Estrita de Jargões Internos, Conceitos de IA e Nomes de Tabelas de Banco de Dados (MOVIMEN, MOVIBAN, DOCUREC, etc.) nos Manuais do Cliente
* **O que acontecia:** Inclusão de termos técnicos de desenvolvimento de IA (como *"Vocabulário de suporte e gatilhos de busca (Indexação RAG)"*) e nomes de tabelas físicas de banco de dados (como `MOVIMEN`, `MOVIBAN`, `DOCUREC`, `MOVIREC`) diretamente nos procedimentos operacionais voltados aos clientes e operadores do ERP.
* **O erro gerado:** O cliente e o operador comum não fazem ideia do que é RAG, nem conhecem nomes de tabelas internas como `MOVIMEN` ou `MOVIBAN`. A presença desses termos gera confusão, estranhamento e quebra a naturalidade de um manual de uso, fazendo o procedimento parecer um documento técnico de DBA/programador em vez de um guia prático para o usuário final.
* **Regras de Ouro:**
  1. **Linguagem Operacional, Direta e Humana:** Todo texto nos procedimentos e few-shots deve traduzir comportamentos de sistema na linguagem do dia a dia do operador.
     - *Proibido:* *"Parametrização da operação financeira conforme destino do recurso (MOVIMEN ou MOVIBAN)."*
     - *Correto:* *"Escolha conforme onde o dinheiro vai entrar: no Caixa físico da loja (dinheiro) ou na Conta Bancária da empresa (PIX, boleto, depósito)."*
  2. **Proibição de Conceitos Internos de Engenharia de IA:** NUNCA utilizar títulos ou blocos contendo *"Vocabulário de suporte"*, *"Gatilhos de busca RAG"* ou jargões de LLM nos artigos. Substituir sempre por seções naturais e focadas na dor do usuário, como:
     - *"Dúvidas comuns no dia a dia e quando utilizar esta rotina"*
     - *"Como saber qual rotina utilizar"*
  3. **Blindagem de Nomes de Tabelas e Colunas Físicas:** Tabelas como `DOCUREC`, `MOVIREC`, `MOVIMEN`, `MOVIBAN`, `FILIAIS`, `COMPLNFCE` e afins pertencem exclusivamente à documentação interna de suporte de banco de dados. Nos manuais públicos (`PROC` e `FEWSHOT`), mencione unicamente os nomes de telas, menus, abas e campos que o cliente efetivamente visualiza na interface gráfica do Resulth Windows e Web.

---

## 28. Proibição Estrita de Menções a Custos, Orçamentos, Horas de Desenvolvimento e Relatórios Personalizados
* **O que acontecia:** Ao minerar atendimentos reais do suporte, resumos e procedimentos herdavam frases de analistas sobre desenvolvimento sob encomenda, tais como: *"o relatório personalizado é passível de custo"*, *"o valor depende das horas necessárias para criação"* ou orientações sobre contratação de relatórios customizados com a fábrica.
* **O erro gerado:** A Tina e os procedimentos operacionais da base de conhecimento da ATS destinam-se exclusivamente à operação técnica do ERP Resulth. Menções a cobranças, custos de desenvolvimento, estimativas de horas técnicas ou orçamentos geram atrito desnecessário com os clientes, insegurança comercial e violam a separação entre suporte operacional e negociação comercial.
* **Regras de Ouro:**
  1. **Foco 100% Técnico e Operacional:** Todos os procedimentos (`PROC`) e respostas rápidas (`FEWSHOT`) devem instruir exclusivamente como utilizar os recursos nativos existentes no ERP (como filtros de Somatório de Vendas, configurações de colunas, relatórios existentes e parametrizações).
  2. **Proibição Total de Termos Comerciais e Financeiros da ATS:** NUNCA utilizar termos como *"passível de custo"*, *"horas de desenvolvimento"*, *"cobrança de relatório sob encomenda"*, *"orçamento com a fábrica"* ou *"validação com setor de projetos"*.
  3. **Assunto Estrito do Suporte Humano e Comercial:** Caso o cliente insista que precisa de um cruzamento de dados inexistente no sistema, a orientação deve ser unicamente técnica e neutra (explicar o que o relatório padrão faz). Qualquer menção a viabilidade de desenvolvimento customizado, escopo ou precificação pertence exclusivamente ao suporte técnico humano e aos consultores de contas da ATS.

---

## 29. Processo Mandatório para Criação de Conhecimento a partir de Pastas de Insumos Brutos (Imagens, Transcrições de Vídeo e Curadoria Dupla no NotebookLM)
* **Contexto da Demanda:** O usuário fornece uma pasta local de insumos brutos contendo prints de telas (`.png`) e uma transcrição textual de vídeo (`.txt`), exigindo a criação do procedimento técnico (`PROC`) e da resposta rápida (`FEWSHOT`) com máxima fidelidade e cobertura sistêmica.
* **O erro a evitar:** Criar o procedimento apenas resumindo o `.txt` ou descrevendo superficialmente as imagens sem detalhar todos os campos visíveis, ou omitir as consultas nos cadernos do Gemini Notebook (NotebookLM).
* **Protocolo de Execução Passo a Passo (Obrigatoriedade Estrita):**
  1. **Inspeção Visual Exaustiva de TODAS as Imagens da Pasta:**
     - Inspecionar cada imagem individualmente com as ferramentas visuais (`read_file`).
     - Mapear **todos os campos** existentes na tela (mesmo os secundários ou não marcados): filtros, campos de digitação, caixas de marcação (`checkbox`), colunas nominais de grades e tabelas, e botões de ação do rodapé.
  2. **Leitura Integral da Transcrição do Vídeo:**
     - Ler o arquivo `*_transcricao.txt` na íntegra para absorver as dicas operacionais, contexto prático transmitido pelo analista e armadilhas citadas no treinamento.
  3. **Consulta de Validação Técnica Inicial no Gemini Notebook (Caderno Base de Conhecimento):**
     - **Regra de Ouro da Consulta Prática (Usar o Título da Tela e Listar os Campos):** As imagens da pasta trazem no topo o nome oficial da tela/janela aberta no ERP. A pergunta ao caderno `13cdb6b1...` DEVE ser formulada em duas partes estruturadas:
       1. *O Fluxo Completo:* `"Como é feita a [Nome da Rotina, ex: Liberação de reservas da Pré-Venda] no ERP Resulth? Qual é o procedimento passo a passo completo do início ao fim?"`
       2. *Explicação Campo a Campo (Nome da Tela + Lista de Campos):* `"Na tela [Nome Exato da Janela no Print, ex: Liberação de reservas], explique detalhadamente para que serve e qual a função de cada um dos seguintes campos, filtros, opções e colunas: [Listar todos os campos visíveis da foto, inclusive os não marcados]"`
     - Isso impede respostas abstratas ou de programador e obriga o modelo a explicar a função operacional real de cada elemento da interface.
  4. **Mineração de Dores e Linguagem Real no Gemini Notebook (Caderno Histórico de Atendimentos):**
     - Submeter perguntas direcionadas ao caderno de suporte (`c308482f...`) para minerar as frases literais que os clientes usam no WhatsApp/chat (ex.: *"produto preso na reserva"*, *"mãozinha ofuscada"*), dúvidas mais comuns e relatórios de chamados reais.
  5. **Triangulação Mandatória — Consulta de Resolução das Dúvidas Mineradas na Base de Conhecimentos:**
     - **Regra Vital de Fechamento de Ciclo:** As dúvidas, reclamações e cenários de atrito minerados no Histórico de Atendimentos **DEVEM OBRIGATORIAMENTE ser submetidos como novas perguntas ao caderno da Base de Conhecimentos (`13cdb6b1...`)**.
     - Isso garante que cada dor real do cliente receba a resposta técnica e o procedimento operacional definitivo homologado pelo ERP Resulth (ex.: regras de liberação parcial, tratamento de reservas órfãs e liberação de alçadas).
  6. **Redação do Procedimento (`PROC`) com Estrutura Completa:**
     - **Estimativas de Tempo:** Inserir tempo operacional estimado em cada passo (ex.: *Tempo estimado: 1 minuto*, *30 segundos*).
     - **Mapeamento Nominal de Grades:** Listar cada coluna pelo seu nome exato de tela (ex.: `Sel.`, `Filial`, `Cliente`, `Tipo (F)`, `Documento`, `Núm.Dav/Pv`, `Dt. emissão`, `Produto`, `Descrição`, `Grade`, `Quant.`, `Vendedor`).
     - **Recursos Auxiliares:** Mapear caixas como *"Marca todos"* e botões secundários como *"Limpar"* e *"Sair"*.
     - **Mensagem Exata de Confirmação:** Registrar a frase exata emitida pelo sistema (ex.: *"Reserva(s) liberada(s)."*).
     - **Seção de Dúvidas Frequentes do Suporte Respondidas:** Documentar detalhadamente a resolução das perguntas mineradas que foram respondidas pela Base de Conhecimentos.
     - **Tratamento de Exceções:** Incluir o que fazer se o botão/atalho estiver desabilitado ou ofuscado (permissões de usuário).
     - **Seção "Como verificar se foi bem-sucedido":** Descrever o passo a passo de conferência no sistema que confirma que a rotina funcionou (ex.: dedução do saldo bloqueado no cadastro do produto).
     - **Linguagem:** Português brasileiro formal (tela, estoque, grade, registro, usuário).
  7. **Redação do Exemplo de Atendimento (`FEWSHOT`):**
     - Resposta rápida, concisa e humanizada, orientada a resolver a dor do cliente em até 5 passos, trazendo as frases reais e a seção de Dúvidas Rápidas respondidas.
  8. **Validação Estrutural Local via CLI:**
     - Rodar `python ats.py validar` para garantir conformidade total (zero fórmulas LaTeX `$$`, YAML íntegro e mídias devidamente mapeadas).

---

### Dificuldade 28: Proibição Estrita de Menções a Custos, Orçamentos, Horas de Desenvolvimento e Relatórios Personalizados nos Manuais e Atendimentos
* **O que acontecia:** Ao minerar atendimentos reais do suporte (inclusive no módulo de Lacunas da Base), resumos e procedimentos herdavam frases de analistas sobre desenvolvimento sob encomenda, tais como: *"o relatório personalizado é passível de custo"*, *"o valor depende das horas necessárias para criação"* ou orientações sobre contratação de relatórios customizados com a fábrica.
* **O erro gerado:** A Tina e os procedimentos operacionais da base de conhecimento da ATS destinam-se exclusivamente à operação técnica do ERP Resulth. Menções a cobranças, custos de desenvolvimento, estimativas de horas técnicas ou orçamentos geram atrito desnecessário com os clientes, insegurança comercial e violam a separação entre suporte operacional e negociação comercial.
* **Regras de Ouro:**
  1. **Foco 100% Técnico e Operacional:** Todos os procedimentos (`PROC`) e respostas rápidas (`FEWSHOT`) devem instruir exclusivamente como utilizar os recursos nativos existentes no ERP (como filtros de Somatório de Vendas, configurações de colunas, relatórios existentes e parametrizações).
  2. **Proibição Total de Termos Comerciais e Financeiros da ATS:** NUNCA utilizar termos como *"passível de custo"*, *"horas de desenvolvimento"*, *"cobrança de relatório sob encomenda"*, *"orçamento com a fábrica"* ou *"validação com setor de projetos"*.
  3. **Assunto Estrito do Suporte Humano e Comercial:** Caso o cliente insista que precisa de um cruzamento de dados inexistente no sistema, a orientação deve ser unicamente técnica e neutra (explicar o que o relatório padrão faz). Qualquer menção a viabilidade de desenvolvimento customizado, escopo ou precificação pertence exclusivamente ao suporte técnico humano e aos consultores de contas da ATS.

---

### Dificuldade 29: Processo Mandatório para Criação de Conhecimento a partir de Pastas de Insumos Brutos (Inspeção Visual Exaustiva de Telas e Curadoria Dupla no NotebookLM)

> [!CAUTION] PROIBIÇÃO ABSOLUTA DE DESCRIÇÃO SUPERFICIAL OU INFERÊNCIA DE TELAS
> **É TERMINANTEMENTE PROIBIDO redigir procedimentos baseando-se apenas na transcrição de áudio ou nos nomes dos arquivos de imagem.**
> Vídeos e áudios omitem campos secundários, nomenclaturas exatas de botões e configurações críticas de telas.
> O agente DEVE inspecionar individualmente 100% das imagens com as ferramentas visuais (`read_file`), mapeando de forma exaustiva e literal cada elemento visível antes de redigir qualquer linha de procedimento.

* **Protocolo de Inspeção Visual Obrigatória (Varredura de Cima a Baixo em Cada Imagem):**
  1. **Título Exato da Janela:** Capturar o nome literal na barra de título do Windows (ex.: `Conferência cega de pedidos`, `Configurações`, `Cadastro de Usuários - [Nome do Produto]`);
  2. **Abas e Guias:** Mapear a aba ativa e listar todas as abas secundárias disponíveis (ex.: `Dados Gerais`, `Produtos e Preços`, `Permissões`);
  3. **Campos de Cabeçalho e Filtros:** Identificar campos de digitação, buscas com lupa e filtros de período ou cliente;
  4. **Caixas de Seleção (Checkboxes e Radio Buttons):** Listar **todas** as caixas visíveis na tela, destacando expressamente o estado de cada uma (marcada vs. desmarcada);
  5. **Colunas Nominais de Grades e Tabelas:** Transcrever o nome exato de cada coluna da tabela (ex.: `Produto`, `Descrição`, `Grade`, `Unid.`, `Quantidade`);
  6. **Instruções e Textos de Atalho:** Copiar o texto orientativo de rodapé ou atalhos de teclado (ex.: *"Aperte a tecla [DELETE] sobre um produto para excluí-lo"*);
  7. **Botões de Ação:** Mapear botões principais e secundários (ex.: `Finalizar conferência`, `Cancelar conferência`, `Limpar`, `Sair`, ⚙️ `Configurações`);
  8. **Janelas Modais e Caixas de Diálogo:** Se houver janela sobreposta, detalhar minuciosamente suas opções (ex.: seleção de unidades `KG`, `MT`, `UN`, `PC`, `PCA`, caixas de confirmação `Informação: Conferência efetuada.` ou alertas de bloqueio `Divergência existente`).

* **Protocolo de Consulta ao Gemini Notebook (NotebookLM com a Lista Nominal de Campos):**
  - Cada tela inspecionada visualmente DEVE ser enviada ao caderno oficial da Base de Conhecimento (`13cdb6b1...`) formulada em duas partes estruturadas:
    1. *O Fluxo Completo:* `"Como é feita a rotina [Nome da Tela] no ERP Resulth passo a passo?"`
    2. *A Função de Cada Campo:* `"Na tela [Nome Exato da Janela], explique detalhadamente a função operacional de cada um dos seguintes campos, filtros, caixas de seleção e colunas visíveis: [Lista Exaustiva de Todos os Campos Mapeados na Imagem]"`
  - Isso impede respostas abstratas ou genéricas e garante que o manual contenha a explicação técnica homologada para 100% dos botões e caixas da tela.
  - **Triangulação com o Histórico de Suporte:** As dúvidas mineradas no Histórico de Atendimentos (`c308482f...`) devem ser trianguladas de volta à Base de Conhecimento para obter as soluções oficiais de contorno.

* **Redação e Validação:**
  - O procedimento (`PROC`) deve mapear nominalmente cada campo e grade, conter estimativas de tempo em cada passo e trazer o teste de sucesso.
  - O exemplo de atendimento (`FEWSHOT`) deve responder diretamente à dor em 5 passos com 8 a 10 dúvidas frequentes do suporte.
  - Validação estrita via `python ats.py validar` antes de qualquer entrega.

### Dificuldade 30: Autenticação da CLI do Gemini Notebook (`nlm login`) no Windows: Proibição de Execução em Prompt Elevado (Administrador)

* **O que acontece:** Ao executar `nlm login` em um terminal aberto como Administrador (`Administrador: Prompt de Comando` ou `PowerShell com privilégios elevados`), o comando realiza o fluxo no navegador, mas o Windows salva o arquivo `credentials.enc` com propriedade exclusiva do Administrador e criptografa o envelope via DPAPI (`CryptProtectData`) atrelada ao token de segurança elevado.
* **O erro gerado:**
  1. Quando o Antigravity ou o servidor MCP da IDE (que rodam como processo de usuário normal, não-elevado) tentam ler o perfil em `~/.notebooklm-mcp-cli/profiles/default/`, o Windows bloqueia o acesso ao arquivo com:
     `PermissionError: [Errno 13] Permission denied: '.../credentials.enc'`
  2. Como consequência, a biblioteca de credenciais não consegue decodificar o envelope e lança:
     `CorruptCiphertextError: Envelope is corrupt or not valid JSON`
* **Regra de Ouro Inviolável:**
  - **NUNCA execute `nlm login` em terminal de Administrador.**
  - O comando `nlm login` DEVE ser executado **exclusivamente em um terminal de usuário comum** (Prompt de Comando ou PowerShell normal).
  - Caso o login tenha sido realizado acidentalmente como Administrador:
    - *Solução 1 (Rápida na janela Admin aberta):* Conceda permissão total ao usuário atual executando:
      `icacls "%USERPROFILE%\.notebooklm-mcp-cli\profiles\default\*" /grant "%USERNAME%":F /T`
    - *Solução 2 (Definitiva):* Na janela de Administrador, delete o arquivo corrompido com `del "%USERPROFILE%\.notebooklm-mcp-cli\profiles\default\credentials.enc"`, abra um terminal comum de usuário e rode `nlm login` novamente.
