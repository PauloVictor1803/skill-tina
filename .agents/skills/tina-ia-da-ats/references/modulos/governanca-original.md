---
name: tina-ia-da-ats
description: Gerenciar conhecimento e evals da Tina no Tina Console: criar, revisar e testar casos com critérios semânticos equilibrados; auditar, enriquecer e publicar procedimentos, few-shots e mídias do ERP Resulth ATS, usando validação técnica e rastreabilidade. Não usar para migrações de banco de dados.
metadata:
  icon: "📚"
---

# Base de Conhecimento IA — ATS Informática (Tina Console)

Esta skill orienta o fluxo completo de criação, curadoria, enriquecimento visual, auditoria e publicação de conhecimento para a assistente virtual **Tina** e para a base de suporte do ERP **Resulth** (Windows e Web).

## Regra global: pasta de trabalho da base

- A raiz oficial de trabalho da base é `\\ATS-MOC-01290\Users\paulo.magalhaes\Desktop\Base de dados IA - ats`, independentemente da pasta em que a sessão foi aberta.
- Procure nessa raiz os arquivos da base que não estiverem na sessão atual, incluindo `ats.py`, e use-a como diretório de trabalho ao executar scripts da base. Os caminhos relativos citados nesta skill para arquivos da base partem dessa raiz.
- Quando a tarefa autorizar criar ou editar arquivos da base, trabalhe nessa pasta compartilhada, na subpasta correspondente. Não crie cópias ou estruturas paralelas em `C:\Tina` ou em outra pasta local. Se a pasta compartilhada estiver inacessível ou um arquivo não for encontrado nela, informe o bloqueio em vez de recriar o arquivo em outro local.
- Esta regra define onde trabalhar; ela não autoriza copiar, alterar ou publicar arquivos da base sem pedido explícito do usuário.

## ⚠️ Regra Inviolável de Governança: Trava de Segurança em Agentes

> [!CAUTION] PROIBIÇÃO ESTRITA DE ALTERAÇÃO AUTÔNOMA DE AGENTES
> **É TERMINANTEMENTE PROIBIDO alterar prompts, guardrails, skills, modelos, versões ou fluxos de conversação dos agentes sem a autorização prévia, explícita e direta do Paulo Magalhães.**
> 
> * **Ferramentas Bloqueadas para Execução Autônoma (Exigem Confirmação Prévia):**
>   - `ajustar_prompt`
>   - `salvar_guardrail` / `remover_guardrail`
>   - `salvar_skill` / `remover_skill`
>   - `salvar_configuracao`
>   - `ativar_versao` / `ativar_versao_fluxo`
>   - `salvar_rascunho_fluxo` / `publicar_fluxo`
>   - `criar_agente` / `editar_agente`
> * **Ações de Leitura e Auditoria Permitidas (Somente Leitura):**
>   - `ver_configuracao`, `ver_regras_fixas`, `listar_versoes`, `ver_versao`, `validar_configuracao`, `ver_fluxo`, `listar_agentes`.
> * **Protocolo de Modificação em Agentes:**
>   Sempre que for necessário ajustar um prompt, guardrail ou skill de um agente:
>   1. O agente deve redigir a proposta completa com diff/explicação no chat.
>   2. Perguntar claramente ao Paulo se ele autoriza a gravação.
>   3. **Aguardar a confirmação explícita** do Paulo antes de disparar a ferramenta MCP.

---

## 🚫 Regra Inviolável de Segurança de Dados: Proibição de Procedimentos de Banco de Dados / SQL / Corrupção / Bugcheck

> [!CAUTION] ESCALONAMENTO OBRIGATÓRIO PARA SUPORTE DE NÍVEL SUPERIOR
> **É TERMINANTEMENTE PROIBIDO disponibilizar scripts SQL, comandos de banco de dados (IBExpert/ISQL/pgAdmin) ou procedimentos de reparo estrutural de tabelas nos documentos markdown (`PROC` e `FEWSHOT`) e nas respostas aos clientes.**
>
> 1. **Cenários Estritamente Proibidos de Serem Ensinados no Conhecimento N1 / Tina:**
>    - Execução de comandos SQL (`SELECT`, `UPDATE`, `DELETE`, `INSERT`, `ALTER`, `COMMIT`) em tabelas do sistema (`SANGRIA`, `PARAMREM`, `LINKSEMPRESANOVASENHA`, `COMPLNFCE`, etc.).
>    - Procedimentos de manipulação de generators, triggers, sequences ou reconstrução de índices e chaves primárias (`PK_...`).
>    - Erros críticos de banco de dados e infraestrutura: **bugcheck**, **banco de dados corrompido** (*database corrupted* / *checksum error* / *wrong page type*), falhas de I/O de disco e reparos com ferramentas de baixo nível (`gfix`, `gbak`).
>    - Criação ou recriação de banco de dados a partir de scripts.
>
> 2. **Diretriz Mandatória de Redação e Atendimento:**
>    - Sempre que uma ocorrência envolver corrupção de dados, bugcheck, travamento físico de tabelas ou necessidade de comando SQL, o documento **NÃO DEVE** descrever a solução técnica de banco.
>    - **Regra de Comunicação (Sem Níveis de Suporte):** Não citar níveis de suporte (ex.: "N1", "N2", "N3", "Tier 2"). Referir-se sempre de forma direta e profissional como **"Suporte Técnico"** ou **"equipe de suporte técnico / equipe técnica especializada"**.
>    - A documentação e a IA devem registrar expressamente:
>      > *"Este erro indica uma inconsistência estrutural ou de infraestrutura no banco de dados. O procedimento técnico NÃO deve ser executado pelo usuário/cliente. O chamado DEVE ser transferido/escalonado para o **Suporte Técnico da ATS** (pois o mesmo possui competência para tal) para análise e intervenção segura com backup prévio."*


---
