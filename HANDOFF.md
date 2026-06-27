# Handoff — Paralu

Última sessão: **2026-06-27**

## Estado atual

- **URL pública:** https://rafaelrfuentes.github.io/paralu_game/
- **Repo:** https://github.com/rafaelrfuentes/paralu_game (público)
- **Local:** `~/code/paralu_game/`
- **Branch main:** commit `2b0ab42` (mais recente)

---

## O que foi feito nesta sessão

### 1. OpenMoji SVG no Canvas — ícones consistentes cross-platform

Problema anterior: `ctx.fillText(emoji)` renderiza diferente por OS/browser.
Solução: três funções que carregam SVGs do CDN e usam `ctx.drawImage`.

| Função | O que faz |
|---|---|
| `_emojiCodigo(emoji)` | Converte emoji para codepoint hex (ex: `1F98B`, `1F577-FE0F`) |
| `_carregarEmoji(emoji)` | Carrega SVG assíncrono com cache em `_emojiImgs` |
| `_desenharEmoji(ctx, emoji, x, y, tamanho)` | `drawImage` se SVG pronto, `fillText` de fallback enquanto carrega |

- Pré-carregamento de todos os 21 personagens + `🌼` `🌳` no boot
- Canvas de captura: flores, árvores, criaturas migrados para `_desenharEmoji`
- Canvas de batalha: personagens (inclui fusão) e projéteis migrados
- `drawImage` não usa `fillStyle` → elimina definitivamente a armadilha #3 do CLAUDE.md

### 2. Sons de erro nos mini-games

- `somErro()` adicionado em `verificarAnagrama()` quando anagrama errado
- `somErro()` adicionado em `confirmarDefesa()` quando conta errada

### 3. Bugs corrigidos

| Bug | Localização | Correção |
|---|---|---|
| `var pJn` dead variable (×2) | `usarGolpe`, `usarGolpeEspecial` | removido |
| `var novoNv` dead variable | `usarGolpe` | removido |
| `var agora = Date.now()` não usado | `desenharBatalha` | removido |
| `cien` hardcoded "Raul" | `realizarFusao` | usa `E.nomeTreinador` |
| Comentário "20 personagens / 5 peixes" | header + data | corrigido para 21 / 6 |

### 4. Deduplicação de lógica de vitória

Extraída `_resolverFimTurno()` — o bloco `setTimeout(600ms → 650ms)` que ficava copiado em `usarGolpe` e `usarGolpeEspecial`. Ambas agora chamam `setTimeout(_resolverFimTurno, 600)`.

---

## Stack de dependências (todas open-source)

| Biblioteca | Licença | Finalidade |
|---|---|---|
| RPG Awesome | MIT | Ícones vetoriais de RPG/fantasia |
| OpenMoji Color | CC BY-SA 4.0 | Emojis flat-art — font CSS + SVGs no Canvas |
| Tone.js 14.8.49 | MIT | Sons 8-bit via Web Audio API + música de fundo |
| Press Start 2P | OFL 1.1 | Fonte pixelada arcade |

---

## Arquitetura atual (resumo)

- `index.html` único (~2940 linhas) — HTML + CSS + JS vanilla, sem build
- Personagens: **21** (15 insetos + 6 peixes — inclui Piranha)
- Mundos: 6 (2 dimensões), dificuldade escalada via array `MUNDOS`
- Batalha: máquina de estados `faseBat` ('escolha' | 'animando' | 'fim')
- Minigames: Captura (d-pad), Anagrama (super poder), Contas (defesa especial)
- Persistência: `localStorage` (`paralu_progresso`)
- Canvas: captura (600×380) + batalha (600×256)
- Música de fundo: Tone.js Transport + `scheduleRepeat`, toggle `🔊/🔇`

---

## Próximos passos sugeridos

| O que | Detalhe |
|---|---|
| Tela de créditos | Mostrar após vitória final: "Criado por Rafa e Raul Fuentes" |
| Sprite Ash animado | Ciclo de caminhada (2-3 frames) com `setInterval` na captura |
| Música por dimensão | Melodia diferente para Floresta Verde e Lago Profundo |
| Dificuldade adaptativa | Reduzir `capVel` / aumentar `capRaio` se jogador errar muito |
| Pokédex de criaturas | Tela listando os 21 com nome científico, desbloqueados ao capturar |
