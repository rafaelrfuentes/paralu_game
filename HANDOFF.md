# Handoff — Paralu

Última sessão: **2026-06-27**

## Estado atual

- **URL pública:** https://rafaelrfuentes.github.io/paralu_game/
- **Repo:** https://github.com/rafaelrfuentes/paralu_game (público)
- **Local:** `~/code/paralu_game/`
- **Branch main:** commit `0baca5f` (mais recente)

---

## O que foi feito nesta sessão

### 1. RPG Awesome — ícones vetoriais (MIT)

CDN: `cdn.jsdelivr.net/gh/nagoshiashumari/Rpg-Awesome@master`

- Menu mundo: `ra-leaf` (Capturar), `ra-crystals` (Fusão), `ra-crossed-swords` (Batalha), `ra-lightning-bolt` (Super Poderes), `ra-shield` (Defesa Especial)
- Topo-labels: mesmos ícones em todas as telas
- Botões de golpe: tier por posição — `ra-sword` / `ra-crossed-swords` / `ra-lightning-bolt` / `ra-skull`
- Custo de golpe: `ra-fire` (dano) + `ra-droplet` (mana)
- Botão Defender e Super Poder em batalha: RA icons via `innerHTML`

### 2. OpenMoji Color — emojis flat-art (CC BY-SA 4.0)

CDN: `cdn.jsdelivr.net/npm/openmoji@15.1.0/font/OpenMoji-Color.woff2`

- `@font-face` com `font-display: swap`
- Aplicado em `.sc-em`, `.fs-em`, `.fr-em`, `.cc-em`, `.ce-icone`, `.ana-emoji`, `.nv-emoji`, `.col-em`
- Canvas: todas as 6 chamadas `ctx.font` prefixadas com `"OpenMoji"` (captura e batalha)

### 3. Regra de licenciamento open-source (CLAUDE.md)

- Licenças aceitas documentadas: MIT, Apache 2.0, OFL, GPL, LGPL, CC0, CC BY, CC BY-SA
- Proibido: freemium, open-core, NC/ND, qualquer tier pago
- Tabela de dependências aprovadas com licença verificada

### 4. Tone.js — sons 8-bit chiptune (MIT)

CDN: `cdnjs.cloudflare.com/ajax/libs/tone/14.8.49/Tone.js`

- `_sfxMel`: PolySynth onda quadrada (timbre chiptune)
- `_sfxHit`: NoiseSynth ruído branco (impactos)
- Lazy init no primeiro click/touch (exigência da AudioContext API)
- 12 funções de som hookadas nos eventos do jogo:

| Função | Evento |
|---|---|
| `somGolpe()` | jogador ataca |
| `somErro()` | mana insuficiente |
| `somImpacto()` | impacto no inimigo |
| `somDano()` | jogador recebe dano |
| `somDefesaBloqueou()` | defesa reduz dano |
| `somDerrota()` | vida do jogador chega a 0 |
| `somVitoria()` | `mostrarGameOverVitoria()` |
| `somNivelUp()` | `mostrarNivelUp()` |
| `somCaptura()` | criatura capturada na captura |
| `somFusao()` | `realizarFusao()` com sucesso |
| `somSuperPoder()` | `usarGolpeEspecial()` |
| `somDefesaAtivada()` | `ativarDefesa()` |

### 5. Estética Cyberpunk Neon Lo-Fi — `plano_cyberpunk_neon.md`

- **Fonte:** Press Start 2P (OFL 1.1) via Google Fonts — aplicada globalmente, base 9px
- **Fundo:** `#040914` (era `#08101e`) + meta theme-color
- **`@keyframes brilhar`:** ciano `#00f0ff` ↔ magenta `#ff2a85` (era verde)
- **Painéis:** `border: 2px solid #00f0ff` + `box-shadow` neon ciano
- **Fusão painel:** `border: #ff2a85` magenta neon
- **CRT scanlines:** `body::after` com grid 4px+3px, z-index 9999, pointer-events none
- **Canvas gradiente:** `#040914 → #0a152d`
- **Plataformas:** ciano `#00b8d4` (inimigo) + rosa `#c2185b` (jogador)
- **Estrelas:** 28 estrelas piscando em ciano/rosa/branco — código existia mas nunca desenhava; loop adicionado
- **Botão golpe hover:** glow ciano inset

### 6. Feedback visual de toque (commit Gemini `0baca5f`)

- `.card-cap:active`: scale 0.95 + glow ciano
- `.fusao-slot:active`: scale 0.96
- `.sel-card:active`: scale 0.95 + border ciano

### 7. `.gitignore` — imagens de referência

Adicionado `*.jpg`, `*.png`, `*.jpeg`, `.~lock.*` para não versionar mockups e sprites de referência local.

---

## Stack de dependências (todas open-source)

| Biblioteca | Licença | Finalidade |
|---|---|---|
| RPG Awesome | MIT | Ícones vetoriais de RPG/fantasia |
| OpenMoji Color | CC BY-SA 4.0 | Emojis flat-art para criaturas |
| Tone.js 14.8.49 | MIT | Sons 8-bit via Web Audio API |
| Press Start 2P | OFL 1.1 | Fonte pixelada arcade |

---

## Arquitetura atual (resumo)

- `index.html` único (~2800 linhas) — HTML + CSS + JS vanilla, sem build
- Personagens: 21 (15 insetos + 5 peixes + Piranha)
- Mundos: 6 (2 dimensões), dificuldade escalada via array `MUNDOS`
- Batalha: máquina de estados `faseBat` ('escolha' | 'animando' | 'fim')
- Minigames: Captura (d-pad), Anagrama (super poder), Contas (defesa especial)
- Persistência: `localStorage` (`paralu_progresso`)
- Canvas: captura (600×380) + batalha (600×256)

---

## Próximos passos sugeridos

| O que | Detalhe |
|---|---|
| Sprite artwork | Emojis no Canvas dependem do OS. Solução definitiva: sprites PNG base64 por personagem |
| Tela de vitória final | Animação especial ao completar os 6 mundos |
| Nome do jogador | Campo de texto na entrada; aparece no log da batalha |
| Sons adicionais | Som de erro na defesa/anagrama, música de fundo com Tone.js |
| Reset pós-zeramento | Após mundo 6 concluído, `E.mundoIdx` fica em 6; UX não fecha o loop |
