# Detalhamento de Funcionalidades

## 🪞 Modo Espelho (Action Mirroring)
O sistema de espelhamento não é apenas uma replicação de comandos, mas um sincronizador de estados.

- **Captura de Eventos:** Eventos de `mousedown`, `keydown` e `scroll` são capturados na janela mestre.
- **Normalização de Coordenadas:** O software converte a posição do clique para porcentagem relativa (0-100%) da janela para que o clique ocorra no mesmo lugar em instâncias com resoluções diferentes.
- **Broadcast:** O `Main Process` envia o evento para as janelas escravas que utilizam o método `webContents.sendInputEvent()`.

## 🤖 Motor de Automação
A aba de automação permite a execução de scripts em massa sem detecção.

- **Injeção Dinâmica:** Scripts são injetados via `executeJavaScript` em ambientes de `worldSafe`.
- **Prevenção de Detecção:** O motor remove rastros de automação comuns (como a propriedade `navigator.webdriver`) e mascara objetos de automação de alto nível.
