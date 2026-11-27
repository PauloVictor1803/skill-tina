## 1. Fluxo de Procedimentos Operacionais (`type: procedimento`)

Os procedimentos são documentos aprofundados destinados a agentes, analistas de suporte e clientes avançados:

1. **Estrutura Obrigatória de Seções:**
   - **Frontmatter YAML:** `id`, `title`, `type: procedimento`, `audience: all`, `status: active`, `tem_imagem`, `quantidade_imagens`, `modulos`, `tags`, `data_criacao`, `data_atualizacao`, e bloco `midias:`.
   - **Visão Geral e Conceito:** Contextualização do fluxo no Resulth, finalidade da operação e requisitos legais.
   - **Vocabulário de Suporte e Gatilhos de Busca (Indexação RAG):** Tabela estruturada cruzando termos reais que clientes usam nos chamados com as rotinas sistêmicas correspondentes.
   - **Pré-Requisitos e Parametrizações:** Cadastros básicos necessários no ERP (Tipos de Operação, regras de ICMS/CST/CSOSN, aba Integração, amarrações tributárias).
   - **Passo a Passo com Telas do Sistema:** Caminhos exatos pelos menus superiores tradicionais (ex.: `NF-e » Digitação/Emissão de NF-e` ou `Windows » Faturamento » ...`) e indicação dos marcadores numerados nas imagens (`[1]`, `[2]`, etc.).
   - **Faturamento e Tratamento Financeiro:** Regra de diferença zero, geração de crédito para cliente ou abatimento no Contas a Receber, caixa e bancos.
   - **Conferência de Documentos (DANFE / XML):** Campos obrigatórios a inspecionar antes ou após a emissão.
   - **Diagnóstico de Erros e Rejeições da SEFAZ:** Soluções puramente sistêmicas para códigos de rejeição e alertas operacionais.
   - **Veja Também (Regra de Pertinência Temática Estrita):** Links internos em wikilink (`[[pasta/slug|Título]]`) estritamente restritos à mesma jornada do usuário (alternativas operacionais diretas, pré-requisitos/cadastros exigidos na rotina e o documento irmão FEWSHOT correspondente). **Proibido** linkar rotinas desconexas (ex.: venda em compras/entrada).

---

