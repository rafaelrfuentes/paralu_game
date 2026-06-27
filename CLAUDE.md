# CLAUDE.md — paralu_game

Doutrina específica deste projeto. Lê junto com o `~/.claude/CLAUDE.md` global.

## O que é

Jogo single-page de batalha estilo Pokémon GameBoy, feito com o filho (6 anos). 20 personagens (15 insetos + 5 peixes), 6 mundos em 2 dimensões. Roda no celular e desktop.

- Live: https://rafaelrfuentes.github.io/paralu_game/
- Repo: https://github.com/rafaelrfuentes/paralu_game

## Stack e restrições

- HTML + CSS + JS vanilla, **arquivo único** (`index.html`)
- Sem framework, sem build step, sem dependências, sem package.json
- Canvas 2D para sprites pixel art e animações
- `localStorage` para progresso
- Deploy: `git push` em `main` → GitHub Pages serve `index.html` automaticamente (~1 min)

## Convenção de arquivos

| Arquivo | Função |
|---|---|
| `index.html` | Versão viva — atualizada a cada sessão, é o que o Pages serve |
| `para_lu_vN.html` | Snapshots imutáveis para distribuição via WhatsApp (gerar `cp index.html para_lu_vN.html` ao final de cada versão estável) |
| `README.md` | Visão narrativa do jogo (orientada ao player/pais) |
| `HANDOFF.md` | Estado da última sessão e próximos passos |
| `DESIGN.md` | Referência técnica: fórmulas, tabelas, fluxos |
| `CLAUDE.md` | Este arquivo — doutrina pro agente |

Quebrar `index.html` em múltiplos arquivos é OK quando o jogo crescer, mas hoje a simplicidade vale mais.

## Workflow padrão de uma alteração

1. Editar `index.html` direto
2. Testar abrindo o arquivo no browser (não há servidor, não há build)
3. Para mobile: testar em `https://rafaelrfuentes.github.io/paralu_game/` após push
4. `git commit` + `git push` → live em ~1 min
5. Em versão estável marcante: `cp index.html para_lu_vN.html` e commitar junto

## Armadilhas conhecidas (cometi todas — não repetir)

### 1. Show/hide de telas via JS inline ≠ CSS `!important`

O JS usa `el.style.display = 'flex'` e `'none'` para mostrar/esconder cada `.tela`. Se eu coloco `display: grid !important` no CSS, vence o `display: none` inline também → tela fica sempre visível, sobrepondo as outras.

**Padrão correto** para sobrescrever display em media query:
```css
#tela-captura[style*="display: flex"] {
  display: grid !important;
  ...
}
```
O seletor de atributo só casa quando o JS marcou como visível.

### 2. `selIdx` vs `E.jogIdx` — não são a mesma variável

- `selIdx` (global): personagem **escolhido** na tela de seleção
- `E.jogIdx`: personagem **da batalha em andamento**, definido APENAS dentro de `iniciarBatalha()`

`confirmarBatalha()` deve checar `selIdx`, nunca `E.jogIdx` (que é `null` na primeira batalha após carregar a página).

### 3. `fillStyle` com alpha vaza para `fillText` de emoji

Padrão tóxico (causou bug visível na captura E na batalha):
```js
ctx.fillStyle = 'rgba(0,0,0,0.18)';   // sombra
ctx.fill();
ctx.fillText(p.emoji, x, y);           // emoji sai 18% transparente!
```

**Sempre** envolver com `save()`/`restore()` quando mexer em `fillStyle` antes de desenhar emoji:
```js
ctx.save();
ctx.fillStyle = 'rgba(0,0,0,0.18)';
ctx.fill();
ctx.restore();
```

Emojis no Canvas são renderizados com tint do `fillStyle` atual — qualquer alpha < 1 deixa o emoji translúcido.

## Convenções de código

- **pt-BR** em variáveis, funções, comentários e logs (`desenharAsh`, `realizarFusao`, `capPers`, `voltarMundo`)
- **Funções nomeadas pelo que fazem**, não pelo tipo (`atualizarUICaptura`, não `updateUI`)
- **Sem abreviações obscuras** — `capTeclas` é OK (capturaTeclas), `pJ` só vale dentro de função pequena
- **Código sempre completo** — nunca `...` ou trechos parciais; o arquivo é entregue rodando
- **Não usar emoji em código** (só nos textos do jogo voltados ao player)

## Estado e persistência (`E` global)

| Chave | Reseta entre mundos? | Persiste em localStorage? |
|---|---|---|
| `mundoIdx` | n/a | ✅ |
| `concluidos[]` | n/a | ✅ |
| `niveis{nome: nv}` | ❌ não | ✅ |
| `capMundo` (Set) | ✅ **reseta** ao avançar | ❌ |
| `fusao` | ✅ **reseta** ao avançar | ❌ |
| `jogIdx`, `slotAtivo` | n/a | ❌ (sessão) |

Salvar via `salvarProgresso()` (chave `paralu_progresso`). Carregar via `carregarProgresso()` no boot.

## Layout responsivo

Tudo é 600px de largura no design base (desktop). Para mobile usar:

- `<meta viewport>` está no `<head>` (essencial — sem ele celular renderiza em modo desktop)
- Media query `@media (orientation: landscape) and (max-height: 500px)` cobre celular deitado
- Tela de captura vira CSS grid (canvas esquerda + D-pad direita) — ver armadilha #1 do seletor
- Canvas da batalha escalado via CSS `height` + `aspect-ratio: 600/256`
- Grid de seleção: 4 colunas → 8 colunas em landscape, com nomes ocultos

Botão `⛶` fullscreen flutuante no canto superior direito (Fullscreen API).

## Sistema de toques (mobile)

- D-pad da captura mapeia `touchstart`/`touchend` para `capTeclas['ArrowUp/Down/Left/Right']` — mesma estrutura do teclado, sem duplicar lógica de movimento
- `touch-action: none` no `.dpad-btn` evita scroll/zoom acidental
- Demais botões usam `onclick` que dispara naturalmente em tap mobile

## O que NÃO fazer

- Não adicionar dependências (framework, build, npm)
- Não quebrar a regra do arquivo único sem motivo forte (cada split aumenta atrito do "abrir e jogar")
- Não comentar o óbvio — comentário só quando o porquê não é evidente do código
- Não usar `--no-verify` em commits
- Não mexer no Drive ou outras cópias — git é canônico (regra global)
