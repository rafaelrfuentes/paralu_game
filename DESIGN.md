# DESIGN.md — Para Lú!

Referência técnica: fórmulas, tabelas e fluxos. Para a visão narrativa do jogo ver `README.md`. Para convenções de código e armadilhas ver `CLAUDE.md`.

## Mundos

| # | Dimensão | Nome | Vida/Mana inimigo | Golpes do inimigo | ⭐ |
|---|---|---|---|---|---|
| 1 | Floresta Verde | Clareira | 100 | 2 | ⭐ |
| 2 | Floresta Verde | Floresta Densa | 135 | 3 | ⭐⭐ |
| 3 | Floresta Verde | Pântano | 170 | 4 | ⭐⭐⭐ |
| 4 | Lago Profundo | Margem do Lago | 210 | 4 | ⭐⭐⭐⭐ |
| 5 | Lago Profundo | Águas Fundas | 260 | 4 | ⭐⭐⭐⭐⭐ |
| 6 | Lago Profundo | Fundo do Abismo | 320 | 4 | ⭐⭐⭐⭐⭐⭐ |

Curva de escalada do inimigo: +35 (1→2→3→4), depois +50 (4→5) e +60 (5→6). Número máximo de golpes do inimigo estaciona em 4.

## Personagens (16 = 11 insetos + 5 peixes)

Cada um tem 4 golpes ordenados do mais fraco ao mais forte (último é o "especial" que ganha bônus por nível). Definidos no array `PERS[]` em `index.html`.

| Tipo | Lista |
|---|---|
| Insetos 🐛 | Borboleta-monarca, Abelha-europeia, Joaninha, Gafanhoto-verde, Besouro-hércules, Lagarta-da-couve, Mosquito-da-dengue, Mosca-doméstica, Formiga-saúva, Barata-americana, Voador Azul (fictício) |
| Peixes 🐟 | Peixe-palhaço, Piranha, Baiacu, Peixe-espada, Arraia |

Formato de cada golpe: `{n: nome, i: ícone, d: dano base, m: custo de mana, t: texto narrativo}`.

## Fórmulas de combate

Para o personagem **jogador** ao entrar na batalha:

```
maxVida = vida_base + bonus_captura + bonus_nivel
maxMana = mana_base + bonus_captura + bonus_nivel
dano_especial = dano_base_do_golpe + extra_nivel

onde:
  vida_base, mana_base = 100 (padrão)
  bonus_captura = E.capMundo.size × 5
  bonus_nivel   = (nivel - 1) × 10
  extra_nivel   = (nivel - 1) × 3   (só no último golpe)
```

A **fusão não tem nível** (`nomeJog === null`). Usa stats base sem bônus de nível, mas recebe bônus de captura.

### Exemplo numérico

Joaninha Nv.3, com 5 capturas no mundo atual:

- maxVida = 100 + (5 × 5) + ((3-1) × 10) = **145**
- maxMana = 100 + 25 + 20 = **145**
- "Armadura Total" (último golpe, d=30) = 30 + (3-1) × 3 = **36** de dano

## Persistência

Estado global em `E` (objeto). Persistido em `localStorage['paralu_progresso']`.

| Chave | Reseta entre mundos | Persiste em localStorage |
|---|---|---|
| `mundoIdx` | n/a | ✅ |
| `concluidos[]` | n/a | ✅ |
| `niveis{nome: nv}` | ❌ | ✅ |
| `capMundo` (Set) | ✅ ao avançar | ❌ |
| `fusao` | ✅ ao avançar | ❌ |
| `jogIdx`, `slotAtivo` | n/a | ❌ (só sessão) |

`salvarProgresso()` é chamado: ao subir de nível, ao avançar de mundo, ao apagar progresso. `carregarProgresso()` roda no boot.

## Fluxo da batalha (state machine)

```
faseBat: 'escolha' → 'animando' → 'escolha' (loop) → 'fim'
```

1. **`escolha`** — botões de golpe ativos, jogador escolhe
2. **`animando`** — golpe selecionado, animações de ataque/dano, depois `turnoInimigo()`, depois volta para `escolha`
3. **`fim`** — vida ≤ 0 em algum lado; abre área-go

### Vitória (`combIni.vida <= 0`)

```
1. mostrarLog vitória
2. Se nomeJog (não fusão):
   - E.niveis[nomeJog] += 1
   - salvarProgresso()
   - aoSairNivel = proximoMundo
   - mostrarNivelUp(nome, novoNv)  ← popup tela-nivel
3. Se fusão: vai direto para mostrarGameOverVitoria
```

Ao clicar Continuar! no popup de nível → `continuarNivel()` → `proximoMundo()` → próximo mundo (ou entrada, se foi o último).

### Derrota (`combJog.vida <= 0`)

```
1. mostrarLog derrota
2. area-go visível com:
   - "↩ Tentar de novo" → voltarMundo()
   - "🗺️ Mapa" → mostrarMapa()
```

Sem level-up. Estado preservado (capMundo e fusao continuam, niveis continuam).

## Sistema de captura

Mini game canvas 600×380. Áreas:

- **`y ≤ LAGO_Y` (160)**: grama — insetos podem aparecer
- **`y > LAGO_Y`**: lago — peixes podem aparecer; jogador anda 45% mais devagar
- **6 árvores** com colisão circular (raio 28px)

Loop:
- A cada 2200ms gera 1 inseto OU 1 peixe aleatório
- Limite total no campo: 12 criaturas
- Captura ao encostar (distância < 28px)
- Cada espécie nova adicionada a `E.capMundo` (Set por nome)

Controles:
- Teclado: setas (capturadas globalmente via `keydown`/`keyup`)
- Touch: D-pad (`touchstart`/`touchend` mapeia para as mesmas `capTeclas`)

## Sistema de fusão

Combina duas criaturas capturadas no mundo atual:

- Emoji = concatenação dos dois (renderizado como dois emojis lado a lado na batalha)
- Nome = "X + Y"
- Cor = média RGB dos dois
- Golpes = 1 golpe de cada criatura origem (alternando) — total 4 golpes
- Não tem nível, não persiste entre mundos
- Fica disponível como opção extra na tela de seleção (índice `'fusao'`)

## Telas (8)

```
Entrada → Mapa → Mundo ─┬─ Captura
                        ├─ Fusão
                        └─ Seleção → Batalha ─┬─ vitória → Nível → Mundo seguinte
                                              └─ derrota → área-go → Mundo / Mapa
```

Detalhes do fluxo em texto: ver seção "Telas" no `README.md` e nas funções `mostrar*()` em `index.html`.

## Convenções visuais

- Largura base: 600px (todos os painéis)
- Canvas captura: 600×380
- Canvas batalha: 600×256
- Resolução interna do canvas é fixa; em landscape mobile o CSS escala via `height` + `aspect-ratio`
- Cores principais: fundo `#08101e`, painéis `#06101e`, bordas `#1a3a5c`, accent verde `#a8ff8c`, accent azul `#7dcfff`

## O que está hardcoded e pode evoluir

- Stats dos mundos (`MUNDOS[]`) — fácil adicionar mundos novos copiando linha
- Personagens e golpes (`PERS[]`) — adicionar entrada nova já aparece na captura/seleção/grid automaticamente
- Cooldown de geração de criaturas (2200ms), limite no campo (12)
- Bônus por captura (×5) e por nível (×10 vida/mana, +3 dano especial)
