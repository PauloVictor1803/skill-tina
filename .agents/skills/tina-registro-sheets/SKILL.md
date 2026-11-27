---
name: tina-registro-sheets
description: "Consultar obrigatoriamente ao finalizar ou concluir alterações na Tina/Help ATS e antes de registrar ou sincronizar a planilha ATS. Define abas, campos, fontes locais, auditoria, backlog e verificação do Google Sheets e Excel."
---

# Registro obrigatório das alterações da Tina no Google Sheets

## Quando consultar e o que significa concluir

Carregue esta skill antes de registrar na planilha e SEMPRE ao finalizar uma alteração na Tina ou Help ATS: conhecimento, mídias, prompt, guardrails, skills do agente, ferramentas, versões, fluxos, propostas ou evals. Vale também quando a alteração foi feita diretamente pelo MCP, sem a CLI.

O colaborador autorizou o registro e a sincronização como parte do fechamento das alterações solicitadas. Não peça novamente essa autorização rotineira. Isso NÃO autoriza publicar conteúdo, ativar versões, executar testes pagos, assumir itens, alterar outros trabalhos ou corrigir o histórico indiscriminadamente. Respeite as autorizações da tarefa e a skill tina-ia-da-ats.

Não declare a entrega integralmente concluída enquanto o registro aplicável no Sheets não tiver sido relido e confirmado. Se a alteração terminou mas o registro falhou, informe separadamente: alteração concluída; registro no Sheets pendente, com motivo e próxima ação. Não confunda reindexação com atualização da planilha.

## Destino e fontes oficiais

- Planilha: https://docs.google.com/spreadsheets/d/120ZEDlvhXAnZYwD-brCDerTZ0wS2hSSLDaHbaN0LoIc/edit
- Raiz compartilhada nesta máquina: \\ATS-MOC-01290\Users\paulo.magalhaes\Desktop\Base de dados IA - ats
- Localize o acesso real à mesma pasta em outras máquinas. Não suponha o nome do usuário Windows; não crie base ou planilha paralela.
- Leia instruções locais e confira README.md, ats.py, scripts/sincronizar_sheets.py, scripts/gerenciador_base.py e scripts/gerar_excel.py. Reinspecione quando mudarem.
- A integração usa API REST Google Sheets; não depende de um MCP de Sheets.
- A planilha tem sete abas. Linha 1: título; linha 2: descrição; linha 3: cabeçalhos; registros a partir da linha 4.
- A coluna A (#) é numeração de apresentação, não identidade estável. Preserve títulos, cabeçalhos, filtros, formatação, fórmulas e registros de terceiros.
- Não trate exemplos antigos da planilha como instruções vigentes do agente: ela é registro histórico, não a configuração ativa.

## Escolha das abas

| Alteração efetiva | Registro específico | Histórico |
| --- | --- | --- |
| Persona, instruções core ou prompt | Prompt | Auditoria |
| Regra de proteção/guardrail | Guardrails | Auditoria |
| Skill configurada no agente Tina | Skills | Auditoria |
| Ferramenta HTTP, OpenAPI, integração ou MCP do agente | Tools HTTP e MCP | Auditoria |
| Página, procedimento, few-shot, troubleshooting, rotina ou mídia vinculada | Base de Conhecimento | Auditoria |
| Avaliação, teste, ativação, revisão, decisão ou configuração sem aba própria | Sem criar aba nova; Auditoria | Melhorias e Backlog se houver acompanhamento/pendência |
| Lacuna, conteúdo analyst a revisar, falha ou trabalho acompanhado | Melhorias e Backlog | Auditoria quando houver ação executada |

Uma entrega pode afetar várias abas, mas não copie o mesmo texto em todas. A aba específica descreve o que mudou; Auditoria conta o evento e suas evidências; Backlog acompanha problema, ação e estado.

Uma skill local do OpenCode não é uma skill configurada no agente Tina. Não a registre em Skills como se tivesse alterado o atendimento. Se registrar esse trabalho de governança, identifique explicitamente o componente local na Auditoria.

## Esquemas e regras de preenchimento

### Prompt, Guardrails, Skills, Tools HTTP e MCP

Colunas A:G: **# | Data | Componente | Ação | Objetivo | ANTES (Problema) | DEPOIS (Solução)**.

Fontes locais respectivas: dados/prompt.json, dados/guardrails.json, dados/skills.json, dados/tools.json. São listas de objetos:

```json
{"data":"DD/MM/AAAA","componente":"Nome específico","acao":"Adaptado","objetivo":"O que se pretende resolver","antes":"Comportamento anterior comprovado","depois":"Mudança efetivamente aplicada e seu estado"}
```

- Use Adicionado para inclusão e Adaptado para revisão, como no padrão existente. Outra ação deve ser descritiva e fiel (por exemplo remoção), sem disfarçá-la como adição.
- A data é a do evento, não a data da sincronização se forem diferentes.
- Não reescreva eventos antigos para apagar histórico. Antes de incluir, confira se o mesmo evento já existe.
- Ordem das listas locais é preservada pelo sincronizador. Há abas históricas que não estão rigorosamente ordenadas por data; não reordene toda a aba por iniciativa própria.
- Rascunho não equivale a configuração ativa: escreva versão e estado explicitamente em DEPOIS.

### Base de Conhecimento

Colunas A:I: **# | Data | Título | Ação | Objetivo | ANTES (Problema) | DEPOIS (Solução) | Tem Imagem? | Qtd. Imagens**.

É um inventário por documento, não uma cópia completa do log de eventos. Atualize o documento existente pelo ID canônico; PROC e FEWSHOT são documentos distintos. Mantenha o histórico de revisões na Auditoria.

O gerador combina Markdown/frontmatter das pastas cobertas com dados/metadados_base.json, objeto indexado pelo ID canônico:

```json
{
  "TS-exemplo": {
    "objetivo": "Finalidade específica do artigo.",
    "antes": "Lacuna ou problema observado.",
    "depois": "Resultado aplicado; público e estado de publicação quando relevantes."
  }
}
```

- Objetivo é exibido com o prefixo [ID-canônico]. Não duplique o prefixo.
- Título vem do frontmatter title; data prioriza data_atualizacao, data_criacao, updated_at, created_at. Use a data verdadeira; não altere frontmatter apenas para forçar posição no relatório.
- O inventário ordena datas decrescentes, agrupa tema e prioriza PROC antes do FEWSHOT. O gerador deduplica por ID, com prioridade de pastas; verifique qual arquivo está prevalecendo.
- Tem Imagem? = Sim/Não; quantidade numérica real das mídias cadastradas no documento. Não conte capturas brutas ainda não vinculadas.
- Evite os textos genéricos de fallback. ANTES descreve a lacuna; DEPOIS descreve o que existe agora, sem afirmar redução de chamados ou estabilidade sem medição.
- Mudança de vínculos também é mudança de conhecimento. Registre IDs/páginas afetados na Auditoria.
- Artigos analyst continuam descritos como internos. Publicar/indexar não os torna disponíveis à Tina cliente final.
- O gerador atual lê procedimentos/, procedimentos checkout/, few-shots/ e base-conhecimento/. Alterações só remotas ou em outras pastas não são automaticamente descobertas.
- Os metadados sem Markdown têm fallback de título, data, tipo e mídias que pode ser incorreto. NÃO use esse fallback como registro válido: confirme os campos gerados e escolha representação suportada/autorizada, sem criar cópias paralelas nem alterar o gerador silenciosamente.
- O campo Ação é atualmente derivado pelo código, em geral Adicionado, mesmo em revisões. Não prometa que alterar acao no JSON mudará a célula. Descreva a revisão real na Auditoria; se for necessário corrigir o gerador, proponha a mudança separadamente.

### Auditoria

Colunas A:F: **# | Data / Hora | Responsável | Tipo de Ação | Arquivo / Componente | Detalhes**.

Fonte: dados/auditoria.json, lista de objetos:

```json
{"data_hora":"DD/MM/AAAA HH:MM","responsavel":"Responsável confirmado","tipo_acao":"Publicação de base","componente":"ID ou componente exato","detalhes":"O que mudou, estado real e evidências verificadas."}
```

- Todo fechamento com alteração deve ter evento de auditoria. Consulta sem alteração não exige inventar evento.
- Informe o responsável efetivo confirmado, nunca o Paulo por padrão. registrar_auditoria tem responsável padrão; passe o valor explicitamente.
- Detalhes: escopo, IDs/slugs, público, rascunho/publicação/ativação, commit, páginas indexadas e resultado da validação quando existirem.
- Evals: versão, IDs dos casos/rodadas, repetições, passou/falhou e ressalvas. Bloqueio do provedor não é falha da Tina. Não invente testes.
- Diferencie resultado automático e revisão humana; preserve falhas e não afirme estabilidade por um único sucesso.
- Não reconstrua o ANTES sem evidência, nem preencha data/hora antiga por suposição. Registros retroativos devem indicar que o são.
- registrar_auditoria insere no início da lista, mas o histórico contém acréscimos fora dessa ordem. Preserve-os.
- Os leitores só levam à planilha os campos do esquema acima. Chaves extras de JSON só aparecem se incorporadas também ao texto de Detalhes.

### Melhorias e Backlog

Colunas A:I: **# | Data Registro | Tema / Módulo | Slug / Página Relacionada | Audience Atual | Tipo de Pendência | Diagnóstico / Conteúdo Útil | Ação Recomendada | Status**.

Fonte: dados/melhorias_backlog.json, lista:

```json
{"id":"MELH-NNN","data":"DD/MM/AAAA","tema":"Tema / Módulo","slug":"pasta/slug-real","audience_atual":"analyst","tipo_pendencia":"Comportamento a investigar","diagnostico":"Fato observado e evidência","acao_recomendada":"Próxima ação objetiva","status":"Pendente"}
```

- Pesquise por ID, slug e problema antes de criar. Atualize o item correspondente; não gere duplicata a cada sync.
- Estados usuais: Pendente, Em Andamento e Concluído. Existem estados qualificados; preserve qualificadores úteis. Não normalize todo o histórico.
- Só conclua a etapa comprovada. Publicação concluída pode coexistir com falha de eval ainda pendente.
- Se o trabalho acompanhado acabou, atualize o item existente para refletir o desfecho, sem apagar diagnóstico nem falhas.
- Nem toda publicação precisa criar uma pendência artificial; use Backlog para acompanhamento real e Auditoria para o evento.
- registrar_melhoria usa tamanho da lista para criar ID e não garante deduplicação/concorrência. Verifique IDs e exclusividade de escrita.
- melhorias/controle_melhorias.json é outra fila. Concluir nela não atualiza automaticamente este JSON nem o Sheets. Siga tina-gestao-melhorias para a fila de propostas.

## Fluxo seguro de fechamento e sincronização

1. **Confira o realizado.** Releia o resultado do Console ou os arquivos e associe cada mudança às abas aplicáveis. Consulte esta skill antes dos registros, não só depois.
2. **Leia local e remoto antes de gravar.** Compare por conteúdo e identidade, não apenas quantidade ou #. Desconsidere diferenças meramente de exibição de datas na comparação.
3. **Proteja o histórico.** Há registros remotos que podem não estar nas fontes locais. Não rode sync geral se ele sobrescrever esses registros. Faça backup das fontes não secretas e snapshot dos intervalos afetados em local autorizado; reconcilie apenas equivalências comprovadas. Em conflito de autoria/conteúdo, pare e peça decisão, sem descartar trabalho.
4. **Atualize fontes aplicáveis.** Preserve chaves e itens existentes, escreva UTF-8 sem BOM e releia o JSON. Prefira as funções existentes quando adequadas, mas confira suas limitações. Não deixe a planilha atualizada e as fontes antigas, pois o próximo sync desfaria o registro.
5. **Valide a prévia.** Execute os leitores carregar_* do gerenciador_base sem importar sincronizar_sheets: o sincronizador escreve remotamente durante a importação. Compare as linhas geradas e o diff pretendido. Verifique títulos, datas, mídias, status e ausência de duplicatas.
6. **Confira ambiente e acesso.** Use o mesmo Python 3; confira PyYAML e openpyxl. Instale apenas o necessário com autorização aplicável. Confira a planilha por leitura autenticada dos metadados, ID e abas antes da escrita. Uma leitura CSV pública ajuda na inspeção, mas NÃO prova autorização de escrita.
7. **Sincronize só o escopo seguro.** Sem divergência nem concorrência, execute da raiz a etapa Sheets e só depois Excel:
   - python scripts/sincronizar_sheets.py
   - confira código de saída, intervalos e linhas;
   - python scripts/gerar_excel.py
   - confira código de saída e destino real.
   Uma escrita direcionada pela API também precisa atualizar a fonte local e preservar os demais registros. Não improvise reescrita geral para resolver um conflito.
8. **Releia o remoto.** Leia os intervalos efetivamente escritos e compare todos os registros da entrega com os dados esperados. Verifique que registros anteriores foram preservados e não ficaram duplicatas/caudas obsoletas. Não faça limpeza de linhas fora do escopo sem autorização.
9. **Verifique Excel separadamente.** Confirme caminho, data de modificação e abertura com openpyxl. Sheets atualizado não implica Excel gerado, e vice-versa.
10. **Entregue estado real.** Informe abas atualizadas, IDs/componentes, confirmação por releitura e eventuais pendências. Não declare "sincronizado" só pelo retorno da CLI.

## Limitações verificadas dos scripts (reconferir antes de usar)

- ats.py sync chama subprocess.run sem check=True e imprime sucesso mesmo se uma etapa falhar. Prefira executar/verificar separadamente; código 0 da CLI não comprova sucesso de ambas.
- sincronizar_sheets.py escreve as sete abas a partir das fontes locais. Não é sincronização bidirecional, não puxa mudanças do Console e não faz merge dos registros editados na planilha.
- Intervalos de dados começam em A4; o script não limpa caudas antigas e ignora coleções vazias. A7 (Melhorias e Backlog) tem também cabeçalhos reescritos; preservar customizações exige inspeção.
- gerar_excel.py contém destino absoluto legado do Paulo. Confira destino acessível; proponha ajuste mínimo para os.path.join(BASE_DIR, 'planilhas', 'Relatorio_ATS_v3.xlsx') se necessário, com autorização antes de editar o script.
- Não use ats.py publicar, formatar, limpar, check-token ou auth-tina como atalho para registro: possuem outros efeitos ou outra autenticação.

## Credenciais e falhas

O sincronizador procura o primeiro arquivo existente, nesta ordem: dados/sheets_tokens.json; dados/mcp_oauth_tokens.json; sheets_tokens.json na raiz; legado no perfil local; legado fixo do Paulo. Prefira a configuração já existente na pasta compartilhada; confirme estrutura sem imprimir valores.

Nunca copie credenciais de conversas para esta skill, documentação, logs, commits ou mensagens. Preserve entradas existentes; não copie sessão pessoal da Tina. Esta skill não concede permissão para usar credenciais de outra pessoa. Credenciais expostas devem ser revogadas/renovadas pelo responsável.

**Fluxo obrigatório de renovação OAuth para Sheets (não usar access token diretamente):**

- Tokens de acesso Google são curtos (tipicamente cerca de 1 hora); a expiração do access token não significa que o usuário precise autenticar novamente. O refresh token é o mecanismo normal de renovação silenciosa.
- Antes de qualquer chamada REST ao Sheets, leia o arquivo configurado sem imprimir valores; valide `token.expiry`. Se o access token estiver ausente ou expirar em menos de 60 segundos, faça POST `application/x-www-form-urlencoded` ao `token_url`, com `grant_type=refresh_token`, o refresh token, `client_id` e `client_secret`. Atualize no mesmo arquivo o access token retornado, o refresh token se houver rotação e a expiração calculada com `expires_in` (padrão 3600 s), preservando os demais campos. Use só então o token novo nas chamadas Sheets.
- **Nunca** leia o access token salvo e o use diretamente sem validar/renovar; esse atalho causou HTTP 401 mesmo com refresh token disponível e foi confundido com expiração da autenticação do usuário. Não peça novo login só porque o access token expirou.
- `scripts/sincronizar_sheets.py` implementa refresh automático, mas executa sincronização ampla durante a importação. **Não o importe nem o execute para renovar token ou fazer gravação direcionada.** Para tarefa direcionada, use função/helper isolado que faça somente leitura/refresh/persistência OAuth e depois chame a API apenas nos intervalos autorizados; leia e faça backup dos intervalos/fontes, atualize a fonte local e releia o Sheets para conferir.
- Se o refresh responder `invalid_grant`, indicar revogação, ou a chamada REST continuar em 401 após refresh confirmado, pare sem repetir em loop; revise conta OAuth, escopos e validade do refresh token e solicite a ação humana apropriada. Não imprima corpo bruto de erro ou dados de credenciais.
- Nunca imprima access/refresh token, client secret, nem o JSON de autenticação. Erros devem ser sanitizados. Login Tina não renova Google.

Em pasta inacessível, dependência ausente, conflito, falta de permissão ou falha de Sheets/Excel, preserve o realizado e relate o bloqueio. Não prometa sincronização futura automática sem mecanismo configurado.

## Checklist final

- [ ] Skill consultada; escopo e estado real conferidos.
- [ ] Aba(s) específica(s) e Auditoria registradas; Backlog atualizado quando aplicável.
- [ ] Fonte e remoto comparados; histórico e trabalho alheio preservados.
- [ ] Datas, responsável, IDs, público e evidências corretos; sem segredos.
- [ ] Escrita confirmada por releitura no Google Sheets.
- [ ] Excel confirmado separadamente, ou pendência explicitada.
- [ ] Entrega distingue publicado/indexado, registrado no Sheets, testado e ativado.
