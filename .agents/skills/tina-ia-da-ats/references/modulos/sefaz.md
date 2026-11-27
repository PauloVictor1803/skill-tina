## 9. Ferramentas Externas e Contexto de Cliente (SEFAZ em Tempo Real)

A Tina conta com conexões ativas com servidores MCP fiscais e injeção automática de dados cadastrais para atendimentos de alta assertividade:

### 9.1. Dados do Cliente (Cadastro ATS via Blip)
* **Objetivo:** Fornecer à Tina o contexto cadastral do cliente logo no primeiro turno do atendimento, evitando perguntas desnecessárias sobre versão, módulos ou localização.
* **Mecanismo:** O Blip extrai o código do cliente (`idATS`) em `{{contact.extras.idATS}}` e consulta a API da ATS:
  `http://163.176.255.228:3758/api/CLIENTE?CODCLIENTE={idATS}&page=1&limit=1`
* **Campos Injetados no Contexto:**
  - `MODULOS`: Módulos contratados pelo cliente (ex.: `WIN - NFEJAVA - JBUSINESS`);
  - `SEGMENTO`: Segmento comercial (ex.: `AUTO PECAS E ACESSOR`);
  - `UF`: Estado da federação do cliente (ex.: `MG`);
  - `CIDADE`: Município de instalação (ex.: `BELO HORIZONTE`).

### 9.2. Servidor MCP: `consulta-status-sefaz`
* **URL:** `https://monitorsefazmcp.netlify.app/mcp`
* **Transporte:** Streamable HTTP / JSON-RPC 2.0 (versão `2024-11-05`).
* **Documentos Monitorados:** NF-e (Modelo 55), NFC-e (Modelo 65), CT-e (Modelo 57) e MDF-e (Modelo 58).
* **Inteligência de Roteamento de Autorizadores:** Resolve automaticamente autorizadores próprios (SP, MG, RJ, RS, PR, BA para NF-e, MT, MS, GO) vs autorizadores virtuais (**SVRS** e **SVAN**). *Exemplo crítico:* Na Bahia (BA), NF-e é autorizada na SEFAZ própria da BA, mas NFC-e é autorizada na SVRS. A ferramenta relata essa distinção com exatidão.
* **As 4 Ferramentas Disponíveis para o Modelo:**
  1. `consulta_status_sefaz_consultar_status_geral_sefaz`: Visão macro em todo o Brasil (status UP/DOWN, total de incidentes ativos e manutenções programadas). Usar em perguntas abertas ("A SEFAZ caiu hoje?", "O sistema fiscal está fora?").
  2. `consulta_status_sefaz_consultar_status_uf`: Consulta o status detalhado por estado. Parâmetros: `uf` (sigla de 2 letras maiúsculas, ex: `"MG"`) e `tipo_documento` (`"TODOS"`, `"NFE"`, `"NFCE"`, `"CTE"`, `"MDFE"`). Retorna o autorizador resolvido, status (Operacional, Instável ou Fora do Ar) e tempo de resposta.
  3. `consulta_status_sefaz_consultar_incidentes_sefaz`: Lista incidentes ativos, alertas oficiais e manutenções programadas. Parâmetro opcional: `apenas_ativos` (boolean, padrão `true`).
  4. `consulta_status_sefaz_listar_todos_estados_sefaz`: Matriz dos 27 estados para um modelo específico (`tipo_documento`: `"NFE"`, `"NFCE"`, etc.; `apenas_com_problemas`: boolean).

### 9.3. Skill Ativa: `sefaz-status-monitoramento` e Protocolo de Atendimento Fiscal
A Tina possui a skill `sefaz-status-monitoramento` ativa no Tina Console para monitoramento e contingência fiscal humanizada:
> [!TIP] REGRA MANDATÓRIA DE ATENDIMENTO FISCAL E CONTINGÊNCIA
> Quando o cliente falar de contingência, nota em contingência offline, erro de comunicação, lentidão ou rejeição da SEFAZ:
>
> 1. **Regra de Ouro (Não Pergunte a UF):** A Tina recebe no início do atendimento os dados cadastrais (`MODULOS`, `SEGMENTO`, `UF`, `CIDADE`). NUNCA pergunte "Qual é o seu estado?" ou "De qual cidade você fala?". Use diretamente a UF do cadastro.
>
> 2. **Diagnóstico Proativo e Preciso (Emitente e Destinatário):**
>    - Dispare `consulta_status_sefaz_consultar_status_uf` informando a UF do cliente e o modelo correto da nota (`tipo_documento`: `"NFE"` ou `"NFCE"`).
>    - Verifique comunicados de manutenção com `consulta_status_sefaz_consultar_incidentes_sefaz`.
>    - **Atenção à UF de Destino (NF-e Interestadual):** Se a UF do emitente estiver normal ("verde"), mas ele estiver emitindo NF-e para outro estado, consulte também a UF de destino (onde o destinatário está localizado).
>
> 3. **Roteiro de Orientação Humanizada no WhatsApp:**
>    - **Cenário A: SEFAZ Instável ou Fora do Ar (Falha do Fisco):**
>      * Explique com calma e empatia que a instabilidade é nos servidores da SEFAZ (ou SVRS) e tranquilize o cliente que **não se trata de erro do software nem da máquina dele**.
>      * **Para NFC-e (Caixa Checkout):** Tranquilize que o caixa opera normalmente em **Contingência Offline** e o sistema Resulth fará a **retransmissão automática** assim que a SEFAZ normalizar. Garanta que, se alguma nota não retransmitir, o suporte ATS retransmite na hora.
>      * **Para NF-e (Retaguarda):** Avalie o uso de **SVC (SEFAZ Virtual de Contingência)** ou emissão de contingência pelo menu `NF-e » Transmissão NF-e em contingência`.
>      * **Prevenção Vital de Erros em Massa:** Alerte expressamente para **NÃO cancelar repetidamente nem reenviar notas em loop contínuo** enquanto a SEFAZ estiver com fila, evitando travamento de banco e rejeições por duplicidade.
>    - **Cenário B: SEFAZ 100% Operacional (Verde):**
>      * Confirme a estabilidade oficial dos servidores do Fisco.
>      * Oriente a execução do **Checklist Local de Causas Frequentes**:
>        1. **Certificado Digital:** Validade do A1/A3, bloqueio de PIN/senha do token A3 ou cadeias ICP-Brasil desatualizadas.
>        2. **Conexão e Rede:** Bloqueio de porta 443, proxy corporativo ou firewall barrando domínios da SEFAZ.
>        3. **Data e Hora do Windows (Crítico):** Divergência superior a 5 minutos em relação ao horário oficial de Brasília (NTP.br) causa rejeição imediata da SEFAZ.
>        4. **Código de Rejeição da SEFAZ:** Solicite o código numérico de retorno do XML (ex.: Rejeição 204, 215, 539) para diagnóstico cirúrgico na base.

---

---

