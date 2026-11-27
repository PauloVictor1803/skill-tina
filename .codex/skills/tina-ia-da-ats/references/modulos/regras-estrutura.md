### Dificuldade 2: Arquivos Trocados / Invertidos (PROC vs FEW-SHOT)
* **O que aconteceu no passado:** O conteúdo extenso do Procedimento (29 KB) foi parar no caminho do Few-Shot no Tina, e o Few-Shot (6 KB) no caminho do Procedimento.
* **Regra de Ouro:** Antes de salvar qualquer página, valide:
  - Se o caminho começa com `procedimentos/`, o frontmatter deve ter `type: procedimento` e tamanho esperado (>15 KB).
  - Se o caminho começa com `few-shot/`, o frontmatter deve ter `type: few-shot` e tamanho esperado (<12 KB).

### Dificuldade 3: Slugs Remotos vs Nomes Locais e Links Quebrados (`[[...]]`)
* **O que acontece:** Se você usar o nome do arquivo local (ex: `[[procedimentos/PROC-cancelamento-de-faturamento...]]`), o Tina Console renderiza o link como **quebrado (em vermelho)**.
* **Regra de Ouro:** O link wiki da Tina deve usar o slug remoto real da página (sem `.md`).
  - *Correto:* `[[procedimentos/procedimento-de-cancelamento-de-faturamento-nf-e-pre-venda-e-varios-pedidos-no-r|Cancelamento de Faturamento...]]`
  - *Incorreto:* `[[procedimentos/PROC-cancelamento-de-faturamento-nfe-pre-venda-e-pedidos-resulth-windows]]`

### Dificuldade 6: Estrutura Modular Local Autocontida vs Estrutura Flat Remota
* **Diretriz Oficial no Repositório Local (Seu Computador):** 
  Todo tema deve ser um **módulo isolado e autocontido** dentro de `procedimentos/<slug-do-assunto>/`.
  Exemplo padrão:
  ```
  procedimentos/emissao-nfe-devolucao-de-venda/
  ├── PROC-emissao-nfe-devolucao-de-venda-cliente-resulth-windows.md  (Guia operacional aprofundado)
  ├── FEWSHOT-como-emitir-nota-fiscal-de-devolucao-de-venda-...md      (Resposta rápida da Tina)
  ├── 17. NF-e Devolução de Venda_transcricao.txt                     (Transcrição de vídeo/aula)
  └── *.png                                                           (Imagens e prints originais da rotina)
  ```
  *Vantagens:* Manutenção centralizada, fácil auditoria e sincronia imediata entre Procedimento e Few-Shot.
* **No Tina Console (Remoto):** Todas as páginas residem no formato flat exigido pelo indexador: `procedimentos/<slug>.md` e `few-shot/<slug>.md`. Nossos scripts de deploy cuidam da distribuição automática na nuvem a partir da pasta local.

### Dificuldade 8: Links do 'Veja Também' Desconexos (A Regra da Árvore de Conexões)
* **O que acontecia:** Preenchimento de links aleatórios ou desconexos da jornada do operador (ex.: linkar rotinas de venda em compras, ou relatórios sem vínculo com a tela).
* **Regra Universal da Árvore de Conexões (Knowledge Tree):** Em **qualquer tema ou módulo do ERP Resulth**, a base de conhecimento funciona como uma **árvore orgânica de processos onde cada rotina é um nó conectado estritamente aos seus ramos diretos**:

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

* **Como aplicar a árvore em qualquer cenário do ERP:**
  1. **Galho Acima (Cadastros e Parâmetros que Alimentam a Tela):** A tela do sistema exige algum campo obrigatório? O usuário precisa entender onde cadastra aquilo (ex.: em *Entrada de Notas*, vincula *Tipo de Operação* e *Amarrações de CFOP/CST*; em *Vendas*, vincula *Tipo de Operação de Venda*; em *Financeiro*, vincula *Conta Caixa e Forma de Pagamento*).
  2. **Galho Lateral (Alternativas Operacionais e Telas Compartilhadas):** Se a rotina falhar, cair ou o operador quiser outro caminho para o mesmo objetivo (ex.: *Painel de Notas* ↔ *Entrada Manual via XML*, pois abrem a mesma tela de *Entrada de Produtos*; ou *Venda Direta* ↔ *Importação de Pré-Venda*; ou *Relatório Desktop* ↔ *Relatório Web*).
  3. **Galho Abaixo (Desdobramento Imediato do Processo):** O que acontece imediatamente após concluir a rotina com sucesso ou em caso de estorno (ex.: após *Entrada*, a *Confirmação de Estoque* ou *Devolução ao Fornecedor*; após *Venda*, a *Emissão do DANFE*, *Envio de XML* ou *Cancelamento*).
  4. **Vínculo Espelho Obrigatório:** O `PROC` detalhado aponta sempre para o seu `FEWSHOT` de resposta rápida, e o `FEWSHOT` aponta de volta para o seu `PROC`.
* **Regra de Isolamento dos Ramos:** **Nunca cruzar galhos de árvores distintas.** Cada artigo deve navegar exclusivamente na jornada funcional de quem está operando aquela rotina.

### Dificuldade 17: Isolamento Mandatório do Acervo Legado em Pasta Própria (`melhorias legado/`)
* **O que acontece:** Misturar revisões com os procedimentos e few-shots homologados da ATS causa confusão de versões e risco de sobrescrita acidental.
* **Regra de Ouro:** Os nossos 54 procedimentos oficiais são 100% blindados e intocáveis. Qualquer procedimento ou few-shot do acervo legado revisado deve ser salvo exclusivamente dentro da pasta `melhorias legado/<slug-ou-numero>/` e controlado pela 7ª aba do Google Sheets (**`Melhorias e Backlog`**).

### Dificuldade 25: Obrigatoriedade de Amarracao Bilateral da Arvore de Conhecimento (PROC <-> FEWSHOT e Rotinas Oficiais)
* **O que acontece:** O analista ou agente cria a relacao ou o link do procedimento oficial ou do procedimento irmao apenas no `FEWSHOT`, deixando o `PROC` sem vinculos ou sem a secao `## Veja Tambem`.
* **O erro gerado:**
  1. Quando o motor RAG da Tina recupera diretamente o `PROC` (ou quando um analista de suporte consulta o procedimento longo no Tina Console), o agente **nao enxerga a conexao** com a resposta rapida do `FEWSHOT` nem com os procedimentos ilustrados oficiais do acervo.
  2. A arvore de conhecimento fica "unilateral" e orfa, impedindo a navegacao fluida e o salto semantico entre a visao conceitual e o passo a passo ilustrado com fotos.
* **Regra de Ouro Inviolavel (Amarracao Bilateral Completa):**
  - **Toda relacao DEVE ser bilateral (espelhada em ambos os documentos):**
    - **No `PROC`:**
      - YAML `relations:` deve apontar para o ID do `FEWSHOT` irmao (`tipo: "exemplo_atendimento"`) e para os procedimentos oficiais correlatos (`tipo: "procedimento_relacionado"`).
      - No corpo do texto: secao **`## Veja Também`** obrigatoria no final contendo os wikilinks canonicos:
        `* [[few-shot/<slug-remoto>|Exemplo de Atendimento (Resposta Rápida)]]`
        `* [[procedimentos/<slug-oficial>|<Titulo Oficial Ilustrado>]]`
    - **No `FEWSHOT`:**
      - YAML `relations:` deve apontar para o ID do `PROC` irmao (`tipo: "detalha_procedimento"`) e para os procedimentos oficiais correlatos (`tipo: "procedimento_relacionado"`).
      - No corpo do texto: secao **`## Veja Também`** obrigatoria no final contendo os wikilinks canonicos:
        `* [[<doc-id-proc>|Procedimento Operacional Completo]]`
        `* [[procedimentos/<slug-oficial>|<Titulo Oficial Ilustrado>]]`

### Dificuldade 26: Padrão Mandatório de Apresentação de Pacotes de Revisão (Tabela de Panorama Consolidado)
* **Diretriz Mandatória do Paulo Magalhães:** Ao concluir a elaboração de pacotes de melhorias, curadorias ou novas revisões na base de conhecimento, a entrega final no chat DEVE SEMPRE conter a tabela de panorama consolidado com links diretos clicáveis para os arquivos físicos locais.
* **Estrutura Obrigatória da Tabela:**
  - Título padronizado: `📦 Panorama Consolidado das X Novas Revisões Locais (Nenhuma Publicada)` (ou o status correspondente da rodada).
  - Subtítulo com link de navegação: `Todas as X pastas locais estão prontas para sua conferência em 📄 [melhorias legado/](file:///...) :`
  - Colunas da Tabela:
    1. **Revisão:** Identificador da revisão (`Rev 20`, `Rev 21`, etc.).
    2. **Tema / Tópico:** Descrição concisa e objetiva do problema sistêmico e da rotina tratada.
    3. **Atendimento de Origem:** Ticket sequencial e identificador do cliente real que originou a demanda (ex.: `Ticket 114506`).
    4. **Procedimento Técnico (`PROC`):** Link direto clicável no formato `[PROC](file:///c:/Users/paulo.magalhaes/Desktop/Base%20de%20dados%20IA%20-%20ats/melhorias%20legado/.../PROC-....md)`.
    5. **Resposta Rápida (`FEWSHOT`):** Link direto clicável no formato `[FEWSHOT](file:///c:/Users/paulo.magalhaes/Desktop/Base%20de%20dados%20IA%20-%20ats/melhorias%20legado/.../FEWSHOT-....md)`.
* **Regra de Acompanhamento:** Apresentar a discriminação detalhada dos pontos de atrito identificados nos atendimentos reais antes ou após a tabela, permitindo ao usuário conferir a lógica de suporte e aprovar a publicação remota apenas quando der o aval explícito.

