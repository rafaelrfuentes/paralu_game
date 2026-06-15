# Handoff — Para Lú!

Última sessão: 2026-06-15

## Estado atual

- **Versão:** v4 (arquivo `para_lu_v4.html` + `index.html` para Pages)
- **URL pública:** https://rafaelrfuentes.github.io/paralu_game/
- **Repo:** https://github.com/rafaelrfuentes/paralu_game (público)
- **Local:** `~/code/paralu_game/`

## O que foi feito nesta sessão

1. Criado o projeto `paralu_game/` em `~/code/` e subido para GitHub
2. Implementado salvamento de progresso via `localStorage` (mundoIdx, mundos concluídos, níveis)
   - Salva automaticamente ao subir de nível e ao avançar de mundo
   - Flash "💾 SALVO" e botão "Recomeçar" na tela de entrada
3. Corrigido bug: insetos/peixes quase invisíveis no mini game
   - Causa: `fillStyle = rgba(..., 0.12)` do texto do lago vazava pro `fillText` dos emojis
4. Mini game de captura agora mostra ícones das criaturas capturadas + contador "X de 16"
5. GitHub Pages configurado — `index.html` serve a versão atual; `para_lu_vN.html` para WhatsApp

## Convenção de arquivos

- `index.html` — versão mais recente (o que o Pages serve); atualizar a cada sessão
- `para_lu_vN.html` — snapshot de distribuição por WhatsApp; gerar ao final de cada versão estável

## Próximos passos (roadmap)

Opções em ordem de impacto para o Raul:

| O que | Detalhe |
|---|---|
| Direção do sprite | Ash vira para o lado do movimento no mini game (Canvas, já tem desenharAsh) |
| Nome do jogador | Campo de texto na tela de entrada; aparece no log da batalha |
| Tela de vitória final | Animação especial ao completar os 6 mundos |
| Controles touch | Botões na tela para substituir setas do teclado — necessário para celular |

## Stack / restrições

- HTML + CSS + JS vanilla, single file (pode quebrar em múltiplos quando necessário)
- Sem framework, sem build step
- Comentários e variáveis em português
- Funções nomeadas pelo que fazem (`desenharAsh`, `realizarFusao`)
- Código sempre completo, nunca trechos com "..."
