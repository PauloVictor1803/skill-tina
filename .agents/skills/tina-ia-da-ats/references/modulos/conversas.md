## 5. Fluxo de Curadoria e Auditoria de Chamados Reais (`FLUXO E`)

Utilize este fluxo sempre que houver relato de erro, desvio de conduta, alucinação da assistente virtual ou necessidade de conferência de métricas de atendimento:

1. **Inspeção Turno a Turno (`ver_conversa`):**
   - Recupere a conversa completa pelo ID (`conversa_id`), analisando:
     - Mensagem original do cliente e intenção real;
     - Resposta gerada pela Tina e ferramentas acionadas;
     - Pontuação de relevância dos trechos da base trazidos pelo RAG;
     - Se o Verificador de Fundamento cortou frases ou gerou lacunas no texto.

2. **Diagnóstico Assistido por IA (`revisar_conversa`):**
   - Dispare `revisar_conversa` para obter parecer estruturado:
     - Sugestão de página que falta na base (com rascunho de conteúdo pronto);
     - Correção em páginas existentes (`base_corrigir`);
     - Instrução necessária no prompt ou guardrail;
     - Caso de teste recomendado para a suíte de evals.

3. **Marcação Formal de Alucinação (`marcar_alucinacao`):**
   - Se a Tina inventou tela, menu, botão, regra fiscal ou parâmetro inexistente:
     - Chame `marcar_alucinacao(conversa="...", turno_id=X, comentario="Explicar o que foi inventado")`.
     - Isso alimenta o `relatorio_encerramento` e congela a evidência na plataforma.

4. **Correção do Desfecho Formal (`alterar_status_atendimento`):**
   - Se o atendimento terminou com status distorcido (ex.: encerrou como `inatividade`, mas a dúvida foi 100% sanada no 1º turno; ou caiu em inatividade após corte do verificador quando deveria ser transferido):
     - Chame `alterar_status_atendimento(conversa="...", motivo="resolvido_ia" | "transferido_tina", justificativa="Explicar motivo")`.

---

