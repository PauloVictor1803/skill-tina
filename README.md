# Tina IA da ATS (Skill Oficial)

Skill avançada para gerenciar, auditar, enriquecer e publicar procedimentos operacionais, few-shots e mídias no **Tina Console** para o ERP Resulth ATS.

A skill integra validação técnica e mineração de linguagem real com **Gemini Notebook (NotebookLM)**, mantendo sincronização bidirecional com o Google Sheets, metadados locais e rastreabilidade total de homologação.

---

## 🚀 Principais Capacidades

1. **Catálogo Completo de 101 Ferramentas MCP:**
   * Mapeamento integral da API do Tina Console em 11 módulos funcionais:
     - Gestão da Base (Páginas, Pastas, Staging e Árvore);
     - Gestão de Mídias (Storage, Upload e Associação);
     - Auditoria Operacional (Chamados, CSAT, Sinais de Insucesso);
     - Capacitador & Propostas (Curadoria Assistida);
     - Transbordo Inteligente e Fila Humana;
     - Envio, UX e CSAT (Mensagens, Interações e Feedbacks);
     - Observabilidade Analítica e Custos de LLM;
     - Suíte de Avaliação Automatizada (EVALS / Prevenção de Regressão);
     - Sincronização Google Sheets e Staging Seguro.

2. **Blindagem e 30 Dificuldades Operacionais Homologadas:**
   * **Dificuldade 29:** Protocolo estrito de inspeção visual atômica de 100% das imagens (`read_file`) e envio da lista nominal de elementos ao NotebookLM antes de redigir qualquer procedimento.
   * **Dificuldade 30:** Regra de ouro de autenticação da CLI do Gemini Notebook (`nlm login`) no Windows em terminal comum para evitar travamento de ACL/DPAPI do Administrador.
   * **Segurança e SQL:** Proibição absoluta de comandos SQL (`SELECT`, `INSERT`, `UPDATE`, `DELETE`), nomes de tabelas físicas (`MOVIMEN`, `MOVIBAN`, `DOCUREC`, etc.) ou scripts de banco em procedimentos do cliente.
   * **Linguagem e Estilo:** Formato nativo Markdown sem fórmulas LaTeX cruas (`$$`), sem tags HTML `<kbd>` e sem imagens inline em texto (uso estrito do padrão `> [!TIP] Mídia Obrigatória`).

3. **Curadoria com Gemini Notebook (NotebookLM):**
   * Triangulação entre o caderno oficial de **Base de Conhecimentos** (`13cdb6b1-759d-4a52-83ec-56dbfd2ec71a`) e o caderno de **Histórico de Atendimentos Reais** (`c308482f-2e83-4986-9d55-10a41081adbe`).

4. **Pipeline de Testes Automatizados (Evals):**
   * Criação e execução de baterias de testes automatizados com avaliação por LLM para validar a taxa de acerto e prevenir regressões.

---

## 📁 Estrutura do Repositório

```text
skill-tina/
├── .agents/
│   └── skills/
│       └── tina-ia-da-ats/
│           ├── SKILL.md                          # Instrução mestra e árvore de decisão
│           ├── agents/                           # Definições de agentes complementares
│           ├── assets/                           # Diagramas e ícones visuais
│           └── references/
│               ├── mcp-tina-console.md           # Catálogo das 101 ferramentas MCP
│               ├── curadoria-notebooklm.md       # Roteiro de triangulação de cadernos
│               └── gotchas-e-dificuldades.md     # Guia expandido de dificuldades
├── .codex/
│   └── skills/
│       └── tina-ia-da-ats/                       # Espelho para ambientes Codex
├── .gitignore
└── README.md
```

---

## 🛠️ Comandos Frequentes (`ats.py`)

* `python ats.py check-token`: Inspeciona validade e auto-renova o token OAuth da Tina.
* `python ats.py validar`: Valida conformidade de procedimentos, frontmatter e mídias.
* `python ats.py upload-midias <pasta>`: Upload em lote de imagens com geração de hashes canônicos.
* `python ats.py publicar`: Pipeline unificado (validação, staging, publicação nuvem e sync Sheets).
* `python ats.py evals`: Consulta e executa casos de testes automatizados.
