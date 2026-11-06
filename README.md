# [S4ME] Genesys Cloud Auto-Unhold & Protection Service (B2C)

Serviço ultraleve client-side em Vanilla JavaScript para o **Genesys Cloud CX**, projetado para monitorar em tempo real e neutralizar instantaneamente ações indevidas de atendentes durante o atendimento de voz, garantindo conformidade operacional com latência sub-segundo (~50-70 ms) e sem consumo excessivo de cota de API.

---

## 🛡️ Pilares de Proteção do Produto

1. **Auto-Unhold (Anti-Espera):**
   - Detecta no WebSocket quando `agentParticipant.held === true`.
   - Executa `PATCH /conversations/calls/{convId}/participants/{participantId}` com `{ "held": false }`.
   - Despausa a chamada antes do início do áudio de espera.

2. **Auto-Unmute (Anti-Mudo):**
   - Detecta quando o operador coloca a chamada no mudo (`agentParticipant.muted === true`).
   - Executa `PATCH /conversations/calls/{convId}/participants/{participantId}` com `{ "muted": false }`.
   - Reativa o microfone do operador instantaneamente.

3. **Auto-Kill Conference (Anti-Consulta/Conferência):**
   - Detecta qualquer perna de chamada iniciada com finalidade de consulta (`purpose === 'consult'`).
   - Executa `PATCH /conversations/calls/{convId}/participants/{consultParticipantId}` com `{ "state": "disconnected" }`.
   - Derruba a consulta imediatamente, impedindo a criação de conferência paralela.

---

## 🚀 Como Funciona

1. **Execução no Navegador do Operador:**
   O serviço é carregado dentro de um componente de página web (**WebPage**) no Script de Atendimento do Genesys Cloud ou como Client App / Widget.
2. **Autenticação Transparente (Implicit Grant SSO):**
   Utiliza a sessão ativa do operador logado no Genesys Cloud (`apps.sae1.pure.cloud`), sem solicitar credenciais adicionais.
3. **Escuta em Tempo Real (WebSocket):**
   Conecta na API de Notificações do Genesys Cloud (`v2.users.{userId}.conversations.calls`) e escuta as mudanças de estado da chamada.
4. **Alerta Visual e Sonoro:**
   - **Banner:** Exibe aviso visual imediato e muda de cor para vermelho pulsante durante a violação.
   - **Áudio:** Emite bipe sintetizado nativo via Web Audio API (sem dependência de arquivos externos).

---

## 🛠️ Configurações no Genesys Cloud

* **OAuth Client ID:** `a743c6f8-f776-4268-8e13-4e3828883a5e`
* **Tipo de Concessão:** `Token (Implicit Grant)`
* **Região:** `sae1` (São Paulo / `sae1.pure.cloud`)
* **Redirect URI:** `https://likas7.github.io/Genesys-unhold-service/`
* **Escopos:** `conversations`, `notifications`, `users`, `user-basic-info`

---

## 📦 Como Embutir no Script de Atendimento

1. No Genesys Cloud, vá em **Admin > Scripts** e abra o Script de Atendimento (ex: `Script_Voz_B2C_Teste_Callback`).
2. Adicione um componente do tipo **WebPage**.
3. No campo **Web Page Source / URL**, cole:
   ```
   https://likas7.github.io/Genesys-unhold-service/?v=3
   ```
4. Dimensões recomendadas:
   - **Modo Banner Integrado:** Largura `stretch` (100%), Altura `52px`, Margem `[4, 0, 8, 0]`.
   - **Modo Dashboard / Diagnóstico:** Altura `350px` a `500px` para visualizar métricas e log em tempo real.
   - **Modo Invisível:** Largura `1px` e Altura `1px`.
