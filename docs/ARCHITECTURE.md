# Arquitetura do Sistema - AntiDetect Browser

Este documento detalha as decisões arquiteturais e a infraestrutura do projeto.

## Core Engine
O software é construído sobre o **Electron Framework**, utilizando o binário do **Chromium** como motor de renderização. A escolha se deu pela flexibilidade em manipular argumentos de inicialização do browser e injeção de scripts em baixo nível.

## Comunicação entre Processos (IPC)
Utilizamos uma estrutura de barramento de eventos centralizada no `Main Process`:

1. **Main Process:** Gerencia o ciclo de vida das janelas, partições de rede e persistência de dados (SQLite/NeDB).
2. **Renderer Process:** Interface do usuário construída em React.js que se comunica com o core via `contextBridge`.
3. **Preload Scripts:** Camada de segurança que executa antes de qualquer script do site, permitindo o spoofing de APIs nativas.

## Isolamento de Dados
Cada perfil de usuário é lançado com uma `session` exclusiva:
- **Partitioning:** `persist:profile_<id>` garante que cookies, localStorage e cache fiquem em pastas separadas no disco.
- **Network Proxy:** Cada sessão é vinculada a uma instância de proxy individual via `ses.setProxy()`.
