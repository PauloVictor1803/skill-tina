### Dificuldade 16: Timeout de 180s em Consultas no Gemini Notebook (NotebookLM)
* **O que acontece:** A ferramenta `notebook_query` síncrona estoura o limite de 180s em cadernos com alto volume de fontes (ex.: *Histórico de Atendimentos* com dezenas de milhares de chamados).
* **Regra de Ouro:** Sempre utilizar o par assíncrono: disparar com `notebook_query_start(notebook_id=..., query=..., timeout=180)` e checar o retorno com `notebook_query_status(query_id=...)`.

