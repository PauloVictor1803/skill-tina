---
name: tina-catalogo-telas-upload
description: Preparar, revisar e enviar fichas de navegação e imagens de rotinas da Tina em rotinas/telas/, preservando título real, caminhos por módulo e mídias por estado ou aba. Usar ao cadastrar capturas, atualizar acessos ou fazer upload de imagens de rotinas; não substitui a documentação funcional.
---

# Catálogo de telas — manutenção no OpenCode

## Escopo e autorização

Carregue também `tina-ia-da-ats`. Siga suas autorizações de gravação, upload e publicação e consulte as referências de mídia pertinentes. Esta skill não autoriza publicar ou alterar configuração do agente.

As fichas em `rotinas/telas/<slug>.md` servem exclusivamente para identificar a rotina, encontrar seus caminhos e selecionar imagens. A finalidade e o uso detalhado ficam nas páginas de rotinas existentes e nos procedimentos. Não reescreva, mova ou remova esses documentos para montar o catálogo.

Uma ficha por rotina, compartilhada entre módulos. O catálogo complementa o fluxo atual e suas fontes; imagens e caminhos existentes nos procedimentos permanecem intactos.

## Implantação gradual

O catálogo começa incompleto. Cadastre somente os módulos, caminhos e estados comprovados, sem exigir cobertura total para aproveitar uma ficha. Não preencha lacunas com caminhos de outros módulos ou estados presumidos.

A skill de atendimento é um recurso complementar quando o fluxo atual e suas fontes não fornecem o caminho ou a imagem necessários, não uma consulta obrigatória antes de toda orientação. Ausência no catálogo não significa inexistência da rotina e não exige transferência automática. Se nenhuma fonte disponível fornecer evidência da navegação, a agente deve reconhecer a falta de confirmação, nunca inventar menus, abas, botões ou sequências. A falta de imagem não impede orientar por um caminho comprovado em outra fonte.

## Modelo aprovado

Leitura humana e busca usam o corpo Markdown; o cadastro nativo da mídia usa o YAML. Não publicar fichas somente em YAML.

```markdown
---
id: TELA-consulta-cheques
title: "Consulta Cheques"
type: rotina
audience: all
midias:
  - id: consulta-cheques-inicial
    arquivo: midia/2026/11/consulta-cheques-7023adbc.png
    estado: inicial
    descricao: "Consulta Cheques, tela inicial com filtros e opções de visualização."
    quando_enviar: "Quando o cliente precisar identificar a tela inicial de consulta de cheques e o procedimento não trouxer imagem aplicável."
---

## Acesso e imagem

Módulos: Business (Resulth Business para Windows).
Caminho: Consultas > Bancos > Cheques.

> [!TIP] Mídia Obrigatória - Tela inicial de Consulta Cheques
> Consulta Cheques, tela inicial com filtros e opções de visualização.
>
> Dispare a ferramenta `enviar_midia` com o ID `consulta-cheques-inicial` quando o cliente precisar identificar ou pedir uma imagem da tela inicial de Consulta Cheques no Business e o procedimento consultado não trouxer imagem aplicável.
```

Este é um exemplo real validado; a chave pertence exclusivamente a essa captura. Não reutilize título, caminho, ID ou chave em outras rotinas.

- `id`: estável, único e independente do módulo.
- `title`: copie EXATAMENTE o `WindowTitle` do JSON da captura, inclusive acentos e caixa. Não substitua pelo menu nem acrescente módulo, aba ou estado. Investigue divergência entre JSON e imagem.
- `type`: `rotina`. Preserve `audience`; não converta conteúdo restrito em público.
- Caminhos completos e módulos confirmados ficam no corpo, não em `acessos` no YAML. Cada caminho aparece uma única vez, associado à lista de módulos comprovados.
- `midias` mantém ID, chave real de armazenamento, descrição, gatilho e atributos visuais. Não repetir mídia por módulo ou acesso.
- Para cada mídia enviável, o corpo contém um callout com descrição visual, estado/aba/modo relevantes, gatilho e instrução explícita de disparar `enviar_midia` com o ID exato. A chave `arquivo` fica no YAML, não precisa ser repetida no corpo.
- Mantenha descrição e gatilho coerentes entre YAML e corpo; gere ambos da mesma informação na preparação de lotes. Apenas mencionar “Imagem: ID” é insuficiente como padrão.
- Não adicionar `WindowTitle` duplicado, `WindowClass`, `WindowHandle`, `status`, `ImagePath`, `tem_imagem`, lista separada de estados ou título redundante. Não acrescentar descrição funcional, passo a passo nem imagens inline.


## Identidade e caminhos

1. Confira a ficha existente antes de criar outra, usando título real e contexto. Não confunda nomes de menu com títulos de janela.
2. Use apenas acessos documentados ou observados. Nunca deduza o caminho de CaixaCs ou RecebeCs a partir do Business.
3. Registre cada caminho completo uma única vez na ficha. Quando o mesmo caminho for confirmado em outro módulo, acrescente esse módulo à lista `Módulos` do respectivo caminho no corpo, sem duplicar o item nem os módulos. Caminhos diferentes ficam em itens separados, e um módulo pode aparecer em vários itens. Não consolide caminhos apenas semelhantes nem remova níveis para torná-los iguais. Não escolha um preferencial pela ordem da lista, sem evidência ou decisão explícita.
4. Não associe uma imagem a uma rotina por semelhança de nome. Confira título, contexto e captura.
5. Se aparecer colisão de títulos ou mudança de identidade, pare a associação automática e esclareça; não mescle rotinas.

## Mídias e estados

Cada item de `midias` deve ter:

| Campo | Conteúdo |
| --- | --- |
| `id` | Identificador único e estável da mídia, incluindo rotina e estado |
| `arquivo` | Chave real devolvida pelo storage, nunca caminho local |
| `estado` | Estado visual identificado, como `inicial` ou `aba-contatos` |
| `aba` | Texto exato da aba selecionada, somente quando confirmado |
| `modo_operacao` | Inclusão, alteração ou outro modo, somente quando confirmado e relevante |
| `descricao` | Descrição visual curta que distinga a captura |
| `quando_enviar` | Situação de uso da imagem, respeitando a prioridade do procedimento |

Estado, aba e modo pertencem à mídia. Não criar outra lista de estados nem referências intermediárias.

- O escopo padrão é a captura inicial. Só mapear abas adicionais quando solicitado.
- O mesmo título pode ter várias imagens; não altere `title` para distingui-las.
- Aba selecionada não comprova modo de inclusão, alteração ou consulta.
- `inicial` representa a abertura observada, não garante valores padrão universais.
- Dois prints da mesma aba não exigem dois estados. Só separar se houver diferença relevante comprovada para a orientação.
- Não cadastrar abas apenas porque seus nomes aparecem: é necessário inspecionar o conteúdo da captura correspondente.
- Omita atributos desconhecidos. Em protótipo local explicitamente solicitado, `arquivo: null` pode demonstrar uma mídia pendente. Nunca publique nem trate esse item como mídia enviável.
- `estado`, `aba` e `modo_operacao` são metadados da mídia. Exponha os atributos relevantes também no callout pesquisável; não presuma que campos YAML sejam entregues ao agente.

## Fluxo de manutenção e upload

1. **Defina o escopo.** Confirme base, agente, fichas, módulos e estados solicitados. Diferencie piloto local, upload, gravação e publicação autorizados.
2. **Leia os originais.** Consulte JSONs de captura e manifesto, abra todas as imagens selecionadas e confira a ficha atual e seu hash. Preserve alterações de terceiros.
3. **Confira mídia existente.** Consulte o storage e as mídias vinculadas. Reutilize uma chave somente quando a identidade do arquivo ou a equivalência visual estiver comprovada, não pelo nome parecido.
4. **Prepare a ficha mínima.** Siga o modelo acima, sem ampliar descrições funcionais. Use UTF-8 sem BOM. Na base oficial, respeite a raiz compartilhada definida em `tina-ia-da-ats`; só crie pilotos locais quando pedidos.
5. **Envie a imagem se autorizado.** Descubra as ferramentas atuais do Console e use o mecanismo de upload apropriado. Registre a chave exata retornada; não construa hashes ou URLs por suposição.
6. **Vincule.** Cadastre o item em `midias`, com ID, estado e gatilho adequados, e crie o callout correspondente no corpo. Não duplique a imagem por módulo, caminho ou print sem diferença relevante.
7. **Confira antes de gravar.** Título idêntico ao JSON, caminhos confirmados por módulo, IDs sem colisão, chave existente e nenhuma mídia pendente na versão a publicar. Rode `previa_trechos` com o conteúdo final: exija trechos não vazios contendo o caminho/módulo e os callouts com IDs e `enviar_midia`. Em fichas maiores, confira se cada trecho mantém o contexto necessário para selecionar a captura correta.
8. **Grave somente com autorização.** Use `ler_pagina` e `salvar_pagina` com o hash atual, no destino `rotinas/telas/<slug>.md`. Releia o resultado. Se o hash divergir, reconcilie sem sobrescrever trabalho alheio.
9. **Publique separadamente.** Inspecione alterações pendentes para não incluir trabalho de terceiros. Só publique o escopo autorizado e confirme a leitura/indexação.
10. **Registre o fechamento.** Carregue `tina-registro-sheets` ao concluir alterações efetivas na Tina/Help ATS, registre as abas aplicáveis e confirme por releitura. Informe eventuais bloqueios.

Mudança da base exige avaliação pertinente conforme `tina-ia-da-ats` e seu módulo de evals. Não execute suíte ampla, captura real ou testes pagos para uma simples edição de protótipo.

## Verificação de recuperação e envio

- Publicação com “indexed” e busca administrativa bem-sucedida não comprovam recuperação no atendimento. Confira a prévia dos trechos, releia a página publicada e valide uma amostra representativa no chat real.
- Foi observado tratamento diferente de frontmatter na prévia conforme LF/CRLF. Não use troca de quebra de linha para forçar YAML ao índice; mantenha o conteúdo necessário no corpo pesquisável e reporte inconsistências ao responsável pelo indexador.
- Separe recuperação, chamada da ferramenta e entrega efetiva. Uma lista de nomes de ferramentas não prova ID utilizado nem sucesso de envio.
- Quando eval disser que o envio falhou, mas o chat real entregar a imagem, registre a divergência como possível limitação do executor/retorno da ferramenta nos evals. Investigue argumentos e retorno; não altere fichas corretas nem relaxe critérios apenas para obter aprovação.
- Evidência do chat real valida a amostra observada, não todo o acervo. Em lotes futuros, mantenha rastreabilidade captura → ficha → ID de mídia → chave de storage → publicação e verificações por lote. Falhas em um item não autorizam completar outros por inferência.

## Entrega

Informe os arquivos afetados e distinga claramente: preparado localmente, imagem enviada, ficha gravada, publicada, recuperação avaliada e registro no Sheets. Upload não equivale a publicação nem a envio ao cliente.
