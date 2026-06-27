# Handoff — Paralu

Última sessão: **2026-06-27**

## Estado atual

- **URL pública:** https://rafaelrfuentes.github.io/paralu_game/
- **Repo:** https://github.com/rafaelrfuentes/paralu_game (público)
- **Local:** `~/code/paralu_game/`
- **Branch main:** commit `53d8062`

---

## O que foi feito nesta sessão

### 1. Pokédex de criaturas — implementação completa

Tela acessível pelo botão no mapa dos mundos.

**Funcionamento:**
- Grid 5×5 com as 21 criaturas
- Criatura revelada (emoji colorido + nome + tipo + nível) quando capturada no mini game
- Antes de capturar: card escuro com `?`
- Clicar numa criatura revelada abre painel de detalhe embaixo com borda dourada no card selecionado
- Clicar novamente fecha o detalhe
- Contador "X / 21 descobertas" no topo

**Persistência:**
- `E.descobertas` — Set com nomes das criaturas já capturadas alguma vez
- Persiste em `localStorage` junto com o resto do progresso
- Reseta com "Recomeçar do início"

### 2. Pokédex enriquecida — dados científicos (museu de animais)

Cada uma das 21 criaturas tem ficha completa no painel de detalhe:

| Campo | Descrição |
|---|---|
| 📍 Origem | Bandeira + região geográfica |
| 🍽️ Dieta | O que a criatura come |
| 📏 Tamanho | Dimensão real do animal |
| 💡 Curiosidade | Fato científico divertido para criança |

Exemplo — Borboleta-monarca:
- Origem: 🌎 América do Norte e Central
- Dieta: Néctar de flores (larva: folhas de algodão-de-seda)
- Tamanho: 9–10 cm de envergadura
- Curiosidade: Migra até 4.500 km por ano entre o Canadá e o México!

**Design:** ficha em grid compacto + curiosidade com destaque verde (borda esquerda) estilo museu de história natural.

### 3. Remoção do efeito de listras (CRT scanlines)

- Removido o pseudo-elemento `body::after` que simulava CRT scanlines, eliminando a textura de listras pretas e coloridas que prejudicava a visibilidade do jogo.

### 4. Correção de layout Fullscreen/Landscape na captura

- Removida a restrição `max-height` da media query da `#tela-captura` para habilitar o layout lateral (canvas à esquerda, D-pad à direita) em qualquer tela horizontal (incluindo desktops e notebooks em fullscreen).
- Adicionado `min-height: 0` no canvas de captura e configurado `grid-template-rows: auto minmax(0, 1fr) auto` para quebrar a dependência circular da altura intrínseca (380px) do canvas em telas curtas, prevenindo estouros de grid.
- Adicionado `overflow: hidden !important` condicionado a `body:has(#tela-captura)` em landscape para travar a tela e evitar qualquer rolagem vertical acidental.
- Refatorado o seletor do grid para `#tela-captura:not([style*="display: none"])` para garantir ativação robusta cross-platform, independentemente de espaços na serialização do `display: flex`.

### 5. Substituição de sprites genéricas por Caranguejo e Polvo

- Substituídas as entradas que compartilhavam o emoji genérico `🐟` por emojis exclusivos:
  - **Caranguejo-azul** (`🦀` - `Callinectes sapidus`), com golpes temáticos (*Pinçada Rápida*, *Carapaça Dura*, etc.).
  - **Polvo-comum** (`🐙` - `Octopus vulgaris`), com golpes temáticos (*Jato de Tinta*, *Abraço de Tentáculos*, etc.).
- Com isso, todas as 21 criaturas têm emojis 100% exclusivos (sem duplicidade).
- Mantido `tipo: 'peixe'` no array `PERS` para compatibilidade com a física de colisão do lago no mini-game de captura, mas atualizados os labels da GUI de "peixes" para "aquáticos/criaturas".

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

- `index.html` único (~3100 linhas) — HTML + CSS + JS vanilla, sem build
- Personagens: **21** (15 insetos + 6 aquáticos: peixe-palhaço, tubarão, baiacu, caranguejo-azul, água-viva, polvo)
- Mundos: 6 (2 dimensões), dificuldade escalada via array `MUNDOS`
- Batalha: máquina de estados `faseBat` ('escolha' | 'animando' | 'fim')
- Minigames: Captura (d-pad), Anagrama (super poder), Contas (defesa especial)
- Persistência: `localStorage` (`paralu_progresso`) — inclui `descobertas[]`
- Canvas: captura (600×380) + batalha (600×256)
- Música de fundo: Tone.js Transport + `scheduleRepeat`, toggle 🔊/🔇

---

## Próximos passos sugeridos

| O que | Detalhe |
|---|---|
| Tela de créditos | Mostrar após vitória final: "Criado por Rafa e Raul Fuentes" |
| Sprite Ash animado | Ciclo de caminhada (2-3 frames) com `setInterval` na captura |
| Música por dimensão | Melodia diferente para Floresta Verde e Lago Profundo |
| Dificuldade adaptativa | Reduzir `capVel` / aumentar `capRaio` se jogador errar muito |
| Filtro na Pokédex | Botão para alternar entre ver só insetos / só aquáticos / todos |
