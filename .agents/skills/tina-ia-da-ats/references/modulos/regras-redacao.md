### Dificuldade 7: Diretriz Fiscal Cautelosa do Suporte ATS
* **Regra de Ouro:** A ATS é uma empresa de software ERP, não uma consultoria contábil. A Tina e os procedimentos **nunca devem prescrever alíquotas ou cravar enquadramentos tributários**. Devem sempre orientar o campo sistêmico onde a informação é digitada e orientar que o cliente valide o espelho com a contabilidade da empresa.

### Dificuldade 9: Proibição Estrita de Fórmulas / Notação Matemática LaTeX (`$$...$$` ou `$...$`)
* **O que acontecia:** Uso de blocos matemáticos LaTeX como `$$\text{F12 (Outras Funções)} \longrightarrow \text{F8 (Pesquisar NFC-e)}$$` para representar sequências de teclas ou caminhos de menus.
* **O erro gerado:** O visualizador do Tina Console e os motores de busca RAG não compilam KaTeX/LaTeX para manuais de ERP. O texto aparecia cru e ilegível com `\text`, `\longrightarrow` e `$$`, gerando poluição visual inaceitável.
* **Regra de Ouro:** **É TERMINANTEMENTE PROIBIDO usar LaTeX.** Toda sequência de atalhos, teclas ou menus deve ser escrita em Markdown limpo, direto e legível:
  - *Correto:* `**F12 (Outras Funções)** » **F8 (Pesquisar NFC-e)**` ou `F12 » F8 (Pesquisar NFC-e)`.
  - *Proibido:* `$$\text{F12} \longrightarrow \text{F8}$$` ou qualquer expressão com cifrões `$`.

### Dificuldade 10: Proibição de Tags HTML Não Renderizadas (`<kbd>`, etc.)
* **O que acontecia:** Uso de tags HTML como `<kbd>F8</kbd>` para representar teclas do teclado.
* **O erro gerado:** O parser do Tina Console não estiliza `<kbd>`, exibindo a tag crua no texto (`<kbd>F8</kbd>` ou `<kbd>F8</kbd>)`), prejudicando a leitura do analista e do cliente.
* **Regra de Ouro:** **É TERMINANTEMENTE PROIBIDO usar `<kbd>`.** Teclas de atalho e botões devem usar negrito Markdown padrão:
  - *Correto:* Tecla **F8**, atalho **F12**, botão **Consultar**, tecla **Enter**.
  - *Proibido:* `<kbd>F8</kbd>`, `<kbd>Enter</kbd>`.

### Dificuldade 13: Proibição de Travessão Longo (—, Em Dash) e Hífens como Separadores de Orações (WhatsApp)
* **O que acontecia:** A IA enviava mensagens com travessões longos (`—`) ou hífens no meio de frases para pausas dramáticas ou explicativas (ex: *"A transmissão é rápida — basta apertar F6 — e a nota vai."*).
* **O erro gerado:** O travessão longo (`—`) é um traço estilístico característico de modelos de linguagem treinados em literatura e ensaios formais em inglês. No WhatsApp brasileiro, **ninguém digita travessão longo no celular**. Sua presença entrega de imediato que a mensagem foi gerada por um robô ou copiada de uma IA, quebrando a naturalidade do atendimento humanizado.
* **Regra de Ouro:**
  - **NUNCA use o travessão longo (`—`) nem hífens para fazer pausas ou separar orações.**
  - Escreva como um brasileiro digita no celular: use frases curtas separadas por ponto final, vírgulas simples ou parênteses.
  - A regra deve constar expressamente no prompt do sistema sob a seção `## Isto aqui e WhatsApp` acompanhada de exemplos de "Proibido" vs. "Correto":
    - *Proibido:* *"A consulta é simples — você acessa o menu F12 — e verifica o status."*
    - *Correto:* *"A consulta é simples. Acesse o menu F12 e verifique o status."*

### Dificuldade 19: Proibicao Estrita de Comparacao Cruzada de Relatorios e Escopo do Suporte Tecnico
* **O que acontece:** O cliente ou contador compara relatorios distintos (ex.: Resumo Comercial de Vendas com Livro Fiscal de Saidas, ou Livro de Saidas com SPED Fiscal) e abre chamado alegando divergencia de valores no sistema.
* **Causa Real:** Cada relatorio do ERP Resulth possui dezenas de filtros parametricos independentes (CFOPs, CSTs, modelos 55 vs 65, cancelamentos, devolucoes, datas de emissao vs saida). Um unico filtro marcado de forma diferente entre duas telas ja gera divergencias naturais de valores. Alem disso, devolucoes abatem faturamento comercial mas entram como credito no Livro de Entradas.
* **Diretriz Mandatoria de Atendimento:**
  1. **Nao Recomendacao de Cruzamento de Relatorios:** A Tina e o suporte NUNCA devem incentivar nem realizar comparacao direta entre relatorios diferentes.
  2. **Escopo do Suporte:** O **Suporte Tecnico da ATS NAO faz conciliacao nem auditoria contabil de valores**. A responsabilidade pela analise e fechamento e exclusivamente do usuario junto ao seu escritorio de contabilidade.
  3. **Quando o Suporte Atua:** Somente quando houver **suspeita de falha tecnica real no sistema** (ex.: uma nota fiscal autorizada na SEFAZ que comprovadamente nao consta no XML exportado, cupons fiscais de um caixa especifico que nao integraram no servidor por servico Apache parado, ou erro de execucao de banco de dados ao emitir o relatorio).

### Dificuldade 23: Proibicao Estrita de Paths Locais (file:///, C:\Users\...) e Uso Exclusivo de Wikilinks Canonicos ([[...]])
* **O que acontece:** Insercao de links no formato `file:///C:/Users/paulo.magalhaes/...` ou caminhos absolutos do sistema de arquivos local no corpo dos documentos Markdown (`PROC` e `FEWSHOT`).
* **O erro gerado:**
  1. Vazamento inaceitavel da estrutura de diretorios e dados pessoais do usuario nos arquivos da base de conhecimento da ATS.
  2. O visualizador web do Tina Console e os motores de busca RAG da Tina nao reconhecem esquemas `file:///`, gerando links quebrados ou textos inuteis para clientes e operadores no WhatsApp e Web.
* **Regra de Ouro Inviolavel:**
  - **E TERMINANTEMENTE PROIBIDO usar `file:///`, `C:\Users\...` ou qualquer caminho absoluto de maquina local dentro dos arquivos Markdown da base.**
  - **Links entre paginas no corpo do texto:** Devem utilizar exclusivamente a sintaxe nativa de **wikilinks do Tina Console**:
    - *Correto:* `[[procedimentos/<slug-remoto>|Titulo Visivel]]` ou `[[few-shot/<slug-remoto>|Titulo Visivel]]`.
    - *Exemplo:* `[[procedimentos/geracao-exportacao-xml-nfe-nfce-nfse-contabilidade-windows-web|Procedimento de Geracao e Exportacao de XML de Notas Fiscais para Contabilidade]]`.
    - *Proibido:* `[Procedimento](file:///C:/Users/...)`.
  - **Vinculos estruturais no Frontmatter YAML:** Devem utilizar estritamente o bloco `relations:` com o `id` canonico da pagina alvo:
    ```yaml
    relations:
      - tipo: "procedimento_relacionado" # ou detalha_procedimento
        alvo: "PROC-geracao-exportacao-xml-nfe-nfce-nfse-contabilidade-windows-web"
    ```

### Dificuldade 24: Proibicao Estrita de Diagramas/Artes em ASCII e Caracteres Especiais de Controle (Prevencao de Corrupcao UTF-8/CP1252)
* **O que acontece:** Tentativa de desenhar caixas, fluxogramas, setas ou linhas em ASCII art (como cantos, barras verticais e setas) dentro de procedimentos operacionais.
* **O erro gerado:**
  1. No Windows PowerShell, a interpretacao de caracteres especiais e codigos de escape corrompe a codificacao de bytes em UTF-8 / CP1252.
  2. O arquivo e gravado com caracteres de controle binarios nao-imprimiveis (como Backspace `BS`, `STX`, `SUB`, `DC2`), gerando blocos vermelhos de erro na interface grafica do editor e corrompendo a leitura do arquivo.
* **Regra de Ouro Inviolavel:**
  - **E TERMINANTEMENTE PROIBIDO desenhar fluxogramas ou caixas em ASCII art nos arquivos Markdown.**
  - Fluxos operacionais e etapas de decisao devem ser representados unicamente por:
    - **Listas numeradas e estruturadas em topicos claros** (ex.: `Etapa 1`, `Etapa 2`, `Cenario A`, `Cenario B`).
    - **Tabelas Markdown padrao**.
    - **Diagramas Mermaid** validos com blocos ```mermaid ... ``` (quando suportado pelo console).
  - Toda escrita de arquivos deve ser realizada com scripts seguros garantindo codificacao `utf-8` explicita e validacao de integridade contra bytes de controle.

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

### Dificuldade 28: Proibição Estrita de Menções a Custos, Orçamentos, Horas de Desenvolvimento e Relatórios Personalizados nos Manuais e Atendimentos
* **O que acontecia:** Ao minerar atendimentos reais do suporte (inclusive no módulo de Lacunas da Base), resumos e procedimentos herdavam frases de analistas sobre desenvolvimento sob encomenda, tais como: *"o relatório personalizado é passível de custo"*, *"o valor depende das horas necessárias para criação"* ou orientações sobre contratação de relatórios customizados com a fábrica.
* **O erro gerado:** A Tina e os procedimentos operacionais da base de conhecimento da ATS destinam-se exclusivamente à operação técnica do ERP Resulth. Menções a cobranças, custos de desenvolvimento, estimativas de horas técnicas ou orçamentos geram atrito desnecessário com os clientes, insegurança comercial e violam a separação entre suporte operacional e negociação comercial.
* **Regras de Ouro:**
  1. **Foco 100% Técnico e Operacional:** Todos os procedimentos (`PROC`) e respostas rápidas (`FEWSHOT`) devem instruir exclusivamente como utilizar os recursos nativos existentes no ERP (como filtros de Somatório de Vendas, configurações de colunas, relatórios existentes e parametrizações).
  2. **Proibição Total de Termos Comerciais e Financeiros da ATS:** NUNCA utilizar termos como *"passível de custo"*, *"horas de desenvolvimento"*, *"cobrança de relatório sob encomenda"*, *"orçamento com a fábrica"* ou *"validação com setor de projetos"*.
  3. **Assunto Estrito do Suporte Humano e Comercial:** Caso o cliente insista que precisa de um cruzamento de dados inexistente no sistema, a orientação deve ser unicamente técnica e neutra (explicar o que o relatório padrão faz). Qualquer menção a viabilidade de desenvolvimento customizado, escopo ou precificação pertence exclusivamente ao suporte técnico humano e aos consultores de contas da ATS.

