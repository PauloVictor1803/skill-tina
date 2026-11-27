---
name: delphi-win32-menu-capture
description: Mapear menus Win32 de aplicativos Delphi legados e capturar janelas de rotinas com tratamento de DPI; usar ao depurar cliques deslocados, popups ou recortes incorretos.
---

# Menus Delphi e captura de janelas

## Descoberta

- Nao confie apenas em Process.MainWindowHandle: um Delphi pode expor TApplication, enquanto a janela visivel com menu e outra. Enumere janelas do PID e procure uma visivel com GetMenu/GetMenuItemCount validos.
- Use GetMenuItemInfo para rotulos, atalhos, estado e submenus; preserve a hierarquia sem executar folhas no modo inventario.
- Uma janela popup #32768 pode ser associada ao HMENU por MN_GETHMENU (0x01E1). A existencia do popup precede o fim da animacao: aguarde estabilizacao antes de clicar.

## DPI e acionamento seguro

- Nao misture coordenadas logicas do menu legado com pixels fisicos da captura. SetThreadDpiAwarenessContext, sozinho, nao garantiu que GetMenuItemRect retornasse coordenadas fisicas neste caso.
- Na captura per-monitor-aware testada, converta o centro do item usando LogicalToPhysicalPointForPerMonitorDPI com a janela proprietaria e mova com SetPhysicalCursorPos. Nao aplique fator fixo de escala.
- Envie movimento de mouse, aguarde a selecao e confirme GetMenuState com MF_BYPOSITION (0x400) e MF_HILITE (0x80) antes de clicar em uma folha explicitamente autorizada.
- Tecla Home nao confirmou a selecao da folha de um submenu aberto apenas por hover neste aplicativo. Nao envie Enter sem verificar a selecao correta.
- Confirme que o popup fechou e que uma janela adequada surgiu; clique enviado nao prova sucesso.
- Preserve a protecao de foco. Nunca contorne elevacao, autenticacao ou bloqueios de sessao.

## Identificar e capturar

- Registre janelas existentes antes de abrir a rotina. Detecte novas janelas de nivel superior pertencentes ao aplicativo e filhas diretas de MDIClient; controles internos nao sao rotinas.
- O titulo pode ser diferente do nome do menu. Identifique a classe/titulo reais em teste integrado antes de criar filtros; nao suponha que o titulo contenha o rotulo do menu.
- Exija candidato inequivoco e retangulo estavel. Para nivel superior, DWM extended frame bounds evita margens invisiveis; para MDI, use os limites da janela em pixels fisicos.
- Verifique visibilidade, limites da tela e sobreposicao. CopyFromScreen captura o que esta visivel, inclusive obstrucoes; amostragem de pontos nao garante ausencia de obstrucao pixel a pixel.
- Salve PNG e metadados com caminho, titulo, classe e limites. Abra o PNG e confira visualmente o recorte antes de declarar o trabalho testado. Compilar e validar o caminho nao substitui esse teste.

## Referencia local

Os caminhos e testes abaixo documentam a implementacao observada no ambiente de origem. O codigo `menu-mapper` e suas capturas nao estao incluidos neste repositorio de skills. Localize a instalacao autorizada e valide no aplicativo alvo antes de executar; resultados historicos nao comprovam testes nesta maquina.

Implementacao em C:\Tina\menu-mapper: NativeMenuMapper.cs, RoutineCapture.cs e Capturar-PrecoEstoque.ps1. A classe TFCoVaPro foi observada para Preco/Estoque nesta versao; nao generalize para outros aplicativos ou versoes.

## Lotes com pausa e retomada

- No Resulth testado com escala de tela, GetMenuItemRect da barra principal com hwnd informado retornou pixels fisicos, enquanto popups legados com hwnd zero exigiram conversao. Nao aplique a mesma conversao indiscriminadamente aos dois casos. Essa distincao aplica-se a navegacao fisica legada; o lote atual usa comandos nativos, conforme a secao abaixo.
- Exclua tooltips/THintWindow dos candidatos: eles podem surgir depois que o menu fecha e gerar uma falsa deteccao de multiplas janelas. Janelas Delphi de nivel superior podem ter TApplication como proprietaria; restringir todas ao root-owner da janela principal pode omitir dialogos reais. Restrinja pelo PID e filtre auxiliares conhecidos.
- Windows Forms pode usar classe WindowsForms10.MDICLIENT.* em vez de MDIClient; a janela de teste isolada precisou desse reconhecimento.
- Grave checkpoint atomicamente antes de disparar a folha, depois de salvar a captura e depois de confirmar o fechamento. Se houver interrupcao no disparo, exija revisao humana antes de repetir; nao presuma que nenhum efeito ocorreu.
- Separe o estado Capturada/Aguardando fechamento de Concluida. Na retomada de fechamento, nao tire outra captura nem reabra a rotina.
- Solicite WM_CLOSE com PostMessage, sem bloquear a interface em confirmacoes modais. Confira identidade de processo (PID + inicio), classe e titulo antes de fechar. Nunca confirme avisos automaticamente.
- Persistir o indice/ID atual importa: continuar de um ponto escolhido nao deve voltar silenciosamente a pendencias anteriores. Ofereca selecao manual para voltar.
- Use uma trava exclusiva por raiz de saida. Normalize cada segmento separadamente e detecte colisoes tambem em pastas intermediarias. Teste nomes reservados do Windows, retomada, pausa antes do clique e substituicao atomica sem historico de PNG.
- Testes isolados de aviso, confirmacao, pausa e lote sequencial passaram; testes reais de Estoque por Grade e Preco/Estoque passaram, inclusive pelo painel. Nao execute rotinas destrutivas apenas para testar o mecanismo.

Implementacao do lote: C:\Tina\menu-mapper\BatchCapture.cs, BatchPanel.cs e Capturar-Topbar.ps1. Testes: Test-Batch.ps1 com -MockTest -LiveTest -PanelTest; os testes reais abrem consultas e precisam do desktop livre.

## Falhas de hover e retomada na interface

- Uma falha em abrir submenu pode ocorrer antes de acionar a rotina; registre e confira a etapa antes de atribuir erro ao ERP. Em falha de hover, e possivel tentar um clique apenas se o item foi relido como submenu habilitado, selecionado (MF_HILITE), de mesmo caption e hSubMenu, com pai visivel e foco preservado. Nunca use esse fallback em folhas.
- Botoes de inicio sobre um item em Intervencao devem explicar o bloqueio e oferecer uma nova tentativa com confirmacao explicita. Apenas gravar outro erro no rodape pode parecer que o botao nao funciona. Se a janela real estiver aberta, ofereca captura atual em vez de repetir a acao.
- Nao bloqueie o evento Shown com MessageBox por conflito de F8: duas instancias de teste em raizes diferentes podem disputar a tecla e impedir os handlers seguintes. Mostre aviso nao modal e mantenha o botao Pausar funcional.
- Confira o retorno de PostMessage antes de esperar um fechamento. False nao significa janela ocupada; nao force nem suponha a causa. Solicite fechamento manual quando o Windows recusar.
- Regressao verificada: Test-Batch.ps1 -MockTest -MovementTest passou e gerou movimento-por-produto.png pelo painel; a imagem da rotina que havia falhado foi inspecionada. Testes ficam em raiz separada, preservando o progresso de producao.

## Estrategia atual do lote: resolucao nativa, sem cliques

Priorize esta estrategia para o lote atual em vez dos fallbacks fisicos historicos descritos acima. As correcoes de hover e clique nao eliminaram interrupcoes intermitentes durante sequencias, embora rotinas isoladas abrissem.

- BatchCapture.cs nao usa mais mouse_event nem GetMenuItemRect. Apos validar processo, janela principal habilitada e foco, inicializa o menu com WM_INITMENU (0x0116).
- Percorra os segmentos e posicoes do manifesto; confira caption e habilitacao em cada nivel. Para submenus, envie WM_INITMENUPOPUP (0x0117) com hSubMenu e indice do item pai, releia o item depois da inicializacao e confira se nao surgiu bloqueio/modal.
- Na folha, releia caption, estado e command ID; exija folha habilitada e ID Win32 valido (1..65535). Grave checkpoint antes do disparo e use PostMessage WM_COMMAND (0x0111) com o ID confirmado e lParam zero, exatamente uma vez. Confira o retorno; nunca repita automaticamente uma acao ja enviada.
- Isso remove dependencia de popup visivel, selecao por hover, escala e clique fisico no caminho de abertura. Nao remove necessidade de foco/visibilidade para captura, nem autoriza ignorar avisos ou permissoes.
- Teste transicoes com o painel real atualizando progresso, nao somente a funcao de abertura ou Somente selecionada. Test-Batch.ps1 -SequenceTest executa quatro consultas em raiz de teste, com demais itens pulados apenas nesse manifesto isolado.
- Regressao comprovada: a sequencia Nota Fiscal Simulada -> Analitico de Vencimentos -> Liquidacoes -> Movimentacao por Cliente passou repetidamente no painel, abrindo, capturando e fechando cada tela sem intervencao. O PNG consulta-de-liquidacoes.png foi inspecionado. Progresso de producao nao foi alterado pelos testes.

## Janela VCL grande ou cortada pelo pai MDI

- CopyFromScreen nao resolve uma janela que ultrapassa a tela ou o cliente MDI. Detecte o recorte tambem pelo retangulo cliente do pai convertido para coordenadas de tela, nao apenas pelos limites do desktop.
- No Resulth testado, PrintWindow(hwnd, hdc, 0) renderizou Atualiza Dados de Produtos inteira, incluindo rodape e botoes alem do viewport, sem redimensionar nem alterar campos.
- DPI e importante tambem no PrintWindow: o Delphi sem DPI-awareness imprimiu em pixels logicos dentro de bitmap fisico maior, deixando faixas intocadas. Salve o contexto DPI do chamador; entre no contexto de GetWindowDpiAwarenessContext(hwnd), obtenha GetWindowRect e renderize no tamanho nativo. Restaure o contexto em finally e converta a imagem para o tamanho/recorte fisico esperado.
- PW_RENDERFULLCONTENT (flag 2) retornou sucesso, mas incorporou uma faixa da barra de status do pai na area recortada. Para este caminho VCL, use flag 0 e nao trate flag 2 como fallback equivalente.
- Confira retorno, dimensoes, cores sentinela/intocadas e imagem final. Retorno true nao comprova que todos os controles foram impressos: uma fixture WinForms nao renderizou o rodape fora do viewport. Nao generalize suporte universal nem enfraqueca o teste para aceitar imagem parcial. Valide no aplicativo alvo.
- Diferencie CaptureMethod Screen de WindowRender nos metadados. O fallback aplica-se a janelas geometricamente cortadas, sem remover a protecao contra sobreposicao no modo de captura de tela normal.
- Regressao real: Test-Batch.ps1 -MockTest -LargeWindowTest passou. PNG de Atualiza Dados de Produtos com 1423x879 foi aberto e conferido, com campos, titulo, bordas e botoes inferiores; rotina mantida aberta e progresso de producao preservado.

## Capturar janela atual depois de um aviso

- Teste o handler real do botao, nao apenas BatchScreenshot diretamente. A captura da imagem pode funcionar enquanto a interface fica presa em selecao/foco anteriores ao worker.
- Uma unica janela de rotina sem aviso deve poder ser capturada diretamente; para varias janelas, pre-selecione um item e de feedback se nenhuma selecao existir. Nao deixe Confirmar retornar silenciosamente com SelectedIndex -1. Enter e duplo clique podem confirmar a selecao.
- O modo capture nao deve executar BatchFocus da principal incondicionalmente nem reabrir a rotina. Valide identidade da janela escolhida e foque seu root real. Uma tela modal pode estar habilitada enquanto a principal esta desabilitada; tentar focar a principal nesse caso impede a captura.
- Confira IsWindowEnabled da janela escolhida. Se ela ainda estiver bloqueada por um aviso, peca que o usuario dispense o aviso antes de capturar, em vez de fotografar a tela por tras dele.
- Regressao real: apos o aviso de mascara contabil ja dispensado, Test-Batch.ps1 -CaptureAccountingTest acionou o handler Capturar janela atual, salvou plano-de-contas-contabeis.png e fechou somente a rotina, sem reabri-la nem alterar dados. PNG inspecionado. MockTest tambem passou em captura manual de modal com principal bloqueada.

## Foco de formularios independentes do ERP

- Uma janela top-level do mesmo aplicativo pode nao ter owner ligado a principal: Motivos de trocas (TFrmMotivosTrocas) tinha a si propria como GA_ROOTOWNER. Rejeitar foreground porque nao e a principal nem tem root-owner igual a principal causou pausa falsa antes da captura automatica.
- Depois de acionar a folha, confira o PID do foreground com GetWindowThreadProcessId. Aceite foco no mesmo PID do ERP; continue exigindo janela nova inequivoca, verificacao de avisos, identidade e habilitacao. Outro processo continua sendo perda de foco; nao aceite qualquer janela externa apenas pela aparencia/titulo.
- A verificacao de processo independe do monitor. Preserve coordenadas fisicas/desktop virtual no recorte e nao mova automaticamente a janela para o monitor da principal.
- Regressao: Test-Batch.ps1 -StandaloneTest executou no painel Estoque por Grade (MDI) -> Motivos de Trocas (top-level independente), com abertura, captura e fechamento automaticos, sem o botao de captura manual entre elas. PNG independente inspecionado; o mock tambem passou com a segunda rotina sem MdiParent nem Owner.
