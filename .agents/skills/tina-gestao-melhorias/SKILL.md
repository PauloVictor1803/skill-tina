---
name: Gestão de Melhorias Tina e Help ATS
description: Consultar, selecionar, assumir, triar, preparar e concluir melhorias, lacunas e propostas da Tina IA e do Help ATS. Use quando pedirem a próxima melhoria da lista, um item por ID, o backlog, a revisão de uma proposta ou a retomada de um trabalho.
---

# Gestão de melhorias Tina e Help ATS

Use este fluxo para coordenar a fila compartilhada, não para alterar diretamente as instruções dos agentes. Carregue `tina-ia-da-ats` quando a tarefa envolver conhecimento, comportamento ou publicação no Tina Console, se disponível. Suas regras de governança e as instruções da sessão continuam valendo. Para consulta assíncrona ao NotebookLM, carregue antes `notebooklm-async-queries`.

## Fontes e retomada

- Raiz oficial: `\\ATS-MOC-01290\Users\paulo.magalhaes\Desktop\Base de dados IA - ats`. Execute `python ats.py` nessa raiz. Se ela estiver inacessível ou faltar o script, reporte o bloqueio e não crie uma fila ou cópia paralela.
- Dentro de `melhorias/`, o `controle_melhorias.json` registra status, responsável, classificação, decisão e destino do trabalho. `README.md` é o painel derivado; `MANUAL_DA_EQUIPE.md` e `GUIA_PARA_IA_DO_HERICK.md` descrevem a operação; `PLANO_DE_ACAO.md` e `TRIAGEM_100_COMPLETA.md` registram contexto e decisões históricas. Não congele contagens, prioridades, IDs ou nomes de personas na skill.
- Leia novamente o controle e o Console a cada retomada: confirme ID, responsável, status local, decisão remota e quais etapas já foram verificadas. Não repita aprovações, locks, testes ou sincronizações por presumir que uma sessão anterior não as fez.
- Diferencie o **agente de origem** da proposta no Console do **destino editorial** e `audience` na fila. Uma proposta originada na Tina pode ser trabalho interno do Help ATS. Divergência de status ou destino deve ser explicada e reconciliada antes de gravar; não reclassifique nem mova automaticamente.

## 1. Consultar e selecionar (sem gravar)

1. Liste com `python ats.py melhorias` e consulte `python ats.py melhorias --detalhes <ID>`. Para a próxima, use `python ats.py melhorias --proxima` com os filtros `--agente`, `--tipo` e `--origem` apenas quando corresponderem ao escopo. A seleção usa itens `backlog` e maior número de ocorrências. Preserve filtros já combinados na conversa. Em uma retomada, verifique antes se há uma melhoria em andamento para o responsável.
2. **Nunca acrescente `--dev` ou `--travar` à consulta**. `--proxima --dev <nome>` não é uma prévia: escolhe e reserva o item com tomada forçada no código atual. Para assumir, use o ID específico depois da confirmação.
3. Antes de recomendar o item, leia a proposta inteira, conteúdo atual/diff, lacuna e evidências de origem (`ver_proposta`, `ver_conversa` ou a fonte pertinente). A saída de `--detalhes` é apenas o registro local, não o atendimento. Se a evidência não puder ser aberta, diga o que faltou e não trate hipóteses como fatos.
4. Apresente ID, origem e ocorrências, o problema observado, por que a proposta existe, o que se pretende mudar, destino/público e efeito esperado. Informe duplicidades, conflitos ou trechos `[CONFIRMAR]` relevantes. Um pedido como “pode seguir para a próxima melhoria” permite essa inspeção, não a reserva nem outras gravações.

## 2. Confirmar e reservar

- Antes de qualquer ação com efeito, explique o ID, operação, itens/arquivos/sistemas afetados, consequências e aguarde confirmação explícita. Não extrapole autorização: reserva, criação/edição de arquivos, importação do Console (`--atualizar`), reclassificação, gravação no Console, aprovação/descarte, testes com custo, registro local, sync, publicação e ativação são efeitos distintos.
- Com autorização específica para reservar, confira imediatamente que o ID ainda está no backlog e sem outro responsável. Execute `python ats.py melhorias --pegar <ID> --dev "<responsável confirmado>"`, sem `--forcar`; jamais confirme a tomada de um item alheio. O comando altera status para `em_andamento` e atualiza o JSON e o README em rede. Releia o item e confira o responsável antes de prosseguir. Se já estiver sob o mesmo responsável, retome sem renovar o lock desnecessariamente.
- O script lê o JSON antes do lock de escrita; não trate o lock como garantia transacional de reserva. Se houver concorrência ou inconsistência, pare e coordene com a equipe. Não apague `.controle.lock` nem edite o controle à mão para contornar o fluxo.

## 3. Investigar, triar e preparar

- **Conhecimento novo ou corrigido:** cruze manuais/procedimentos locais com o NotebookLM oficial do ERP Resulth (`13cdb6b1-759d-4a52-83ec-56dbfd2ec71a`); use o histórico oficial para linguagem e recorrência quando pertinente. Confirme produto/versão, telas, atalhos, condições de aplicação e fontes. Procure cobertura existente antes de propor uma página nova. Se fontes divergem ou a solução não foi validada, explicite a lacuna e não invente.
- **Tina, cliente final:** `audience: all` só para orientação operacional segura. Em fiscal, indique onde conferir ou informar dados e peça validação da contabilidade; não prescreva enquadramento, código ou alíquota como corretos. Não proponha conciliação entre relatórios distintos. Não ensine comandos de banco, reparo estrutural ou intervenções de infraestrutura; corrupção, bugcheck e Access Violation exigem transferência ao Suporte Técnico, sem citar níveis internos ao cliente.
- **Help ATS, suporte interno:** mantenha `audience: analyst`, destino editorial `procedimentos/analyst/` e isolamento da base de atendimento ao cliente. Classificar como interno não autoriza criar, publicar ou ensinar rotinas restritas; siga a governança aplicável e encaminhe intervenções especializadas conforme as regras vigentes.
- **Página nova:** prepare PROC e FEWSHOT conforme o padrão da base em `procedimentos/<tema>/` ou na pasta autorizada para o escopo (inclusive separação do legado). **Editar página:** compare versão atual, fonte e proposta; faça só a alteração necessária. Use YAML correto, títulos sem emojis, wikilinks canônicos pertinentes e referências espelhadas quando couber. Sem imagens inline; mídia confirmada no YAML com chave remota válida e gatilho de envio. Não invente menus, soluções nem mídias.
- **Prompt, guardrail, skill, modelo, fluxo ou transbordo:** compare proposta e ambiente vigente; verifique recursos, regras, versões e rodízio de nomes antes de diagnosticar. Mostre trecho atual, mudança mínima, diff e efeito. Preserve instruções e referências fora do escopo. Mudanças de configuração dos agentes exigem autorização prévia e direta do Paulo Magalhães.
- **Eval:** derive a expectativa de evidências, preserve todos os testes existentes, limite a até seis novos casos focados por lote de bases/sprint e informe custo antes de rodar. **Duplicidade, ruído ou conflito de governança:** recomende manter ou descartar com evidência específica, indicando a cobertura existente quando houver. Não descarte sem autorização por ID.

## 4. Validar e pedir decisão

1. Revise o texto e o destino antes de gravar. Depois de uma gravação autorizada, releia e compare o diff completo. Para documentação, execute `python ats.py validar` e reporte erros/avisos reais; confira separadamente FEWSHOT, vínculos, público, mídias e quaisquer arquivos não abrangidos pelo validador.
2. Trate aprovação no Console como **rascunho**, não como algo no ar. Antes de aprovar, explique o que será gravado, versão se aplicável, se há evals automáticos e seu custo quando disponível. Após aprovação, confirme rascunho/versão e consulte a rodada; informe andamento se ainda não terminou. Não diga que passou sem ver o resultado. Publicar uma página ou ativar uma versão é outra decisão e requer autorização separada e validação aplicável.
3. Registre o desfecho no painel local somente com autorização e evidência da etapa que ele representa: `python ats.py melhorias --concluir <ID> --decisao aprovada --destino "<caminho>"` ou `python ats.py melhorias --concluir <ID> --decisao descartada --motivo "<justificativa>"`. Esse comando grava o JSON/README; **não** aprova nem descarta no Console e **não** sincroniza o Google Sheets. Não use conclusão local “Aprovada em Rascunho” se a aprovação correspondente ainda não ocorreu. Confira status, decisão, destino/motivo e data depois.
4. Só rode `python ats.py sync` se autorizado para atualizar Google Sheets e Excel local. Verifique ambos separadamente; Excel atualizado não comprova sucesso no Sheets. Não use `python ats.py publicar` como atalho: pode combinar validação, staging, publicação e sincronização fora da etapa aprovada.

Ao encerrar cada sessão, informe em poucas linhas: ID e público, o que foi apenas consultado/preparado, o que foi efetivamente alterado e verificado, status local/remoto e próxima etapa pendente de decisão.
