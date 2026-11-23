# Integração MCP - Tina Console (ATS Informática)

Documento oficial de referência e catálogo completo da conexão **Model Context Protocol (MCP)** entre os ambientes locais e o servidor central do **Tina Console**.

---

## 1. Dados de Conexão e Configuração

* **URL do MCP Server:** `https://mcp.central.atsinformatica.com.br/tina/mcp`
* **Servidor de Autenticação (ATS Auth):** `https://auth.central.atsinformatica.com.br`
* **Mecanismo de Segurança:** OAuth 2.0 com PKCE (`code_challenge_method: S256`) e Resource Indicators (`resource=https://mcp.central.atsinformatica.com.br/tina/mcp`).
* **Arquivo Global de Configuração:** `C:\Users\paulo.magalhaes\.gemini\config\mcp_config.json`
* **Armazenamento de Tokens de Sessão:** `C:\Users\paulo.magalhaes\.gemini\antigravity-ide\mcp_oauth_tokens.json`

### Bloco no `mcp_config.json`
```json
{
  "mcpServers": {
    "tina": {
      "serverUrl": "https://mcp.central.atsinformatica.com.br/tina/mcp"
    }
  }
}
```

### Script de Renovação de Acesso (OAuth + PKCE)
Quando a sessão expirar (`Unauthorized` ou `401`), execute no terminal da raiz do repositório:
```bash
python ats.py auth-tina
```
*(Executa o script `scripts/auth_tina.js`, abre o navegador na Central ATS e salva o novo token diretamente em `mcp_oauth_tokens.json`).*

---

## 2. Identificação da Conta e Permissões

Consulta obtida via `quem_sou_eu`:

| Campo | Valor |
| :--- | :--- |
| **Nome** | Paulo Magalhães |
| **E-mail** | `paulo.magalhaes@atsinformatica.com.br` |
| **Papel Principal** | `editor` |
| **Papéis Atribuídos** | `editor`, `leitor` |
| **Permissão de Visualização (`pode.ver`)** | `true` |
| **Permissão de Edição (`pode.editar`)** | `true` |
| **Acesso a Segredos/Tokens (`pode.segredos`)** | `false` |
| **Base Habilitada (`base_habilitada`)** | `true` |

---

## 3. Recursos Mapeados no Ambiente ATS

### 3.1. Agentes Cadastrados
* **`tina`**: Suporte N1 ATS (modelo `deepseek/deepseek-v4.1-flash`, base: `tina`).
* **`financeiro`**: Agente para assuntos financeiros (modelo `deepseek/deepseek-v4.1-flash`, base: `tina`).
* **`suporte-chatwoot`**: Suporte N1 no Chatwoot, réplica da Tina após identificação (modelo `deepseek/deepseek-v4.1-flash`, base: `tina`).
* **`atendimento-chatwoot`**: Porta de entrada do Chatwoot do tipo fluxo (executa o fluxo `inicio`).

### 3.2. Bases de Conhecimento RAG
* **`tina`**: Base principal de documentação com **mais de 5.150 documentos/artigos indexados**, vinculada aos 4 agentes ativos.

### 3.3. Fluxos de Atendimento Cadastrados
* **`inicio`**: Fluxo de recepção e identificação de clientes (versão ativa: `2`).
* **`atendimento`**: Fluxo de triagem e direcionamento (versão ativa: `3`).

---

## 4. Catálogo Completo das 101 Ferramentas (MCP Tools)

O servidor MCP expõe **101 ferramentas ativas** distribuídas em 11 módulos funcionais:

### 1. Gestão e Inspeção de Agentes (Governança e Versões) (16 ferramentas)

> ⚠️ **Trava de Segurança (Governança ATS):** As ferramentas de escrita e alteração deste módulo (`ajustar_prompt`, `salvar_skill`, `remover_skill`, `salvar_guardrail`, `remover_guardrail`, `salvar_configuracao`, `ativar_versao`, `criar_agente`, `editar_agente`) **NUNCA devem ser acionadas autonomamente pela IA**. Qualquer modificação de prompts, regras, guardrails ou skills dos agentes exige validação de diff e autorização prévia expressa do Paulo Magalhães.

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `quem_sou_eu` | Conta conectada no Tina Console: e-mail, papel e o que pode (ver, editar, segredos). | — |
| `listar_agentes` | Agentes cadastrados (slug, nome, descricao, ligado). | — |
| `ver_configuracao` | Configuracao ATIVA do agente: modelo, prompt, guardrails, skills, tools, MCPs, verificador, voz, dados do cliente e base de con... | `agente` |
| `ver_regras_fixas` | Regras que valem para todo agente e nao se editam (vao junto do prompt). | — |
| `validar_configuracao` | Monta o agente com a configuracao (sem chamar o modelo) e devolve erros e o PROMPT EFETIVO (com regras fixas, skills e guardrai... | `agente`, `versao_id` |
| `listar_versoes` | Historico de versoes da configuracao (quem gravou, comentario, qual esta ativa). | `agente`, `limite` |
| `ver_versao` | Uma versao com a configuracao completa (para comparar ou voltar). | `versao_id*`, `agente` |
| `criar_agente` | Cadastra um agente novo, com a configuracao padrao (base da Tina). Para bases proprias: criar_base e vincular_base. slug: minus... | `slug*`, `nome*`, `descricao` |
| `editar_agente` | Muda nome, descricao ou liga/desliga o agente (desligado nao atende). | `agente*`, `nome`, `descricao`, `ativo` |
| `ajustar_prompt` | Troca o prompt (instrucoes principais) do agente. Grava uma versao nova a partir da ativa; com ativar=false fica so gravada. Ma... | `prompt*`, `comentario*`, `agente`, `ativar` |
| `salvar_skill` | Cria ou substitui (pelo nome) uma skill: o modelo ve nome e descricao e carrega as instrucoes quando precisa. nome ate 64, desc... | `nome*`, `descricao*`, `instrucoes*`, `comentario*`, `age... |
| `remover_skill` | Tira uma skill (pelo nome) numa versao nova. | `nome*`, `comentario*`, `agente`, `ativar` |
| `salvar_guardrail` | Cria ou substitui (pelo nome) um guardrail. tipo: - instrucao: `texto` vai como regra no prompt; - bloquear_entrada: mensagem d... | `nome*`, `tipo*`, `comentario*`, `agente`, `texto`, `padr... |
| `remover_guardrail` | Tira um guardrail (pelo nome) numa versao nova. | `nome*`, `comentario*`, `agente`, `ativar` |
| `salvar_configuracao` | Para o resto (modelo, fallbacks, verificador, voz, dados do cliente, tools, MCPs, bases, fluxos_ferramenta...): `alteracoes` tr... | `alteracoes*`, `comentario*`, `agente`, `ativar` |
| `ativar_versao` | Poe a versao no ar: o agente troca em alguns segundos, sem reiniciar. Tambem serve para voltar a uma versao anterior. Triagem q... | `versao_id*`, `agente`, `forcar` |

### 2. Capacitador e Propostas de Melhoria Contínua (Curadoria Autônoma) (11 ferramentas)

> 💡 **Pipeline de Curadoria e Evals Automáticos:** O capacitador revisa atendimentos reais encerrados por sinais (`transferido`, `parcial`, `alucinacao`, `avaliacao_baixa`, `nao_funcionou`, `help_ats`) e cria propostas. A aprovação via `aprovar_proposta` **nunca joga direto no ar**: páginas viram alterações pendentes e ajustes de prompt/regras geram versões candidatas que disparam a suíte de evals automaticamente.

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `ver_capacitador` | Agente capacitador do agente: configuracao, se esta rodando, gasto de hoje, ultima e proxima rodada e as rodadas recentes (lido... | `agente` |
| `configurar_capacitador` | Liga/desliga e ajusta o capacitador. So muda o que for informado. - intervalo_min (30 a 10080): de quanto em quanto tempo roda;... | `comentario*`, `agente`, `ativo`, `intervalo_min`, `max_a... |
| `rodar_capacitador` | Roda uma rodada agora (mesmo desligado), em segundo plano. Veja o resultado com ver_rodada_capacitador. Ja ha uma rodando: erro. | `agente` |
| `ver_rodada_capacitador` | Uma rodada: status (rodando, ok, teto, sem_casos, erro), lidos por sinal, propostas (nova, somada, ja_existe, descartada_antes)... | `rodada_id*`, `agente` |
| `listar_propostas` | Fila de propostas de melhoria (as mais repetidas primeiro). status: pendente, aprovada, descartada ou "" (todas). tipo: pagina_... | `agente`, `status`, `tipo`, `origem` |
| `ver_proposta` | Uma proposta com o conteudo, o que existe hoje (`atual`), o `diff` e os atendimentos de origem. | `proposta_id*` |
| `criar_proposta` | Registra uma proposta de melhoria a mao (origem manual). Igual a uma pendente: soma a ocorrencia; igual a uma descartada: erro.... | `tipo*`, `titulo*`, `agente`, `problema`, `prioridade`, `... |
| `editar_proposta` | Completa ou corrige uma proposta PENDENTE antes de aprovar. `conteudo` substitui o inteiro; campos por tipo: pagina_nova {base,... | `proposta_id*`, `titulo`, `problema`, `prioridade`, `cont... |
| `aprovar_proposta` | Aplica como RASCUNHO, nunca no ar: pagina vira alteracao pendente (publicar_alteracoes depois); skill/guardrail/prompt/transbor... | `proposta_id*` |
| `descartar_proposta` | Descarta com motivo (obrigatorio). A mesma proposta nao volta para a fila. | `proposta_id*`, `motivo*` |
| `relatorio_evolucao` | Quanto a base e o agente evoluem: por semana, propostas criadas, aprovadas e descartadas (por tipo e origem), paginas aprovadas... | `agente`, `semanas`, `parada_dias` |

### 3. Transbordo Inteligente e Roteamento Avançado de Filas (11 ferramentas)

> 🎯 **Roteamento Desacoplado do Prompt:** As regras de transbordo leem diretamente dados do cadastro do cliente (ex.: `cliente.CODROTA`) sem poluir a janela de contexto da LLM, roteando clientes para filas específicas (N1, Especial, Premier, ATTO, Revenda). O controle de insistência (`definir_pedidos_para_transferir`) evita transbordo imediato no 1º pedido.

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `ver_transbordo` | Assuntos, regras (em ordem) e fila padrao do transbordo na versao ativa, e os campos do cadastro do cliente (prompt x so roteam... | `agente` |
| `salvar_assunto_transbordo` | Cria ou troca (pelo id) um assunto que o modelo informa ao transferir. id: minusculas, numeros, - e _ (ex.: financeiro). descri... | `id*`, `descricao*`, `comentario*`, `agente`, `ativar` |
| `remover_assunto_transbordo` | Tira um assunto (a gravacao falha se alguma regra ainda o cita). | `id*`, `comentario*`, `agente`, `ativar` |
| `salvar_regra_transbordo` | Cria ou troca (pelo id) uma regra. A primeira regra (em ordem) que casar decide. - condicoes: [{"campo", "operador", "valor"}];... | `id*`, `nome*`, `condicoes*`, `destino*`, `comentario*`, ... |
| `remover_regra_transbordo` | Tira uma regra (para so desligar: salvar_regra_transbordo com ativo=false). | `id*`, `comentario*`, `agente`, `ativar` |
| `ordenar_regras_transbordo` | Nova ordem das regras: `ids` com TODAS as regras, da primeira a ultima. | `ids*`, `comentario*`, `agente`, `ativar` |
| `definir_transbordo_padrao` | Fila quando nenhuma regra casa. Vazio = a fila de transbordo do canal (tela Blip e filas). | `fila*`, `comentario*`, `agente`, `ativar` |
| `definir_campos_roteamento` | Campos do cadastro do cliente que SO as regras de transbordo leem (ex.: ROTA, SITUACAO_FINANCEIRA). Vem na mesma consulta dos d... | `campos*`, `comentario*`, `agente`, `ativar` |
| `definir_pedidos_para_transferir` | Cliente pediu atendente (card #152857): so transfere no N-esimo pedido do atendimento (1 a 5); antes, a Tina tenta resolver sem... | `pedidos*`, `comentario*`, `agente`, `ativar` |
| `simular_transbordo` | Para onde iria uma transferencia, sem transferir ninguem: cada regra com cada condicao (valor que tinha, casou ou nao) e o dest... | `agente`, `id_ats`, `assunto`, `categoria`, `canal`, `ver... |
| `relatorio_transbordo` | Decisoes reais das regras no periodo: por regra (`(padrao)` = nenhuma casou), por destino, por assunto, quantas sem os dados do... | `dias`, `agente`, `limite` |

### 4. Base de Conhecimento RAG (Páginas, Procedimentos e Publicação) (17 ferramentas)

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `listar_bases` | Bases de conhecimento, com os agentes que usam cada uma. | — |
| `arvore_da_base` | Todas as paginas (caminho, tamanho, pendente de publicar). | `agente`, `base` |
| `buscar_na_base` | Procura texto nas paginas (nome e conteudo). | `termo*`, `agente`, `base` |
| `ler_pagina` | Conteudo da pagina (markdown com frontmatter), o `hash` para salvar e os metadados. | `caminho*`, `agente`, `base` |
| `previa_trechos` | Onde o indexador vai cortar este texto de pagina (com ou sem frontmatter): trechos de busca (um por secao # ou ##), blocos de c... | `conteudo*`, `agente`, `base` |
| `alteracoes_pendentes` | Paginas alteradas e ainda nao publicadas (novo, modificado, apagado). | `agente`, `base` |
| `historico_pagina` | Versoes publicadas da pagina (commit, autor, data, mensagem). | `caminho*`, `agente`, `base` |
| `ver_pagina_em` | Conteudo da pagina num commit do historico. | `caminho*`, `commit*`, `agente`, `base` |
| `criar_base` | Cria uma base vazia. Para um agente usar: vincular_base. | `slug*`, `nome*` |
| `renomear_base` | Muda o nome de exibicao da base (o slug fica). | `slug*`, `nome*` |
| `vincular_base` | O agente passa a buscar tambem nesta base (versao nova da configuracao, ja ativa). | `base*`, `agente*` |
| `desvincular_base` | O agente deixa de buscar nesta base (precisa ficar com pelo menos uma). | `base*`, `agente*` |
| `salvar_pagina` | Cria ou altera uma pagina (markdown, caminho terminando em .md). Pagina que ja existe exige o `hash` lido em ler_pagina - se al... | `caminho*`, `conteudo*`, `hash_anterior`, `agente`, `base` |
| `mover_pagina` | Renomeia ou muda a pagina de pasta. | `de*`, `para*`, `agente`, `base` |
| `apagar_pagina` | Apaga a pagina (com o hash de ler_pagina). Sai da base ao publicar; fica no historico. | `caminho*`, `hash_anterior*`, `agente`, `base` |
| `descartar_alteracao` | Desfaz a mudanca pendente de uma pagina (volta a publicada). | `caminho*`, `agente`, `base` |
| `publicar_alteracoes` | Salva no historico (commit com o seu nome) e INDEXA: so depois disso o agente passa a usar. Sem `caminhos`, publica todas as pe... | `mensagem*`, `caminhos`, `agente`, `base` |

### 5. Comportamento de Envio, UX e Encerramento (WhatsApp) (4 ferramentas)

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `configurar_envio` | Como a resposta sai. So muda o que for informado. - dividir / max_partes (2-8): mensagens curtas, cortadas em paragrafo (lista ... | `comentario*`, `agente`, `dividir`, `max_partes`, `digita... |
| `configurar_encerramento` | Como o atendimento resolvido termina. So muda o que for informado. - perguntar_mais_algo: a Tina so encerra depois de perguntar... | `comentario*`, `agente`, `perguntar_mais_algo`, `mensagem... |
| `configurar_nomes` | Nomes da agente em rodizio por atendimento (ex.: ["Tina", "Rita", "Patricia"]). So muda o que for informado; ligado precisa de ... | `comentario*`, `agente`, `lista`, `ativo`, `ativar` |
| `marcar_conversa_teste` | Marca (ou, com teste=false, desmarca) a conversa toda como teste (card #151475): os atendimentos dela aparecem como "teste" e s... | `conversa*`, `teste`, `comentario` |

### 6. Atendimentos, Curadoria e Diagnóstico de Chamados Reais (5 ferramentas)

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `listar_conversas` | Conversas do periodo, mais recentes primeiro: ultima mensagem, quem respondeu, desfecho, como terminou, transferencias, custo. ... | `dias`, `agente`, `desfecho`, `busca`, `canal`, `encerram... |
| `ver_conversa` | Uma conversa turno a turno: mensagem, resposta, desfecho, custo e as etapas (modelo, ferramentas, paginas consultadas com a pon... | `conversa*`, `detalhado` |
| `marcar_alucinacao` | Marca que a IA inventou (tela, menu, procedimento ou informacao) num turno da conversa (`turno_id` = o `id` do turno em ver_con... | `conversa*`, `turno_id`, `comentario` |
| `revisar_conversa` | Revisao da conversa com sugestoes CONCRETAS do que ajustar (card #151036): pagina da base que falta (com o rascunho) ou trecho ... | `conversa*` |
| `alterar_status_atendimento` | Corrige como um atendimento terminou (card #150976). Pegue `encerramento_id` (ou, no sem desfecho, `ultimo_turno`) em ver_conve... | `conversa*`, `motivo*`, `justificativa*`, `submotivo`, `e... |

### 7. Flow Engine (Fluxos de Conversação) (11 ferramentas)

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `guia_de_fluxos` | Como montar um fluxo: tipos de bloco e as saidas de cada um, templates, pontos de retorno e o esquema JSON completo do fluxo. | — |
| `listar_fluxos` | Fluxos cadastrados (slug, nome, versao no ar, quem editou por ultimo). | — |
| `referencias_de_fluxo` | O que os blocos podem usar: agentes (e quais sao triagem), Tools HTTP, servidores MCP, outros fluxos (subfluxo) e nomes de segr... | — |
| `ver_fluxo` | Rascunho do fluxo (nos, arestas, variaveis, ajustes), versao no ar e problemas. | `fluxo*` |
| `simular_fluxo` | Uma mensagem do cliente de teste contra o RASCUNHO. Passe de volta o `estado` da resposta anterior para continuar a conversa (s... | `fluxo*`, `mensagem`, `estado`, `saida`, `contato_nome`, ... |
| `metricas_fluxo` | Por bloco: entradas, erros, abandono (cliente parou de responder ali) e esperando; por saida: quantas vezes foi usada. Das conv... | `fluxo*`, `dias`, `versao` |
| `criar_fluxo` | Fluxo novo. `modelo`: "vazio", "atendimento" ou "entrada" (exemplos prontos); ou `copiar_de` = slug de outro fluxo para duplica... | `slug*`, `nome*`, `descricao`, `modelo`, `copiar_de` |
| `salvar_rascunho_fluxo` | Grava o rascunho INTEIRO (o objeto de ver_fluxo["rascunho"], alterado). Aceita incompleto; devolve os problemas por bloco. Nao ... | `fluxo*`, `rascunho*`, `nome` |
| `publicar_fluxo` | Publica o rascunho: versao nova, imutavel e JA no ar (conversas novas). Recusa se houver erros (veja ver_fluxo). | `fluxo*`, `comentario*` |
| `listar_versoes_fluxo` | Versoes publicadas do fluxo (a `ativa` e a que esta no ar). | `fluxo*` |
| `ativar_versao_fluxo` | Poe uma versao publicada no ar (voltar versao). | `fluxo*`, `versao_id*` |

### 8. Triagem e Avaliação de Acurácia de Classificação (10 ferramentas)

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `ver_conjunto_triagem` | Conjunto rotulado da triagem: exemplos (id, mensagem, destino certo, origem) e os destinos validos (slug dos agentes; "__humano... | `agente*` |
| `sugestoes_triagem` | Primeiras mensagens reais que entraram por esta triagem (e para onde ela mandou), fora do conjunto: confira e adicione com o de... | `agente*`, `dias` |
| `avaliar_triagem` | Roda uma versao da triagem (vazio = a do ar) contra o conjunto, como em producao e sem falar com ninguem. Espera ate `esperar_s... | `agente*`, `versao_id`, `esperar_s` |
| `listar_avaliacoes_triagem` | Rodadas de avaliacao (mais recentes primeiro): versao, acuracia, status. | `agente*` |
| `ver_avaliacao_triagem` | Relatorio de uma rodada: metricas, matriz de confusao e os exemplos (so os errados, por padrao). | `agente*`, `avaliacao_id*`, `so_erros` |
| `comparar_versao_triagem` | Antes de ativar: a ultima avaliacao desta versao e a da versao no ar, no conjunto atual. `piorou` = ativar pede forcar=true. | `agente*`, `versao_id*` |
| `metricas_triagem` | A triagem nas conversas reais: encaminhou, perguntou antes, foi ao humano, reencaminhou; por destino e por origem (atalho x mod... | `agente*`, `dias` |
| `adicionar_exemplos_triagem` | Exemplos novos: `exemplos` = [{"mensagem", "destino"}], ou `texto` com uma linha por exemplo "destino;mensagem" (ou TAB). Repet... | `agente*`, `exemplos`, `texto` |
| `mudar_exemplo_triagem` | Corrige o destino certo (ou o texto) de um exemplo do conjunto. | `agente*`, `exemplo_id*`, `destino`, `mensagem` |
| `apagar_exemplo_triagem` | Tira um exemplo do conjunto rotulado. | `agente*`, `exemplo_id*` |

### 9. Armazenamento de Mídias (Storage Tina / MinIO) (4 ferramentas)

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `listar_midias` | Arquivos ja enviados (chave, tamanho, data), do mais novo para o mais antigo. | — |
| `enviar_midia_por_url` | Baixa um arquivo de um link https publico e guarda no storage das midias. Aceita png, jpg, webp, gif (ate 5 MB), pdf (10 MB) e ... | `url*`, `nome` |
| `enviar_midia_base64` | Envia um arquivo pequeno (ate 8 MB) em base64. nome_arquivo com extensao (ex.: tela-login.png). Arquivo local maior: preparar_e... | `nome_arquivo*`, `conteudo_base64*`, `nome` |
| `preparar_envio_midia` | Link de envio (vale 15 minutos, so para voce) para mandar um arquivo LOCAL com curl, sem passar o arquivo pela conversa. A resp... | `nome` |

### 10. Suíte de Avaliação Automatizada (EVALS / Prevenção de Regressão) (8 ferramentas)

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `listar_evals` | Casos de eval do agente: id, nome, mensagens do cliente, esperado e ativo (so os ativos entram em "rodar todos"). | `agente` |
| `salvar_eval` | Cria um caso (sem caso_id) ou troca um existente por inteiro. `mensagens`: o que o cliente manda, em ordem. `desfecho`: "respon... | `nome*`, `mensagens*`, `agente`, `desfecho`, `deve_conter... |
| `apagar_eval` | Apaga um caso (para so tirar de "rodar todos", use salvar_eval com ativo=false). | `caso_id*`, `agente` |
| `eval_de_conversa` | Rascunho de caso a partir de uma conversa real: as mensagens do cliente (ate o turno marcado como alucinacao), o desfecho do at... | `conversa*`, `agente`, `salvar` |
| `rodar_evals` | Roda todos os casos ativos (ou so `caso_id`) contra uma versao (vazio = a do ar), com `repeticoes` (resposta de LLM varia; o ca... | `agente`, `versao_id`, `caso_id`, `repeticoes`, `esperar_s` |
| `listar_rodadas_eval` | Rodadas de evals (mais recentes primeiro): versao, taxa, custo, status. | `agente` |
| `ver_rodada_eval` | Resultado de uma rodada: por caso, passou/falhou, motivos, custo e tempo; e o resumo da rodada anterior e da ultima da versao n... | `rodada_id*`, `agente`, `so_falhas` |
| `comparar_versao_evals` | Antes de ativar: a ultima rodada completa desta versao e a da versao no ar, nos casos ativos de agora. `piorou` = ativar pede f... | `versao_id*`, `agente` |

### 11. Relatórios Analíticos e Métricas de Gestão (4 ferramentas)

| Ferramenta | Descrição Resumida | Parâmetros Principais |
| :--- | :--- | :--- |
| `resumo_atendimento` | Numeros do periodo: turnos, conversas, custo, latencia, desfechos (respondeu, transferiu, encaminhou, guardrail, erro), por dia... | `dias`, `agente`, `canal` |
| `relatorio_encerramento` | Como os atendimentos do periodo terminaram: por motivo (resolvido_ia, parcial_ia = resolveu parte e transferiu o resto, inativi... | `dias`, `agente`, `canal` |
| `relatorio_avaliacoes` | Avaliacoes pedidas no periodo: pedidas, respondidas, taxa de resposta, media, distribuicao das notas 1-5, por status (respondid... | `dias`, `agente`, `limite` |
| `relatorio_nomes` | Por nome da agente no periodo: atendimentos, como terminaram (motivos), assertividade (resolvidos / na conta) e avaliacao media. | `dias`, `agente` |

---

## 5. Servidores MCP Externos Integrados à Tina

Além das 101 ferramentas nativas do Tina Console, a assistente conta com servidores MCP externos conectados:

### 5.1. Servidor MCP de Consulta Cadastral SEFAZ / CNPJ
* **URL:** `https://consultainscricaoestadual.netlify.app/mcp`
* **Nome no Agente:** `sefaz_consultar_inscricao_estadual`
* **Ferramentas Expostas:**
  1. `sefaz_consultar_inscricao_estadual_consultar_dados_cnpj`: Consulta dados cadastrais da empresa na Receita Federal e SEFAZ via CNPJ.
  2. `sefaz_consultar_inscricao_estadual_consultar_inscricao_estadual`: Consulta e validação direta da Inscrição Estadual (IE) e situação fiscal para emissão de NF-e.

### 5.2. Servidor MCP Monitor SEFAZ (Disponibilidade e Incidentes em Tempo Real)
* **URL:** `https://monitorsefazmcp.netlify.app/mcp`
* **Nome no Agente:** `consulta-status-sefaz`
* **Ferramentas Expostas (4 ferramentas):**
  1. `consulta_status_sefaz_consultar_incidentes_sefaz`: Lista incidentes ativos, alertas de instabilidade, quedas e manutenções programadas.
  2. `consulta_status_sefaz_consultar_status_geral_sefaz`: Visão macro nacional da disponibilidade SEFAZ (UP/DOWN e autorizadores afetados).
  3. `consulta_status_sefaz_consultar_status_uf`: Consulta o status da SEFAZ em tempo real por estado (UF) e tipo de documento (NF-e, NFC-e, CT-e, MDF-e), resolvendo autorizadores próprios vs virtuais (SVRS, SVAN).
  4. `consulta_status_sefaz_listar_todos_estados_sefaz`: Matriz de disponibilidade dos 27 estados da federação.

---

## 6. Integração de Contexto: Dados do Cliente (Cadastro ATS via Blip)

A Tina está integrada ao banco de dados da ATS através do **Blip**:

* **Mecanismo de Injeção:** O Blip extrai o código do cliente (`idATS`) nos extras do contato (`{{contact.extras.idATS}}`). Na abertura da conversa, o sistema consulta a API da ATS:
  `http://163.176.255.228:3758/api/CLIENTE?CODCLIENTE={idATS}&page=1&limit=1`
* **Campos Injetados no Contexto:**
  - `MODULOS`: Módulos contratados pelo cliente (ex.: `WIN - NFEJAVA - JBUSINESS`), impedindo perguntas desnecessárias sobre sistemas.
  - `SEGMENTO`: Segmento comercial (ex.: `AUTO PECAS E ACESSOR`).
  - `UF`: Estado da federação do cliente (ex.: `MG`).
  - `CIDADE`: Município de instalação (ex.: `BELO HORIZONTE`).
* **Campos de Roteamento Silencioso (Exclusivos de Transbordo):**
  - `CODROTA`: Código da rota comercial/suporte (`00000` = Suporte N1, `00001` = Suporte Especial, `00002` = Suporte Premier, `00003` = ATTO, `00004` = Revenda). Esse campo orienta as regras do transbordo sem poluir o prompt do agente.

### 🎯 Sinergia Operacional: Contexto UF + Monitor SEFAZ
1. Quando o cliente relatar lentidão, erro de comunicação ou rejeição de nota (NF-e, NFC-e, CT-e), a Tina **NUNCA pergunta o estado ou cidade**.
2. Dispara imediatamente `consulta_status_sefaz_consultar_status_uf` informando a UF cadastrada do cliente e o modelo da nota.
3. Se o fisco estiver fora do ar ou instável, orienta contingência (offline no Checkout ou SVC na retaguarda) com tranquilidade e empatia.
