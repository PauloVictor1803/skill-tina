---
name: rotinas-imagens-vinculo-deterministico
description: Auditar e preparar vinculos entre capturas do ERP Windows e paginas de rotinas pelo caminho de acesso exato, sem IA, similaridade ou tabela de aliases; tratar caminhos ausentes, referencias auxiliares e Web.
---

# Vinculo deterministico de capturas por caminho

Carregue tambem tina-ia-da-ats para regras de midia, autorizacao, visibilidade e publicacao. Leitura/analise nao autoriza upload, alteracao de paginas ou publicacao. Nao copie a base compartilhada sem pedido explicito.

## Chave e escopo

- Use o caminho COMPLETO documentado na pagina e MenuPath/Segments do manifesto. Nao use titulo do PNG, nome de pagina, WindowClass, similaridade, OCR, embeddings ou inferencia de nomes.
- Normalize apenas tipografia: caixa, acentos, espacos e separadores de menu equivalentes. Nao remova niveis, nao complete prefixos, nao procure por sufixo e nao singularize palavras.
- Capturas originadas do Business podem atender outras rotinas Windows que declarem o mesmo caminho; nao as restrinja apenas por modulo business. Registre a origem Business separadamente da plataforma Windows.
- Web nao compartilha as imagens Windows. Exclua paginas exclusivamente Web. Em paginas mistas, classifique cada caminho por seu contexto e vincule a imagem apenas ao caminho Windows. Contexto ambiguo nao e resolvido por suposicao.

## Extracao do Markdown

- Reconheca secoes de acesso (Caminho de Acesso, Como acessar etc.), rotulos explicitos e primeiro passo Acesse/Abra dentro de secoes de execucao/cadastro.
- Nao aproveite indiscriminadamente qualquer caminho do corpo. Configuracoes auxiliares, erros, pre-requisitos e Ver tambem podem citar outra tela.
- Trate Markdown: caminho inteiro em backticks/negrito e itens individualmente em negrito, como **Cadastros** > **Clientes** > **Cidades**. Nao extraia apenas o separador entre dois spans.
- Determine plataforma por linha/rotulo e hierarquia de secoes. Listas Windows e Web consecutivas no mesmo paragrafo nao devem contaminar mutuamente o contexto.
- Pagina sem caminho identificavel fica pendente; nao adivinhe pelo titulo. Ausencia de secao nao significa ausencia de caminho se ha um primeiro passo explicito.

## Cruzamento e relatorio

- Exija igualdade completa, captura concluida e arquivo existente.
- Bloqueie colisoes: dois itens de captura com a mesma chave normalizada nao permitem escolha arbitraria.
- Relacao pode ser muitos-para-muitos. Deduplicate por pagina + caminho; uma pagina pode documentar varias telas e uma imagem pode servir a varias paginas.
- Separe: correspondencia exata, caminho ausente, caminho incompleto/divergente/fora do mapa, caminho mapeado sem PNG, plataforma nao confirmada e ambiguidade.
- Preserve pagina, hash, audiencia, modulo, caminho original, secao/regra da extracao, caminho capturado e arquivo. Nao confunda correspondencia textual com revisao visual completa.
- Para aplicar posteriormente: conferir midias existentes, obter chave real do storage, preservar audience analyst quando definido, cadastrar midias com descricao e quando_enviar especificos de Windows, validar e so publicar com autorizacao.

## Implementacao local e testes

Referencia da implementacao no ambiente de origem: `C:\Tina\menu-mapper\analise-vinculos`. Os scripts abaixo nao fazem parte deste repositorio de skills; localize uma instalacao autorizada antes de executar. Nao presuma que esse caminho exista em outra maquina:

- extrair-caminhos.js: parser puro (sem rede/modelos), recebe resultado de leitura de pagina e devolve metadados/caminhos.
- Analisar-Caminhos.ps1: cruzamento do extrato caminhos-extraidos.json com capturas-topbar/progresso.json, sem editar base.
- testar-extracao.js e Testar-Analise.ps1: regressao de formatos, plataforma, caminho ausente, referencias, duplicidade e colisao.
- Saidas: vinculos-exatos.csv, paginas-por-caminho.csv, caminhos-classificados.csv e resumo-caminhos.json.

O extrato e um snapshot derivado, nao a base completa nem uma consulta atualizada automaticamente. Releia a base para refletir edicoes e confira hashes antes de qualquer gravacao.

## Armadilhas verificadas

- Respostas MCP podem ser strings JSON: decodifique antes de acessar campos. Processe resultados grandes no execute e devolva somente dados de auditoria, nao corpus integral.
- PowerShell 5.1 pode desenrolar uma colecao unitaria retornada por if. Force array na atribuicao externa com @(if (...) {...}) antes de testar Count.
- Nao use $matches como lista de vinculos: -match/-notmatch podem sobrescrever a variavel automatica $Matches (nomes sao case-insensitive). Use $links.
