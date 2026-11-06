# Genesys Cloud CX — Auto-Unhold Service (B2C)

Serviço leve client-side para o **Genesys Cloud CX** projetado para desativar automaticamente o estado de espera (Hold) em milissegundos assim que acionado pelo operador, garantindo conformidade operacional e prevenindo pausas indevidas no atendimento de voz.

---

## 🚀 Como Funciona

1. **Execução no Navegador do Operador:**
   O serviço é carregado dentro de um componente de página web (**WebPage**) no Script de Atendimento do Genesys Cloud ou como um Client App / Widget.
2. **Autenticação Transparente (Implicit Grant):**
   Utiliza OAuth com a sessão ativa do próprio operador logado no Genesys Cloud (`apps.sae1.pure.cloud`), sem solicitar usuário ou senha.
3. **Escuta em Tempo Real (WebSocket):**
   Conecta na API de Notificações do Genesys Cloud (`v2.users.{userId}.conversations.calls`) e escuta as mudanças de estado da chamada.
4. **Despause Automático (Sub-segundo):**
   No instante exato em que a chamada transita para `held: true`, o script dispara uma requisição `PATCH /conversations/calls/{convId}/participants/{participantId}` com `held: false`, reativando a ligação antes mesmo do áudio de espera iniciar para o cliente.

---

## 🛠️ Configurações no Genesys Cloud

* **OAuth Client ID:** `a743c6f8-f776-4268-8e13-4e3828883a5e`
* **Tipo de Concessão:** `Token (Implicit Grant)`
* **Região:** `sae1` (São Paulo / `sae1.pure.cloud`)
* **Redirect URI:** `https://likas7.github.io/Genesys-unhold-service/`
* **Escopos:** `conversations`, `notifications`, `users`, `user-basic-info`

---

## 📦 Como Embutir no Script de Atendimento

1. No Genesys Cloud, vá em **Admin > Scripts** e abra o Script de Atendimento (ex: `Script_Voz_B2C`).
2. Adicione um componente do tipo **WebPage**.
3. No campo **Web Page Source / URL**, cole:
   ```
   https://likas7.github.io/Genesys-unhold-service/
   ```
4. Para modo de teste/homologação, ajuste a altura para `350px` para visualizar o console e as métricas em tempo real.
5. Para modo de produção invisível, ajuste largura e altura para `1px` no rodapé da página.
