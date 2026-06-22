# Handoff — Para Lú!

Última sessão: **2026-06-21**

## Estado atual

- **Versão:** v6 (`para_lu_v6.html` + `index.html` para Pages)
- **URL pública:** https://rafaelrfuentes.github.io/paralu_game/
- **Repo:** https://github.com/rafaelrfuentes/paralu_game (público)
- **Local:** `~/code/paralu_game/`

## O que foi feito na sessão 2026-06-21

### Mobile (Galaxy S25 FE em landscape, em uso real com o Raul)

1. `<meta viewport>` no `<head>` — sem ele celular renderizava em modo desktop e encolhia tudo
2. **D-pad touch** na captura: botões ▲◀▶▼ mapeados via `touchstart`/`touchend` para as mesmas `capTeclas` que o teclado usa
3. **Layout landscape** da captura via CSS grid: canvas à esquerda + D-pad à direita (acionado com o polegar direito)
4. **Botão `⛶` fullscreen** flutuante (Fullscreen API com prefixo webkit)
5. **Media query landscape geral** cobre todas as telas (entrada, mapa, mundo, fusão, seleção, batalha, nível): reduz padding/fontes, escala canvas da batalha via `aspect-ratio: 600/256`, grid de seleção vira 8 colunas em 2 linhas

### Correções de bug

| Bug | Causa raiz |
|---|---|
| Tela de captura aparecia sobreposta nas outras telas | `display: grid !important` na media query vencia o `display: none` que o JS aplica via `style` inline. Fix: usar seletor `[style*="display: flex"]` para o grid só ativar quando JS marcou visível |
| Botão "⚔️ Batalhar!" não fazia nada na 1ª tentativa após carregar a página | `confirmarBatalha()` checava `E.jogIdx === null`, mas `E.jogIdx` só é setado DENTRO de `iniciarBatalha()`. Fix: checar `selIdx` (que é setado em `selecionarPers()`) |
| Insetos quase invisíveis na batalha | `fillStyle = 'rgba(0,0,0,0.18)'` da sombra vazava pro `fillText` do emoji (mesma classe do bug v3→v4 da captura). Fix: `save()`/`restore()` cercando a sombra |
| Continuar! na tela de nível travava o jogo sem voltar | `aoSairNivel = mostrarGameOverVitoria` mexia em `area-go` dentro de `tela-batalha`, mas `mostrarNivelUp()` tinha chamado `esconder()` antes. Fix: `aoSairNivel = proximoMundo` (UX mais simples — Continuar! vai direto pro próximo mundo) + fallback `mostrarMapa` em `continuarNivel()` |

### UX

- **Seleção de personagem auto-pré-seleciona** a primeira opção (ou a fusão, se existir) — basta um toque em "Batalhar!" para entrar na luta
- Em landscape, nomes dos personagens ficam ocultos no grid de seleção (só emoji + nível) para caber 16 em 2 linhas

### Documentação

- `CLAUDE.md` criado: doutrina do projeto + armadilhas conhecidas
- `DESIGN.md` criado: referência técnica de fórmulas, tabelas e fluxos
- `README.md` mantido como visão narrativa (foco no player)

## Convenção de arquivos (lembrete)

- `index.html` — versão viva, o que o Pages serve
- `para_lu_vN.html` — snapshot imutável para WhatsApp; gerar `cp index.html para_lu_vN.html` ao final de versão estável
- Detalhes de convenções e armadilhas: `CLAUDE.md`

## Próximos passos (roadmap)

Em ordem de impacto para o Raul:

| O que | Detalhe |
|---|---|
| Direção do sprite | Ash vira para o lado do movimento no mini game (já tem `desenharAsh`) |
| Nome do jogador | Campo de texto na entrada; aparece no log da batalha e na tela de nível |
| Tela de vitória final | Animação especial ao completar os 6 mundos (hoje volta direto pra entrada via `proximoMundo`) |
| Reset de `mundoIdx` após zerar | Após terminar o mundo 6, `E.mundoIdx` fica em 6; idealmente o "JOGAR" volta pro mundo 1 ou abre o mapa |
| Sons | Efeitos sonoros simples para captura, golpes, vitória (Web Audio API) |
| Touch para fusão e seleção | Já funciona via `onclick` em mobile, mas pode ganhar feedback visual de press |

## Stack / restrições

- HTML + CSS + JS vanilla, single file
- Sem framework, sem build step
- Comentários e variáveis em português
- Funções nomeadas pelo que fazem
- Código sempre completo, nunca `...`
- Tudo em `index.html` (~1500 linhas) — quebrar só quando crescer demais
