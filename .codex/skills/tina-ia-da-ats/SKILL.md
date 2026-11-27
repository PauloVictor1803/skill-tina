---
name: tina-ia-da-ats
description: "Gerenciar conhecimento e evals da Tina no Tina Console: criar, revisar e testar casos com critérios semânticos equilibrados; auditar, enriquecer e publicar procedimentos, few-shots e mídias do ERP Resulth ATS, usando validação técnica e rastreabilidade. Não usar para migrações de banco de dados."
metadata:
  icon: "📚"
---

# Tina ATS: conhecimento, auditoria e evals

## Como usar esta skill sem inflar o contexto

Esta entrada contém regras permanentes e um índice de referências. Os detalhes originais foram preservados em `references/modulos/`. Não leia todas as referências nem releia esta entrada quando ela já estiver no contexto.

1. Identifique a tarefa e abra somente os módulos necessários da tabela abaixo. Todos os caminhos da tabela partem de `references/modulos/`, dentro desta skill.
2. Se precisar de uma regra pontual, localize a seção com busca por título e leia apenas esse trecho. Não carregue o catálogo inteiro de MCPs ou os guias legados por padrão.
3. Reutilize informações já obtidas nesta sessão. Reconsulte somente se puderem ter mudado ou se faltar evidência.
4. Em Code Mode, processe resultados grandes dentro de `execute` e devolva apenas campos necessários. Por exemplo: validação = `ok`, `erros`, `avisos`; eval = ID, status, passou/falhou e motivo. Não devolva o prompt efetivo inteiro quando só precisar validar.
5. Inspecione configuração e conversa apenas no grau de detalhe necessário. Em auditoria, preserve mensagem, resposta, ferramentas, evidências e trechos que fundamentam o diagnóstico. Não elimine evidências para economizar tokens.
6. Busque ferramentas pelo nome exato e limite resultados. Nunca imprima segredos, credenciais nem conteúdo de autenticação.
7. Se o usuário pedir métricas dos SEUS tokens, meça a sessão do OpenCode, não os atendimentos da Tina. Separe entrada nova, cache, saída e raciocínio. Caracteres não são tokens; não alegue decomposição exata sem telemetria.

## Regras permanentes de autorização e segurança

- Alterações em prompts, guardrails, skills, modelos, versões e fluxos de agentes exigem autorização prévia, explícita e direta de **Paulo Magalhães**. Apresente a proposta/diff e aguarde a autorização. Isso inclui gravar rascunhos. Leitura e auditoria são permitidas.
- Gravar e ativar são operações distintas. Não ative uma versão nem publique páginas sem autorização correspondente. Prefira gravar sem ativar, validar e testar antes da decisão de ativação. Não descarte alterações de outras pessoas.
- Não disponibilize SQL, comandos de banco, reparo de tabelas, índices, generators ou procedimentos de corrupção/bugcheck para clientes ou em PROC/FEWSHOT. Access violation, bugcheck e corrupção exigem encaminhamento ao **Suporte Técnico**, sem tentar associar cegamente a cadastros. Não cite níveis de suporte ou DBA ao cliente.
- Não exponha senhas nem peça que sejam compartilhadas. Falta de permissão não justifica transferir imediatamente: verifique acesso ao MESTRE/responsável autorizado; mesmo com suporte remoto, a autenticação deve ser autorizada pelo cliente. Consulte `regras-permissoes.md` para o fluxo completo.
- Não invente menus, regras fiscais, telas, chaves de mídia ou comportamento do ERP. Fundamente em evidência da rotina e módulo corretos. Não prescreva enquadramento ou alíquota; dados fiscais devem ser confirmados pela contabilidade.
- Documentação puramente técnica permanece `audience: analyst`. Não transforme infraestrutura em orientação para cliente leigo.
- A pasta oficial da base é `\\ATS-MOC-01290\Users\paulo.magalhaes\Desktop\Base de dados IA - ats`. Não copie nem altere a base sem pedido explícito. Trabalhe nela quando autorizado, sem recriar estrutura paralela em C:\Tina. Se inacessível, informe o bloqueio.
- Os procedimentos oficiais são protegidos. Revisões do legado ficam em `melhorias legado/<slug-ou-numero>/`. Não publique automaticamente; preserve a revisão humana.
- Conteúdo original adicional de governança: `governanca-original.md`, `seguranca.md` e `regras-seguranca.md`. Abra se a tarefa exigir detalhes além destas regras.

## Escolha somente as referências da tarefa

| Tarefa | Módulos necessários |
| :--- | :--- |
| Auditar conversa, erro ou transferência | `conversas.md`; acrescente `regras-permissoes.md` para MESTRE/permissões |
| Criar ou revisar evals; testar candidata | `evals.md` |
| Criar/editar PROC ou FEWSHOT | `procedimentos.md` e/ou `fewshots.md`, `regras-redacao.md`, `regras-estrutura.md`, `regras-visibilidade.md` |
| Criar conhecimento a partir de prints, vídeos ou transcrições | Módulos de redação acima + `regras-insumos.md` |
| Upload, envio ou correção de mídia | `midias.md`, `regras-midias.md` |
| Manter fichas e imagens em `rotinas/telas/` | Skill `tina-catalogo-telas-upload`, além de `midias.md` e `regras-midias.md` |
| Validação técnica/mineração no NotebookLM | `notebook.md`, `regras-notebook.md` |
| Preparar/publicar/sincronizar conhecimento | `publicacao.md`, `cli.md`; regras de redação/estrutura/mídia conforme as mudanças |
| Lacuna da base | `lacunas.md`; se gerar conteúdo, acrescente módulos de redação |
| Capacitador e propostas | `propostas.md`; se alterar conteúdo/agente, acrescente o módulo correspondente |
| Transbordo e roteamento | `transbordo.md` |
| Envio, nomes, encerramento e CSAT | `experiencia.md` |
| Métricas dos atendimentos da Tina | `metricas.md` |
| SEFAZ e contexto cadastral | `sefaz.md` |
| Checkout, contingência ou ECF legado | `regras-checkout.md`, além do módulo da tarefa |
| Erro de autenticação | Somente a seção do serviço afetado em `regras-autenticacao.md` |

Para backlog, próxima melhoria, assumir/concluir item ou retomar uma proposta, carregue também `tina-gestao-melhorias`. Para consultas assíncronas ao NotebookLM, carregue **antes** `notebooklm-async-queries` e siga a exigência de automação de retorno. Não prometa retorno automático sem automação confirmada.

Para preparar vínculos por caminho exato, use `rotinas-imagens-vinculo-deterministico`; para captura de janelas e menus Delphi/Win32, use `delphi-win32-menu-capture`. Ao gravar Markdown pelo PowerShell, consulte `windows-markdown-utf8`. Essas skills orientam tarefas distintas: correspondência de caminho não comprova identidade visual, nem autoriza upload ou publicação.

## Registro obrigatório de conclusão no Sheets

Ao finalizar qualquer alteração na Tina/Help ATS, carregue obrigatoriamente `tina-registro-sheets` antes de registrar ou sincronizar. A atualização das abas aplicáveis e da Auditoria faz parte do fechamento autorizado da tarefa; atualize Melhorias e Backlog quando houver acompanhamento. Confirme por releitura no Sheets e reporte bloqueios sem declarar a entrega integralmente concluída. Reindexação, sincronização do Sheets e geração do Excel são verificações distintas. Preserve registros remotos ausentes das fontes locais antes de executar sincronização geral.

## Controles de qualidade que não podem ser omitidos

### Conteúdo
- Antes de editar conhecimento, leia os módulos da tarefa. Preserve PROC/FEWSHOT, frontmatter, visibilidade, vínculos bilaterais e slugs canônicos conforme as referências.
- Use Markdown simples e UTF-8 sem BOM. Nada de SQL, jargões de IA, tabelas físicas, paths locais no corpo dos artigos, LaTeX, HTML de teclas ou imagens inline quebradas.
- Não invente telas ou derive preço a partir de instruções de quantidade. Para insumos brutos, inspecione todas as imagens e valide os elementos visuais antes de redigir.
- Nunca aprove lacunas com marcas `[CONFIRMAR]`. Mídias exigem chave real retornada pelo storage e gatilho de envio. Não adicione imagens por padrão em revisão de legado.
- Os links físicos de entrega são para o chat de revisão, não para o corpo dos artigos. A entrega de pacotes deve seguir a tabela consolidada de `regras-estrutura.md`.

### Evals e agentes
- Leia `evals.md` antes de salvar ou rodar casos. Avalie a intenção do turno, não um checklist de todo o manual. Prefira critérios semânticos; não imponha páginas ou palavras sem necessidade.
- Não amplie testes específicos para toda a suíte sem motivo/autorização. Separe falha do modelo, critério excessivo e problema de busca.
- Antes de ativação, aplique validação, repetições e comparação com a versão ativa conforme o módulo de evals. Um teste aprovado uma vez não prova estabilidade.
- Relate versão, casos executados, resultados e se houve ativação/publicação. Não confunda rascunho com mudança no atendimento.

## Referências anteriores

Os arquivos existentes `references/curadoria-notebooklm.md`, `references/mcp-tina-console.md` e `references/gotchas-e-dificuldades.md` continuam disponíveis para consulta específica. Não os carregue junto com todos os módulos. O catálogo de ferramentas é apoio histórico: confira assinaturas atuais na descoberta MCP. `indice-legado.md` e `referencias-legadas.md` preservam os índices anteriores, não são pré-requisitos de uso.
