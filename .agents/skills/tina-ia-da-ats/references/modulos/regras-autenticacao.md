### Dificuldade 6: Expiração dos Tokens OAuth (Tina Console e Google Sheets)
* **O que acontece:** Os tokens OAuth em `mcp_oauth_tokens.json` expiram com o tempo, gerando erros como `calling "initialize": Unauthorized` ou `HTTP 401: Unauthorized`.
* **Regra de Ouro para o Tina Console:**
  - Quando a ferramenta MCP da Tina falhar com `Unauthorized`, execute no terminal ou peça para o agente:
    ```bash
    python ats.py auth-tina
    ```
    Isso inicia um servidor temporário na porta 8080, gera o desafio PKCE (S256) e abre o navegador em `https://auth.central.atsinformatica.com.br/oauth/authorize`.
  - O usuário faz login, clica em **"Entrar e autorizar"** e o script salva automaticamente os novos tokens em `C:\Users\paulo.magalhaes\.gemini\antigravity-ide\mcp_oauth_tokens.json`.
* **Regra de Ouro para o Google Sheets:**
  - Quando o Sheets der 401, o script `ats.py sync` automaticamente gera o Excel local em `planilhas/Relatorio_ATS_v3.xlsx`. Sempre mantenha `dados/metadados_base.json` e `dados/auditoria.json` atualizados, pois eles alimentam o sync quando o token for renovado.

### Dificuldade 14: Fluxo de Autenticação OAuth PKCE no Tina Console MCP (`auth_tina.js`) e Cuidados de Sessão
* **O que acontecia:**
  - Ao clicar em "Autorizar", o navegador exibia `ERR_CONNECTION_REFUSED` em `localhost:8080/callback?code=...`.
  - Ao tentar recarregar a tela após iniciar o script, o terminal registrava `HTTP 400: {"error":"invalid_grant","error_description":"PKCE não confere"}` e o processo morria.
  - O agente executava o script em background e ficava esperando o output estabilizar por vários minutos sem mostrar o link de autenticação no chat, deixando o usuário sem saber o que fazer e causando atrasos de até 10 minutos.
* **Causas Raiz:**
  1. O navegador só consegue entregar o código de autorização se o script local (`scripts/auth_tina.js`) já estiver rodando em segundo plano ouvindo ativamente na porta `8080`. Se o script for finalizado antes do clique, a conexão é recusada.
  2. No fluxo PKCE (S256), cada execução de `auth_tina.js` gera um par único e aleatório de `code_verifier` e `code_challenge`. Se o usuário der "F5" ou recarregar uma URL antiga contendo um `code` de uma sessão anterior, a Central ATS rejeita com *"PKCE não confere"*.
  3. Se o script fechar o processo (`process.exit`) no primeiro erro capturado, ele derruba o servidor antes de receber a autorização válida da nova janela aberta.

> [!CAUTION] REGRA MANDATÓRIA: SEMPRE ENVIAR O LINK DE AUTENTICAÇÃO DIRETAMENTE NO CHAT
> Quando for necessário renovar o token da Tina (erros `Unauthorized`, `401`, ou solicitação explícita do usuário):
>
> 1. **Limpe a porta 8080** matando qualquer processo `node` travado (`Get-NetTCPConnection -LocalPort 8080 | ForEach-Object { Stop-Process -Id $_.OwningProcess -Force }`).
> 2. **Inicie o `auth_tina.js`** em background com `WaitMsBeforeAsync` de 3000ms para capturar a URL.
> 3. **Leia imediatamente o log** do task para extrair a linha `AUTH_URL_PRONTA:`.
> 4. **Cole o link clicável direto na mensagem do chat** em formato markdown: `[Autorizar Tina Console](URL_AQUI)`.
> 5. **NUNCA dependa exclusivamente da abertura automática do navegador** pelo `exec('start ...')`. O navegador pode não abrir, o popup pode ser bloqueado, ou o usuário pode estar operando por outro dispositivo.
> 6. **NUNCA fique esperando silenciosamente** o output do script estabilizar sem ter enviado o link ao usuário. O tempo máximo entre iniciar o script e exibir o link no chat deve ser inferior a 30 segundos.
> 7. O processo inteiro (limpar porta → subir servidor → enviar link no chat) deve levar **no máximo 30 segundos**, nunca minutos.

* **Regra de Ouro e Procedimento Operacional:**
  - O servidor `auth_tina.js` deve permanecer escutando mesmo se receber um callback antigo ou inválido, exibindo mensagem amigável e aguardando a nova autorização.
  - Ao renovar a autenticação, **o usuário deve sempre utilizar a nova janela aberta** (ou o link direto enviado no chat) e clicar em **"Autorizar"**, nunca recarregando abas anteriores com `code` já expirado.
  - O script deve persistir o token em `mcp_oauth_tokens.json` tanto pela URL do recurso (`https://mcp.central.atsinformatica.com.br/tina/mcp`) quanto pelo slug `tina`, garantindo compatibilidade imediata com o MCP da IDE e com scripts Python/Node locais.

### Dificuldade 30: Autenticação da CLI do Gemini Notebook (`nlm login`) no Windows: Proibição de Execução em Prompt Elevado (Administrador)

* **O que acontece:** Ao executar `nlm login` em um terminal aberto como Administrador (`Administrador: Prompt de Comando` ou `PowerShell com privilégios elevados`), o comando realiza o fluxo no navegador, mas o Windows salva o arquivo `credentials.enc` com propriedade exclusiva do Administrador e criptografa o envelope via DPAPI (`CryptProtectData`) atrelada ao token de segurança elevado.
* **O erro gerado:**
  1. Quando o Antigravity ou o servidor MCP da IDE (que rodam como processo de usuário normal, não-elevado) tentam ler o perfil em `~/.notebooklm-mcp-cli/profiles/default/`, o Windows bloqueia o acesso ao arquivo com:
     `PermissionError: [Errno 13] Permission denied: '.../credentials.enc'`
  2. Como consequência, a biblioteca de credenciais não consegue decodificar o envelope e lança:
     `CorruptCiphertextError: Envelope is corrupt or not valid JSON`
* **Regra de Ouro Inviolável:**
  - **NUNCA execute `nlm login` em terminal de Administrador.**
  - O comando `nlm login` DEVE ser executado **exclusivamente em um terminal de usuário comum** (Prompt de Comando ou PowerShell normal).
  - Caso o login tenha sido realizado acidentalmente como Administrador:
    - *Solução 1 (Rápida na janela Admin aberta):* Conceda permissão total ao usuário atual executando:
      `icacls "%USERPROFILE%\.notebooklm-mcp-cli\profiles\default\*" /grant "%USERNAME%":F /T`
    - *Solução 2 (Definitiva):* Na janela de Administrador, delete o arquivo corrompido com `del "%USERPROFILE%\.notebooklm-mcp-cli\profiles\default\credentials.enc"`, abra um terminal comum de usuário e rode `nlm login` novamente.


