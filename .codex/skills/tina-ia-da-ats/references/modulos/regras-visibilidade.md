### Dificuldade 0: Página Invisível para o Agente (`interno (analyst) · o agente não vê`) e Curadoria de Páginas Analyst
* **O que acontece:** A página aparece no topo do Tina Console com uma tarja amarela dizendo `interno (analyst) · o agente não vê`, impedindo que a assistente Tina utilize o conhecimento nas conversas com clientes.
* **Causa:** Ausência ou erro de digitação nas propriedades de frontmatter YAML. O indexador exige `title:` (e não apenas `titulo:`), `type: procedimento` ou `type: few-shot` (e não apenas `tipo:`), `audience: all` e `status: active`.
* **Regra de Ouro:** Todo frontmatter YAML DEVE conter explicitamente:
  ```yaml
  title: "Título Oficial do Procedimento"
  type: procedimento # ou few-shot
  audience: all      # MANDATÓRIO para a Tina ter visibilidade
  status: active     # MANDATÓRIO para indexação imediata
  ```
* **Protocolo de Curadoria de Páginas `audience: analyst`:**
  - Durante o ciclo de revisão ou ao criar procedimentos que se conectem a artigos marcados como `audience: analyst`:
    1. **Inspecione a página analyst:** verifique se ela contém parâmetros, regras fiscais, tabelas ou passos úteis.
    2. **Aproveite o conhecimento válido:** incorpore as instruções úteis no documento público correspondente (`audience: all` / `PROC`).
    3. **Registre a pendência/melhoria:** anote o item na 7ª aba do Google Sheets (**`Melhorias e Backlog`** via `dados/melhorias_backlog.json` / `registrar_melhoria`) com status `Pendente`, `Em Andamento` ou `Concluído` para que a equipe possa evoluir ou graduar o documento.

### Dificuldade 18: Manutenção de Páginas Técnicas como `audience: analyst`
* **O que acontece:** Converter procedimentos com configurações avançadas (portas, DLLs, `.dir`, flags de concorrência) para `audience: all` expõe rotinas de infraestrutura a clientes leigos no WhatsApp, gerando alto risco de incidentes operacionais.
* **Regra de Ouro:** Procedimentos puramente técnicos de suporte continuam estritamente como `audience: analyst`. Seus conceitos só apoiam procedimentos `audience: all` quando houver telas do ERP acessíveis ao cliente final com segurança.

---

