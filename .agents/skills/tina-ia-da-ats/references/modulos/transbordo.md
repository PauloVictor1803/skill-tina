## 12. Fluxo J: Transbordo Inteligente e Roteamento Avançado de Filas

O gerenciamento de transbordo da Tina conecta o atendimento automatizado diretamente às equipes humanas no Blip Desk com inteligência de dados cadastrais.

### 12.1. Roteamento por Dados do Cadastro (`cliente.CAMPO`)
* As regras de transbordo avaliam dinamicamente o cadastro do cliente sem injetar variáveis desnecessárias no prompt do modelo (`definir_campos_roteamento`):
  * Exemplo: O campo `cliente.CODROTA` determina a equipe de destino imediata:
    - Rota `00000` ➔ Fila **Suporte N1**
    - Rota `00001` ➔ Fila **Suporte Especial**
    - Rota `00002` ➔ Fila **Suporte Premier**
    - Rota `00003` ➔ Fila **ATTO**
    - Rota `00004` ➔ Fila **Revenda**
    - Demais clientes ➔ Fila **Default**
* A ferramenta `salvar_regra_transbordo` permite encadear condições lógicas (`E` / `OU`), operadores (`igual`, `em_lista`, `contem`, `regex`) e mensagens customizadas de transbordo.

### 12.2. Controle de Insistência do Cliente (`definir_pedidos_para_transferir`)
* **Card #152857:** Configura após quantas solicitações explícitas de "falar com atendente / humano" a Tina realiza o transbordo (de 1 a 5 pedidos).
* Se configurado para 2 ou mais, nos primeiros pedidos a Tina tenta acolher a dúvida e solucionar o problema antes de ceder à transferência imediata. Casos fora de escopo, sem resposta ou com erros transferem instantaneamente.

### 12.3. Simulação e Auditoria de Filas
* **`simular_transbordo`:** Permite testar para qual fila um cliente específico iria (passando `id_ats`, `assunto` ou canal), conferindo quais regras casaram sem afetar clientes reais.
* **`relatorio_transbordo`:** Apresenta a distribuição real de transferências por fila, assunto e regra aplicada no período.

---

