# Handoff — Paralu

Última sessão: **2026-07-05**

## Estado atual

- **URL pública:** https://rafaelrfuentes.github.io/paralu_game/
- **Repo:** https://github.com/rafaelrfuentes/paralu_game (público)
- **Local:** `~/code/paralu_game/`
- **Branch main:** commit `2c17438` (não inclui o fix mais recente — ver pendência abaixo)
- **Branch agy-dev-paralu:** commit `1e3c2b6` (à frente da main, aguardando merge)

---

## ⚠️ Pendência: aguardando validação do usuário pra mesclar na main

Commit `1e3c2b6` em `agy-dev-paralu` (fixes do Combate de Rua, ver seção da sessão
2026-07-05 abaixo) **ainda não foi mesclado na main**. Perguntei ao usuário "posso
mesclar?" e a sessão foi encerrada antes da resposta. Próxima sessão: confirmar
com o usuário e rodar `git checkout main && git merge agy-dev-paralu && git push`,
depois voltar para `agy-dev-paralu`.

---

## Sessão 2026-07-05 — Novo modo "Combate de Rua" (beat 'em up) + fixes

### 1. Novo modo de jogo: Combate de Rua

Beat 'em up estilo *Cadillacs and Dinosaurs* (arcade Capcom), reaproveitando os
sprites/emoji dos 21 personagens existentes como heróis jogáveis. Acessível pelo
botão 👊 na tela do mundo, usa a mesma tela de seleção de personagem da batalha
(botão de confirmar muda o texto conforme o destino).

- Tela nova `#tela-brawl`: canvas 600×380 com scroll lateral, D-pad + botão de
  soco (touch/teclado), painel de vida do herói e do chefe.
- 3 ondas de capangas (personagens aleatórios do elenco, exceto o herói e o
  chefe) + luta contra chefe único por mundo (Aranha-de-jardim nos mundos da
  dimensão 1, Tubarão-branco nos da dimensão 2).
- Reaproveita `_desenharEmoji`, partículas, texto flutuante e screen-shake já
  existentes no jogo — sem duplicar renderização.
- Commit `2c17438` (mesclado na main na hora, a pedido do usuário).

### 2. Fix: inimigos não atacavam + jogo "bugava" depois do chefe

Usuário reportou dois bugs no modo novo. Investigação com Playwright (ambiente
isolado fora do repo, ver nota abaixo) revelou duas causas reais:

- **Zona morta na IA**: inimigo parava de perseguir a 40px de distância mas só
  atacava a menos de 36px — ficava parado pra sempre sem nunca acertar o herói.
  Corrigido: distância de parada reduzida pra 26px (dentro do alcance de ataque).
- **Câmera sem trava**: a câmera seguia o herói livremente, então um jogador
  andando pra frente (comportamento natural) sempre deixava os inimigos (mais
  lentos) pra trás — sem combate real, exatamente como "inimigos não atacam".
  Corrigido: adicionado `BR.travado` — tela trava (sem scroll, herói preso na
  área visível) durante cada onda de capangas e durante a luta do chefe, como
  nos beat 'em up clássicos de arcade. Destrava quando os capangas da onda
  atual caem.
- Bônus: zera `capTeclas` de movimento/soco ao iniciar o combate de rua, pra
  evitar estado de tecla residual entre transições de tela.
- Testado via Playwright: ciclo completo onda→onda→chefe→vitória→"Tentar de
  novo"→voltar ao mundo, sem erros JS. Não foi possível reproduzir um crash
  explícito após matar o chefe mesmo antes do fix — hipótese é que era
  percepção/efeito colateral do bug de câmera (combate trivial demais fazia
  a vitória parecer "quebrada").
- Commit `1e3c2b6` em `agy-dev-paralu`, **ainda não mesclado na main** (ver
  pendência no topo do arquivo).

### Nota técnica: ambiente de teste Playwright

Instalado inteiramente fora do repo (pasta de sessão do agente, não em
`~/code/paralu_game/`) pra respeitar a regra de "sem dependências/build step"
do projeto. Scripts usam `page.evaluate()` pra chamar funções do jogo
diretamente (`mostrarMapa()`, `confirmarBatalha()`, inspecionar `window.BR`) —
padrão rápido e eficaz pra testar este jogo em Canvas sem automação de UI
completa. Útil reaproveitar esse padrão em sessões futuras de debug.

---

## O que foi feito na sessão 2026-06-27

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
