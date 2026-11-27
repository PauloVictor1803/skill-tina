### Dificuldade 1: Imagens Quebradas no Corpo do Texto no Tina Console (`[img]`) e Imagens Faltantes
* **O que acontece:** O preview do markdown no Tina Console exibe caixas com ícone de imagem quebrada e o texto alternativo da foto.
* **Causa:** Uso de tags markdown de renderização direta `![descricao](midia/...)`. A Tina Console gerencia imagens pelo painel lateral de Mídias e por disparo via tool MCP `enviar_midia`.
* **Regra de Ouro (Mídias Desacopladas):** NUNCA use a sintaxe `![alt](midia/...)` nos procedimentos e few-shots. Substitua sempre pelos blocos oficiais `> [!TIP] Mídia Obrigatória` com chamada para `enviar_midia`.
* **Regra de Ouro (Imagens Faltantes e Alinhamento Proativo):**
  - Pode acontecer de não termos capturas/prints de todas as telas ou janelas secundárias de um fluxo.
  - **NUNCA invente hashes, nomes de foto ou suposições de telas.**
  - **Em caso de dúvida ou falta de imagens, pergunte diretamente ao Paulo Magalhães** no chat antes de tomar qualquer decisão ou publicar o documento. Se a rotina puder ser explicada textualmente sem imagens, mantenha `tem_imagem: false` e anote a necessidade do print na aba `Melhorias e Backlog`.


### Dificuldade 4: Chaves de Mídia sem Hash e Erro MinIO NoSuchKey (Imagens Quebradas)
* **O que acontece:** Preencher no YAML o nome de arquivo local/bruto sem hash (ex.: `arquivo: midia/2026/10/Pesquisa_status_nfe.png` ou `foto_rotina_status_nfe.png`) faz com que a API gere URLs pré-assinadas para um arquivo inexistente no MinIO S3. O cliente no WhatsApp ou Blip Desk recebe um ícone de imagem quebrada e o link retorna XML `<Error><Code>NoSuchKey</Code></Error>` (como ocorrido no Ticket #113506).
* **Causa Raiz:** O bucket do Tina Storage (`tina-midia`) armazena as fotos com um sufixo de hash de 8 caracteres gerado no upload (ex.: `midia/2026/10/pesquisa-status-nfe-f05a1681.png`) e é estritamente *case-sensitive*.
* **Regras de Ouro:**
  1. **Upload Obrigatório:** Nunca invente a chave da mídia manualmente no Markdown. Faça o upload pelo script oficial `python ats.py upload-midias <pasta>` ou pela ferramenta `enviar_midia_base64`.
  2. **Captura do Hash Real:** Pegue sempre a chave exata retornada pelo storage (com o hash `-[0-9a-f]{8}`) e cole-a no campo `arquivo:` do frontmatter.
  3. **Blindagem do Pipeline (`python ats.py validar`):** O validador oficial do repositório possui checagem estrita por expressão regular (`-[0-9a-f]{8}\.[a-zA-Z]{3,4}$`). Qualquer tentativa de validar ou publicar procedimentos com chaves sem hash acusa erro crítico imediato e barra a publicação.
  4. **Auditoria de Integridade:** Sempre que houver suspeita de imagem quebrada, audite as referências cruzando `arvore_da_base` com `listar_midias` para garantir que 100% das fotos existam no bucket.

### Dificuldade 11: Proibição de Imagens Inline Quebradas no Corpo do Markdown (`![alt](midia/...)`)
* **O que acontecia:** Inserção de tags Markdown inline com caminhos relativos locais (`![Painel](midia/2026/10/painel.png)`) no corpo do texto operacional, ou imagens onde nem deveria ter.
* **O erro gerado:** O Tina Console não renderiza imagens por caminhos locais relativos dentro do visualizador web da Central. Isso gerava caixas retangulares cinzas de imagens quebradas exibindo o texto do arquivo no meio do procedimento.
* **Regra de Ouro (Desacoplamento de Mídias no Tina Console):**
  - Mídias pertencem **exclusivamente ao frontmatter YAML (`midias:`)**, com seu `id`, `arquivo` (chave oficial com hash gerada no storage), `descricao` e gatilho `quando_enviar`.
  - É através do frontmatter que o Tina Console exibe as mídias no painel lateral direito de Mídias e a assistente virtual Tina dispara o envio dinâmico via ferramenta `enviar_midia` no momento exato em que o cliente tem dúvida da tela.
  - O corpo do documento `.md` deve ser limpo, textual e direto, sem tags inline `![...](midia/...)` que quebrem o layout do console.

### Dificuldade 21: Politica Rigorosa de Imagens nas Revisoes do Legado (	em_imagem: false)
* **Diretriz de Curadoria:** Nas revisoes e melhorias do acervo legado (pasta melhorias legado/), **NAO adicionar novas imagens de telas por padrao**.
* **Validacao Critica:** Caso surja a necessidade de imagem, o analista deve inspecionar e questionar criticamente se as midias ja existentes no bucket atendem com 100% de precisao a tela e versao atual. Na ausencia de tela perfeitamente condizente, manter expressamente 	em_imagem: false e quantidade_imagens: 0.

