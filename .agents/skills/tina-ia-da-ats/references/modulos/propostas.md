## 11. Fluxo I: Capacitador e Gestão de Propostas de Melhoria Contínua

O Tina Console dispõe de um módulo autônomo de **Capacitação Contínua**, projetado para revisar chamados reais em segundo plano e submeter sugestões cirúrgicas de enriquecimento para curadoria humana.

### 11.1. Como Opera o Capacitador
1. **Disparo Programado e Orçamento Controlado:**
   - O capacitador roda automaticamente em ciclos configuráveis (ex.: a cada 360 minutos, com limite de atendimentos por rodada e teto diário de custo em USD via `configurar_capacitador`).
   - Pode ser disparado sob demanda a qualquer instante com `rodar_capacitador(agente="tina")`.
2. **Filtro por Sinais de Atrito e Dores Reais:**
   - O revisor analisa atendimentos filtrados por sinais específicos:
     - `transferido`: Casos que caíram para analista humano no Blip Desk;
     - `alucinacao`: Atendimentos onde a IA inventou dados ou caminhos;
     - `parcial`: Atendimentos resolvidos em parte;
     - `avaliacao_baixa`: Notas 1 ou 2 na pesquisa de satisfação;
     - `nao_funcionou`: Casos onde o cliente expressou insatisfação;
     - `help_ats`: Ocorrências originadas do canal interno Help ATS.
3. **Geração de Propostas Estruturadas:**
   - O capacitador analisa a interação e submete propostas para a fila (`listar_propostas`):
     - Páginas novas ausentes na base;
     - Melhorias em páginas existentes com diff de texto;
     - Casos de teste (`evals`) para a suíte automatizada;
     - Ajustes em regras ou prompts.

### 11.2. Regra de Ouro: Aprovação Segura com Evals Automáticos
> [!CAUTION] PROIBIÇÃO DE ATIVAÇÃO DIRETA SEM AVALIAÇÃO
> **A ferramenta `aprovar_proposta` NUNCA coloca alterações diretamente em produção:**
> 1. Propostas de **páginas** viram *alterações pendentes* (`alteracoes_pendentes`), exigindo revisão humana de imagens/hashes antes do deploy oficial via `publicar_alteracoes`.
> 2. Propostas de **prompt, guardrail ou transbordo** geram uma **versão candidata NÃO ativada** e disparam imediatamente a suíte completa de *evals* em segundo plano (`resultado.rodada_eval`).
> 3. O curador deve inspecionar a rodada via `comparar_versao_evals` e **somente ativar a versão** se a acurácia se mantiver estável ou superior, respeitando a regra inviolável de governança.

---

