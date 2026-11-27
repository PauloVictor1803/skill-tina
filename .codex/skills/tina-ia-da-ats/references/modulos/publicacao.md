## 16. Checklist de Publicação no Tina Console

Antes de dar como finalizada qualquer alteração na base:

1. [ ] Upload de imagens via `enviar_midia_base64` e captura dos hashes.
2. [ ] Validação do YAML: `id`, `type`, `midias:` com chaves reais e descrições dos marcadores.
3. [ ] Verificação de links internos no formato `[[pasta/slug|Texto]]` válidos na árvore do Tina.
4. [ ] Chamada de `salvar_pagina` via MCP da Tina para os arquivos correspondentes.
5. [ ] Conferência via `alteracoes_pendentes`.
6. [ ] Publicação oficial via `publicar_alteracoes(base="tina", mensagem="...")`.
7. [ ] Registro no log de auditoria em `dados/auditoria.json`.
8. [ ] Atualização dos metadados em `dados/metadados_base.json` e geração do relatório Excel (`python ats.py sync`).

---

