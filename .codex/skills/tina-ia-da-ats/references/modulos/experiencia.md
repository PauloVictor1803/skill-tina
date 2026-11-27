## 13. Fluxo K: Comportamento de Envio, UX WhatsApp e Encerramento com Pesquisa CSAT

A experiência do cliente no chat é configurada de forma granular para garantir humanização e clareza.

### 13.1. Cadenciamento de Envio (`configurar_envio`)
* **Quebra de Mensagens Longas (`dividir` / `max_partes`):** Divide respostas longas em parágrafos individuais (2 a 8 partes), preservando listas estruturadas unidas.
* **Status "Digitando..." Dinâmico:** Exibe o indicador visual de digitação proporcional ao tamanho da resposta (`caracteres_por_s`, `atraso_min_s`, `atraso_max_s`).
* **Espera Mínima na 1ª Mensagem (`atraso_min_primeira_s`):**
  - Ajustado na **v98 (Card #153028)** em **2 segundos**.
  - Garante uma pausa natural antes da primeira resposta após turnos longos de transcrição de áudio e antes do disparo de notas de voz.
* **Avisos Prévios:**
  - `aviso_analise`: Informa ao cliente que a assistente está consultando a base quando a busca vetorial for acionada.
  - `aviso_audio`: Confirma o recebimento de mensagem de voz enquanto processa a transcrição.

### 13.2. Encerramento Humanizado e Pesquisa CSAT (`configurar_encerramento`)
* **Confirmação Prévia (`perguntar_mais_algo`):** A Tina não encerra abruptamente; pergunta primeiro: *"Posso ajudar em mais alguma coisa?"* e aguarda retorno.
* **Pesquisa de Satisfação Nativa (`avaliacao_ativa`):**
  - Solicita nota de 1 a 5 (`pergunta`);
  - Se habilitado `pedir_comentario`, solicita justificativa ou feedback em texto;
  - Envia mensagem de agradecimento e fecha formalmente o atendimento após resposta ou tempo de tolerância (`espera_min`).

### 13.3. Rodízio de Personas (`configurar_nomes`)
* Permite definir rodízio de nomes de atendente por conversa (ex.: `["Tina", "Rita", "Patricia"]`) para testes A/B de engajamento e acolhimento humanizado, acompanhados pelo `relatorio_nomes`.

### 13.4. Marcação de Conversas de Teste (`marcar_conversa_teste`)
* Permite sinalizar conversas realizadas pela equipe interna de QA/suporte como testes, expurgando esses diálogos dos relatórios de assertividade e tempos médios de resposta.

---

