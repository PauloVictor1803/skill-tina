## 4. Gestão de Mídias e Armazenamento no Tina Console

As imagens no Tina Console funcionam de maneira desacoplada dos arquivos markdown e são gerenciadas pelo painel lateral de **Mídias**:

1. **Upload via MCP ou Script Oficial:**
   - As imagens devem ser enviadas via `python ats.py upload-midias <pasta>` ou pela ferramenta `enviar_midia_base64`.
   - O storage retorna a chave canônica com hash único: `midia/YYYY/MM/<slug>-<hash>.png`.
2. **Amarração Rigorosa no Frontmatter YAML (`midias:`):**
   - No frontmatter do procedimento ou few-shot, todo item de mídia deve conter obrigatoriamente os 4 campos:
   ```yaml
   midias:
     - id: tela-exemplo
       arquivo: midia/2026/10/tela-exemplo-a1b2c3d4.png
       descricao: "A imagem mostra a tela tal destacando [1] campo A, [2] campo B e [3] botão OK."
       quando_enviar: "Sempre que citar, for explicar ou quando o cliente estiver com dúvida no passo X."
   ```
   > [!IMPORTANT]
   > - O campo `arquivo:` DEVE conter a chave remota completa (`midia/YYYY/MM/...-hash.png`), nunca o nome de arquivo local.
   > - O campo `quando_enviar:` é MANDATÓRIO para alimentar o painel lateral do Tina Console e instruir a IA quando enviar a mídia ao cliente no WhatsApp/Chat.
3. **Chamada no Corpo do Markdown (NUNCA usar tags `![alt](midia/...)`):**
   - **PROIBIDO:** Inserir tags markdown tradicionais de imagem como `![texto](midia/2026/10/...)` no corpo do texto. O navegador web do Tina Console não serve essas imagens por rotas HTTP estáticas diretas, o que gera o ícone de imagem quebrada (`[img]`) na interface.
   - **OBRIGATÓRIO:** Fazer a chamada da mídia no corpo do texto utilizando o bloco callout padronizado:
     ```markdown
     > [!TIP] Mídia Obrigatória — [Título da Mídia]
     > Dispare a ferramenta `enviar_midia` com o ID `[id-da-midia]` sempre que [cenário de envio ou orientação].
     ```
4. **Sincronia Visual Texto x Imagem:**
   - Sempre que a imagem possuir círculos, setas ou numerações (`[1]`, `[2]`, etc.), o corpo do texto do markdown **deve referenciar explicitamente essa mesma numeração**.

---

