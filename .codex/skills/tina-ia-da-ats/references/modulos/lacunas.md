## 10. Fluxo H: Gestão de Lacunas da Base (Módulo Nativo do Tina Console)

O Tina Console dispõe de um módulo nativo de **Lacunas da Base** acessível diretamente pela interface web em **`https://ai.tina.atsinformatica.com.br/lacunas`** e pela API REST `/api/obs/lacunas`.

> [!TIP] CONCEITO OPERACIONAL
> *"O que a Tina não soube resolver e um analista humano resolveu no Desk vira rascunho de página automaticamente. Nada entra na base sem aprovação humana."*

### 10.1. Como Funciona a Descoberta Automática de Lacunas
1. O backend de observabilidade monitora continuamente os atendimentos encerrados com transferências (`transferido_tina` ou desfechos onde a IA declarou não saber responder ou alucinou).
2. O sistema lê as respostas fornecidas pelos analistas humanos do suporte da ATS no Blip Desk para os clientes.
3. Um modelo de linguagem leve e veloz (`deepseek-v4.1-flash`) analisa o caso, extrai o problema central e a solução aplicada pelo analista, estruturando um rascunho inicial com:
   - **Título sugerido**
   - **Pasta de destino:** `procedimentos`, `erros`, `troubleshooting` ou `faq`
   - **Slug do arquivo:** (ex.: `cst-icms-099-invalido`, `numero-linha-pedido-compra`)
   - **Conteúdo em Markdown:** com seções estruturadas `## Causa` e `## Solução`.
   - **Marcadores de Auditoria `[CONFIRMAR: ...]`:** Onde o modelo identifica que faltam detalhes específicos do ERP Resulth (caminhos exatos de menu, regras tributárias da contabilidade ou comportamento de parâmetros).

---

### 10.2. Regra de Ouro: Saneamento Mandatório das Tags `[CONFIRMAR]`

> [!CAUTION] PROIBIÇÃO DE APROVAÇÃO COM TAGS PENDENTES
> **O texto NUNCA deve ser aprovado contendo marcas `[CONFIRMAR]`.**  
> A interface do Tina Console exibe um alerta amarelo bloqueante:  
> `⚠️ O texto ainda tem X trecho(s) [CONFIRMAR]. Confira e tire a marca antes de aprovar.`

**Protocolo de Saneamento do Curador:**
1. **Abrir a Lacuna:** Clicar em **Revisar e aprovar** na listagem de Pendentes.
2. **Consultar as Conversas de Origem:** Clicar em *"Ver rascunho e conversas de origem"* para ler os turnos do cliente e a orientação exata do analista no Desk.
3. **Validar no Resulth / NotebookLM:** Usar os cadernos da ATS no Gemini Notebook (`13cdb6b1...`) ou a documentação do ERP Resulth para conferir o caminho real da rotina e as opções de tela.
4. **Remover a Marca:** Substituir o bloco `[CONFIRMAR: ...]` pela instrução técnica definitiva e sem ressalvas.
5. **Gravar como Rascunho na Base:** Clicar no botão **Gravar como rascunho na base** (`POST /api/obs/lacunas/{id}/aprovar`).

---

### 10.3. Ciclo de Publicação das Lacunas Aprovadas

1. **Estado Pendente:** Ao clicar em *Gravar como rascunho na base*, o arquivo é criado na pasta escolhida (`erros/...`, `procedimentos/...`) com status de alteração pendente (`alteracoes_pendentes`).
2. **Revisão Visual:** Se o procedimento exigir capturas de tela ou imagens numeradas, anexe as mídias via `enviar_midia_base64`.
3. **Publicação Oficial:** Publique as alterações pendentes no Tina Console (`publicar_alteracoes` ou `python ats.py publicar`).
4. **Resultado Imediato:** O motor RAG reindexa a nova página na base da Tina. Qualquer chamado futuro com a mesma dúvida será resolvido 100% pela IA sem transferência humana!

---

### 10.4. Especificação Técnica da API de Lacunas (`/api/obs/lacunas`)

| Método | Endpoint | Descrição / Parâmetros |
| :--- | :--- | :--- |
| `GET` | `/api/obs/lacunas?agente={slug}&status={status}` | Lista lacunas. `status`: `"pendente"`, `"aprovada"` ou `"descartada"`. |
| `GET` | `/api/obs/lacunas/bases?agente={slug}` | Retorna as bases de conhecimento associadas ao agente (ex.: `["tina"]`). |
| `POST` | `/api/obs/lacunas/previa` | Prévia da análise. Body: `{"agente": "tina", "de": "ISO", "ate": "ISO"}`. Retorna `casos`, `modelo` e `custo_estimado_usd`. |
| `POST` | `/api/obs/lacunas/analisar` | Executa mineração no período. Body: `{"agente": "tina", "de": "ISO", "ate": "ISO"}`. Cria novos rascunhos pendentes. |
| `POST` | `/api/obs/lacunas/{id}/aprovar` | Aprova rascunho e grava na base. Body: `{"base": "tina", "titulo": "...", "pasta": "...", "slug": "...", "conteudo": "..."}`. |
| `POST` | `/api/obs/lacunas/{id}/descartar` | Descarta uma lacuna sem gravar. Body: `{"motivo": "..."}`. |

---

---

