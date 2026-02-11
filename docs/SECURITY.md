# Protocolos de Segurança e Antidetecção

Este documento detalha as camadas de proteção implementadas no motor do navegador para garantir o isolamento de perfis e a evasão de sistemas de rastreamento digital (Fingerprinting).

## 1. Proteção de Hardware e Renderização

### Canvas Fingerprinting
O navegador utiliza uma técnica de injeção de ruído (noise) nas APIs de imagem para mascarar a assinatura única da renderização de hardware.
* **Implementação:** Interceptação de chamadas `toDataURL` e `getImageData` para adicionar modificações imperceptíveis nos pixels.
* **Status Atual:** ⚠️ **Em fase de aprimoramento.** A funcionalidade está sob desenvolvimento contínuo e refinamento técnico. No estágio atual, o motor pode não ser aprovado em 100% dos testes de fingerprinting sintéticos (como o BrowserLeaks ou Pixelscan), mas está sendo otimizado para aumentar a taxa de evasão em atualizações futuras.

### Hardware Masking
Substituição programática de metadados para evitar o cruzamento de dados de hardware real:
* `navigator.hardwareConcurrency`: Mascaramento do número de núcleos da CPU.
* `navigator.deviceMemory`: Emulação da quantidade de memória RAM.
* **WebGL Vendor/Renderer:** Substituição das informações da placa de vídeo (GPU).

## 2. Gerenciamento de Localização e Localidade (Geo-Spoofing)

O sistema oferece controle granular sobre a identidade geográfica de cada perfil, garantindo coerência com o ponto de saída da rede (Proxy):

* **Fuso Horário (Timezone):** Emulação dinâmica baseada na localização do Proxy ou configuração manual (ex: `America/Sao_Paulo`), impedindo a detecção de discrepâncias entre o relógio do sistema e o IP.
* **Geolocalização (Lat/Long):** Sobrescrita das coordenadas informadas pela API nativa do navegador via protocolo de depuração (CDP). Permite definir Latitude e Longitude exatas para cada perfil.
* **Idioma e Região:** Ajuste automático das propriedades `navigator.language` e dos headers de requisição `Accept-Language`, garantindo que o site visualize o idioma condizente com o perfil criado.

## 3. Isolamento de Rede e Privacidade

* **Proxy Per-Profile:** Integração nativa com protocolos HTTP e SOCKS5. Cada sessão (perfil) possui um túnel de rede isolado.
* **WebRTC Leak Protection:** Configuração de políticas de IP para impedir o vazamento do endereço IP real (local) através de conexões RTC, expondo apenas o IP do Proxy.
* **Isolamento de Cache/Cookies:** Utilização de partições exclusivas (`persist:profile_id`) no Electron para garantir que os dados de navegação de um perfil jamais cruzem com outros.

## 4. User-Agent e Consistência

O software permite a alteração completa da string de `User-Agent`. O motor garante que a declaração do navegador (ex: Chrome no Windows) seja consistente com as capacidades de hardware emuladas, evitando "flags" de inconsistência em algoritmos de segurança avançados.
