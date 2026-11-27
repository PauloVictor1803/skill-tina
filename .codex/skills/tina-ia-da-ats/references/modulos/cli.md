## 15. Ferramental CLI do Repositório (`ats.py`)

No terminal, execute a partir da raiz do repositório:

```bash
# Pipeline unificado de publicação (valida -> staging -> commit na nuvem -> sync no Sheets)
python ats.py publicar

# Inspecionar validade do token da Tina (com tempo restante e opção de auto-renovação com -r)
python ats.py check-token
python ats.py check-token -r

# Reautenticar sessão MCP do Tina Console via navegador (OAuth + PKCE manual)
python ats.py auth-tina

# Validar frontmatter, mídias e conformidade visual de todos os procedimentos recursivamente
python ats.py validar

# Extrair automaticamente telas/cenas do ERP a partir de vídeos de treinamento
python ats.py extrair-keyframes <video.mp4> --sensibilidade 0.3 --intervalo-min 2

# Sincronizar Google Sheets e atualizar Relatorio_ATS_v3.xlsx
python ats.py sync

# Formatar alturas de linha e limites de colunas no Google Sheets
python ats.py formatar

# Limpar arquivos temporários e caches
python ats.py limpar

# Gestão de Lacunas da Base (Módulo Nativo do Tina Console)
python ats.py lacunas                                   # Lista lacunas pendentes mineradas
python ats.py lacunas <ID>                              # Inspeciona rascunho completo e chamados de uma lacuna
python ats.py lacunas --previa [--dias 30]              # Simula custos e novos casos mineráveis
python ats.py lacunas --analisar [--dias 30]            # Dispara mineração de novos rascunhos no período
```

---

