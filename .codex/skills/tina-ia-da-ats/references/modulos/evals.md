## 6. Fluxo de Engenharia de Evals e Prevenção de Regressões (`FLUXO F`)

A suíte de evals garante que erros corrigidos ou novos procedimentos publicados nunca sofram regressão em versões futuras do agente:

1. **Conversão de Chamados em Casos de Teste (`eval_de_conversa`):**
   - Transforma automaticamente o histórico de um atendimento auditado em um teste:
     - `eval_de_conversa(conversa="...", agente="tina", salvar=False)` para revisar o caso;
     - Ajuste o critério semântico do julgamento e confirme com `salvar=True`.

2. **Criação e Gestão Manual de Evals (`salvar_eval`):**
   - Cadastre casos contendo:
     - `nome`: Identificador descritivo do teste;
     - `mensagens`: Lista de mensagens simulando o cliente em ordem sequencial;
     - `desfecho`: `"respondeu"` (proibido transferir) ou `"transferiu"` (mandatório transferir);
     - `deve_conter` / `nao_deve_conter`: Termos proibidos ou obrigatórios;
     - `paginas`: Slugs obrigatórios que a busca vetorial precisa retornar;
     - `criterio`: Frase avaliada por LLM sobre a postura da Tina (critérios semânticos são muito mais robustos que termos literais).

3. **Bateria de Testes Pré-Ativação (`rodar_evals` e `comparar_versao_evals`):**
   - Antes de ativar qualquer versão ou alterar prompt:
     - Execute `rodar_evals(agente="tina", repeticoes=3)` (como LLMs variam, o caso só passa se for aprovado em 100% das repetições);
     - Execute `comparar_versao_evals(versao_id=X, agente="tina")`. Se a nova versão piorar a acurácia em relação à versão no ar (`piorou=true`), o deploy deve ser imediatamente suspenso.

### 4. Critérios de qualidade para criar e revisar evals

Antes de cadastrar um eval, formule a intenção do teste em uma frase: qual comportamento importante esta mensagem deve verificar? Escreva o caso para avaliar a resposta à pergunta feita, não para exigir que a Tina recite todo o procedimento relacionado.

- **Respeite a conversa real e as instruções ativas do agente.** Se o prompt manda responder em etapas, ser breve, fazer uma pergunta de acompanhamento ou não despejar uma lista, aceite como correta uma resposta inicial que avance a conversa de forma segura e útil. Não exija todos os detalhes do artigo em um único turno, a menos que o cliente os tenha pedido naquele turno.
- **Separe o essencial do adicional.** No critério semântico, avalie apenas os requisitos necessários para responder corretamente à mensagem. Não reprove por omitir recomendações úteis, mas não solicitadas, ou por não repetir conteúdo já coberto adequadamente.
- **Não transforme o procedimento completo em checklist universal.** Não imponha cada porta, campo, alternativa, ressalva ou etapa a toda pergunta sobre o tema. Associe cada requisito à intenção concreta do caso.
- **Use `paginas` somente quando a recuperação da página for parte explícita do teste.** Não obrigue a consulta de um artigo apenas porque ele existe ou porque o tema é relacionado. Se o objetivo for testar RAG, formule a mensagem de modo que a informação da página seja realmente necessária e diga no critério que tipo de orientação fundamentada deve aparecer.
- **Evite critérios vagos, cumulativos ou impossíveis de observar.** Defina o que é uma resposta aceitável, incluindo paráfrases e alternativas seguras. Use `deve_conter` e `nao_deve_conter` só para requisitos literais realmente indispensáveis; prefira o critério semântico.
- **Cheque se o caso está alinhado ao turno.** Uma pergunta sobre não compartilhar uma senha pode ser aprovada se a Tina proibir claramente o compartilhamento; só cobre revogação, troca e relato de erro se a mensagem ou o cenário também pedir o que fazer após exposição. Uma pergunta sobre indisponibilidade de um recurso pode ser aprovada com encaminhamento correto ao administrador, sem forçar conteúdo lateral que não seja necessário.
- **Faça revisão humana antes de rodar.** Leia a mensagem e o critério lado a lado; confirme que uma resposta curta, correta e conforme o prompt não seria reprovada por faltar um detalhe opcional. Confirme também se `desfecho`, `estilo` e `paginas` são pertinentes.
- **Interprete falhas antes de corrigir o agente.** Leia a resposta gerada e o motivo da falha. Classifique o resultado como: (a) erro real da Tina; (b) critério excessivamente estrito ou desalinhado; (c) variabilidade do modelo; ou (d) problema de busca/base. Corrija o eval quando o critério estiver errado; proponha mudança de agente ou base só quando houver evidência de erro real. Não declare regressão apenas pela taxa agregada sem revisar exemplos.
- **Controle repetições e escopo.** Uma execução mede uma amostra, não garante comportamento estável. Use repetições quando a decisão depender de variabilidade, sem ampliar o conjunto para todos os evals ativos quando o pedido for testar casos específicos. Relate quais casos foram executados e preserve a distinção entre resultados dos testes e julgamento humano.

---

