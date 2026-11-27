## 3. Fluxo de Validação Técnica e Mineração de Dúvidas Reais com Gemini Notebook (FLUXO G)

Este fluxo padroniza o ciclo de validação técnica e mineração de dores reais de clientes através dos cadernos corporativos da ATS no **Gemini Notebook (NotebookLM)**, garantindo que nenhum procedimento contenha alucinações de menus ou regras fictícias e que a assistente Tina responda exatamente na linguagem que o cliente usa nos chamados.

### 3.1. Cadernos Oficiais da ATS no Gemini Notebook

| Caderno | Notebook ID | Fontes | Papel no Enriquecimento |
| :--- | :--- | :--- | :--- |
| **🎫 Base de conhecimentos com IA - ATS INFORMATICA** | `13cdb6b1-759d-4a52-83ec-56dbfd2ec71a` | 47 fontes | **Validação Técnica e Processo Oficial:** Manuais, rotinas sistêmicas, telas, menus exatos (`Windows » ...`), pré-requisitos cadastrais e regras tributárias do Resulth. |
| **📋 Histórico de Atendimentos do Suporte Técnico** | `c308482f-2e83-4986-9d55-10a41081adbe` | 14 fontes | **Mineração de Linguagem Real e Dores:** Dezenas de milhares de chamados reais de clientes da ATS, com frases literais, erros reportados, dúvidas recorrentes e jargões. |

### 3.2. As 4 Etapas do Ciclo de Enriquecimento

1. **Etapa 1 — Validação Técnica no Caderno de Conhecimento:**
   - Disparar `notebook_query` via MCP `gemini-notebook-mcp` (ou CLI `nlm query`) contra o caderno `13cdb6b1-759d-4a52-83ec-56dbfd2ec71a`.
   - Extrair: caminho de menus rigoroso, pré-requisitos cadastrais, fluxo operacional completo, reflexo financeiro/estoque e alertas de sistema.

2. **Etapa 2 — Mineração de Frases Literais e Dúvidas Frequentes no Histórico:**
   - Disparar `notebook_query` contra o caderno `c308482f-2e83-4986-9d55-10a41081adbe`.
   - **Prompt Padrão-Ouro:**
     > *"Como o cliente entra em contato a respeito de [TEMA/ROTINA] e quais as maiores dúvidas deles sobre isso? Cite frases e perguntas reais usadas pelos clientes pfv, termos coloquiais, erros relatados e dificuldades mais comuns reportadas ao suporte técnico."*
   - Capturar: perguntas literais de abertura (ex.: *"como lanço boleto avulso sem nota"*), jargões de tela e os 6 a 10 maiores pontos de atrito.

3. **Etapa 3 — Estruturação da Dupla Inseparável (`PROC` e `FEWSHOT`):**
   - **No `PROC` (`type: procedimento`):**
     * Popular a tabela *Vocabulário de Suporte e Gatilhos de Busca (Indexação RAG)* com as frases literais e jargões minerados.
     * Escrever o passo a passo com base na validação técnica oficial.
     * Manter a seção *Veja Também* estrita à árvore de conexões direta.
   - **No `FEWSHOT` (`type: few-shot`):**
     * Formular a pergunta principal a partir da frase mais frequente do suporte.
     * Resposta executável em 5 passos objetivos.
     * **Seção Mandatória "Dúvidas Mais Frequentes do Suporte Técnico":** incluir de 6 a 10 perguntas literais de clientes extraídas do histórico com respostas diretas em parágrafo único.
     * Link espelho obrigatório para o slug remoto do `PROC`.

4. **Etapa 4 — Validação, Publicação e Sincronização:**
   - Executar `python ats.py validar` (valida YAML e integridade de hashes de mídia).
   - Publicar no Tina Console (`salvar_pagina` + `publicar_alteracoes` ou `python ats.py publicar`).
   - Sincronizar Google Sheets e gerar relatório Excel (`python ats.py sync`).

---

