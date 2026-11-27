---
name: notebooklm-async-queries
description: Iniciar e acompanhar consultas assíncronas ao NotebookLM, com automação por consulta e entrega da resposta na sessão de origem. Usar antes de notebook_query_start.
---

# Consultas assíncronas ao NotebookLM

## Escopo e requisitos

Este guia foi incluído para atender à dependência das skills Tina. Requer o MCP `gemini-notebook-mcp`; o retorno automático requer um agendador capaz de consultar o status e acionar a sessão de origem, como `automation` no OpenCode. Confira as ferramentas disponíveis e suas assinaturas antes de usá-las. Não presuma que Codex ou Antigravity disponibilizem o agendador do OpenCode.

Não inicie consultas, acesse outras contas nem execute ações na base apenas por carregar esta skill. Use somente os cadernos e o escopo autorizados para a tarefa.

## Iniciar e acompanhar

1. Descubra `notebook_query_start` e `notebook_query_status` no MCP. Confira o caderno, a pergunta, as fontes pertinentes e o orçamento de tempo; o parâmetro `timeout` da consulta é distinto do timeout de uma chamada de ferramenta.
2. Inicie a consulta e guarde o `query_id`, a sessão de origem e a finalidade da consulta. Não interprete a resposta inicial como resposta final.
3. Consulte o status pelo `query_id`. Se estiver concluída, recupere e entregue a resposta. Se houver erro ou timeout, informe o estado real e o impacto na tarefa; não reinicie automaticamente.
4. Se continuar em andamento, configure **uma automação por consulta**, a cada **15 segundos**, antes de encerrar o turno. Confirme a criação e o identificador da automação. Ela deve consultar o status, permanecer silenciosa enquanto pendente e acionar a sessão de origem quando houver resposta, erro ou timeout.
5. Na notificação de conclusão, envie apenas o `query_id` para recuperação, sem copiar trechos brutos das fontes. Configure o encerramento da automação ao detectar estado terminal; estabeleça também um limite de acompanhamento coerente com o orçamento da consulta. Não deixe verificações recorrentes indefinidas.
6. Ao receber o acionamento, recupere o resultado pelo ID, entregue a resposta útil ao usuário e retome a tarefa original quando ela depender dessa informação. Preserve as referências às fontes fornecidas pelo MCP e diferencie evidência de hipótese.

## Automação no OpenCode

- Descubra as assinaturas atuais do agendador. Use intervalo de 15 segundos e uma ação que chame `notebook_query_status` para o ID específico; não use espera ocupada, `sleep` nem loops de polling no terminal.
- Confira o esquema real do resultado antes de escrever verificações de estado. Não suponha nomes de campos, enums ou envelopes que a ferramenta não tenha retornado/documentado.
- Em ação Code Mode, o estado terminal deve encerrar o job e enviar à sessão criadora uma notificação cujo conteúdo seja somente o ID da consulta. A sessão mantém o contexto necessário para recuperar e interpretar o resultado.
- Se houver falha ao consultar o status, notifique a sessão com o ID e encerre o acompanhamento automático para que a sessão investigue; não trate uma falha de transporte como resposta da consulta.
- Registre o ID do job na sessão para evitar automações duplicadas e permitir cancelar o acompanhamento se o usuário cancelar a tarefa.

## Sem agendador ou em caso de bloqueio

- Se não for possível configurar e confirmar a automação, **não prometa retorno automático**. Tente concluir a consulta no próprio turno, dentro do orçamento disponível, ou informe o bloqueio e o ID pendente.
- Em ambientes sem acompanhamento em segundo plano, prefira a ferramenta síncrona quando ela atender à tarefa e ao orçamento de execução.
- Siga o fluxo de autenticação documentado pelo MCP; não imprima tokens nem arquivos de autenticação.
- Repasse uma vez os avisos de usuário e a identificação da conta ativa quando o MCP solicitar. Não atribua falha de autenticação ou timeout a um erro do conteúdo da base.
