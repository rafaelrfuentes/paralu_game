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
