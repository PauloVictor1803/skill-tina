### Dificuldade 22: Prevencao de Travamentos de Buffer no IDE (Substituicao de Grandes Blocos)
* **O que acontece:** O uso de ferramentas de substituicao de arquivos no editor em arquivos com centenas de linhas ou contendo scripts em bloco pode causar travamento de buffer no Windows.
* **Solucao Operacional:** Para manipulacao de arquivos medios e grandes, utilizar scripts de execucao direta via Python ou Node.js a partir da raiz do repositorio, garantindo escrita atomica com codificacao UTF-8 explicita sem onerar o buffer do editor.

### Dificuldade 29: Processo Mandatório para Criação de Conhecimento a partir de Pastas de Insumos Brutos (Inspeção Visual Exaustiva de Imagens e Validação Campo a Campo no NotebookLM)

> [!CAUTION] VITAL: OBRIGATORIEDADE DE INSPEÇÃO VISUAL ATÔMICA E CONSULTA ELEMENTO A ELEMENTO
> **É TERMINANTEMENTE PROIBIDO redigir procedimentos baseando-se apenas na transcrição de áudio, em manuais genéricos ou nos nomes dos arquivos de imagem.**
> Em ERPs densos como o Resulth Windows, **a interface visual das imagens é a única fonte definitiva da verdade operacional**. Vídeos e áudios omitem parâmetros críticos, pequenas caixas de seleção que alteram completamente o comportamento da rotina, regras de validação (ex.: código interno vs. referência de barras), seletores de unidades de medida, mensagens modais de advertência e atalhos de teclado (ex.: tecla Delete para excluir itens).
> Qualquer tentativa de redigir sem a inspeção visual prévia e sem o envio dos elementos visuais mapeados ao NotebookLM é considerada **alucinação operacional grave e reprova imediatamente o procedimento**.

* **Por que a Análise Visual Detalhada é Estritamente Crítica:**
  - Manuais de ERP exigem precisão cirúrgica: se o procedimento esquecer de citar que uma caixa deve ser marcada, que certa unidade exige bipagem unitária ou que um leitor busca pelo campo Referência e não pelo Código do Produto, o cliente final travará a operação da empresa, gerando filas no caixa, recusa de importação de NF-e e abertura de chamados urgentes no suporte.
  - O agente DEVE inspecionar individualmente 100% das imagens com as ferramentas visuais (`read_file`), identificando todos os componentes da interface antes de formular qualquer pergunta ou redigir qualquer parágrafo.

* **Etapa 1: Protocolo de Inspeção Visual Obrigatória (Varredura de Cima a Baixo em Cada Imagem):**
  1. **Título Exato da Janela:** Capturar o nome literal na barra de título do Windows (ex.: `Conferência cega de pedidos`, `Configurações`, `Cadastro de Usuários - [Nome do Produto]`);
  2. **Abas e Guias:** Mapear a aba ativa e listar todas as abas secundárias disponíveis (ex.: `Dados Gerais`, `Produtos e Preços`, `Permissões`);
  3. **Campos de Cabeçalho e Filtros:** Identificar campos de digitação, buscas com lupa e filtros de período, status ou cliente;
  4. **Caixas de Seleção (Checkboxes e Radio Buttons):** Listar **todas** as caixas visíveis na tela, destacando expressamente o estado de cada uma (marcada vs. desmarcada);
  5. **Colunas Nominais de Grades e Tabelas:** Transcrever o nome exato de cada coluna da tabela (ex.: `Produto`, `Descrição`, `Grade`, `Unid.`, `Quantidade`);
  6. **Instruções e Textos de Atalho no Rodapé:** Copiar o texto orientativo de rodapé ou atalhos de teclado (ex.: *"Aperte a tecla [DELETE] sobre um produto para excluí-lo"*);
  7. **Botões de Ação:** Mapear botões principais e secundários (ex.: `Finalizar conferência`, `Cancelar conferência`, `Limpar`, `Sair`, ⚙️ `Configurações`);
  8. **Janelas Modais e Caixas de Diálogo:** Se houver janela sobreposta ou alerta popup, detalhar minuciosamente suas opções (ex.: seleção de unidades `KG`, `MT`, `UN`, `PC`, `PCA`, caixas de confirmação `Informação: Conferência efetuada.` ou alertas de bloqueio `Divergência existente`).

* **Etapa 2: Protocolo de Consulta ao Gemini Notebook (Perguntar a Função de Cada Elemento Visual Mapeado):**
  - Após inspecionar as imagens e ter em mãos a lista completa de elementos visuais, o agente DEVE consultar o caderno oficial da Base de Conhecimento (`13cdb6b1...`) enviando a lista exata dos itens identificados na imagem:
    1. *O Fluxo Completo:* `"Como é feita a rotina [Nome da Tela] no ERP Resulth passo a passo?"`
    2. *A Função Específica de Cada Elemento Visual:* `"Na tela [Nome Exato da Janela], explique detalhadamente a função operacional, o comportamento de sistema e as regras de negócio de cada um dos seguintes elementos visíveis na imagem: [Lista Exaustiva de Todos os Campos, Caixas de Seleção, Colunas e Botões Mapeados nas Telas]"`
  - **Isso impede respostas genéricas e garante que o manual contenha a explicação técnica homologada para 100% dos botões e caixas da tela.**
  - **Triangulação com o Histórico de Suporte:** Consultar o caderno de Atendimentos Reais (`c308482f...`) para mapear as dores, frases coloquiais e erros reportados pelos clientes (ex.: ativação acidental, confusão entre código e referência, margem de impressão cortada, bloqueio de checkout), trazendo as soluções oficiais de contorno.

* **Etapa 3: Redação Técnica e Validação Rigorosa:**
  - O procedimento (`PROC`) deve mapear nominalmente cada campo e grade, conter estimativas de tempo em cada passo, detalhar cenários de divergência e trazer o teste de sucesso.
  - O exemplo de atendimento (`FEWSHOT`) deve responder diretamente à dor em 5 passos com 8 a 12 dúvidas frequentes mineradas do suporte real.
  - Validação estrita via `python ats.py validar` antes de qualquer entrega.

