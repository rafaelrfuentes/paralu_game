# Para Lú!

Jogo de batalha de insetos e peixes em HTML/CSS/JavaScript vanilla, single-file, sem dependências.

---

## Sobre o projeto

**Para Lú!** é um jogo desenvolvido incrementalmente por **Raul (6 anos)** com a ajuda do pai, usando o Claude como assistente de programação. O Raul dita o que quer; o pai transcreve, refina e ajuda a transformar as ideias em código.

**Objetivo pedagógico:** mais importante que o jogo pronto é o Raul entender, ao longo do tempo, que jogos são feitos de instruções escritas que o computador executa. Cada nova versão é uma oportunidade de ele observar uma ideia virar código, ver o bug acontecer, entender a causa e ver o conserto.

**Tom:**
- Interface em português do Brasil
- Linguagem acessível para criança de 6 anos
- Nomes científicos reais aparecem em destaque (aprendizado embutido sobre fauna)
- Inspiração visual: Pokémon GameBoy

---

## Estrutura do jogo

Organização hierárquica:

```
Dimensões → Mundos → Fases (módulos sequenciais)
```

- **Dimensão** = conjunto temático de mundos (ex: Floresta Verde, Lago Profundo)
- **Mundo** = uma fase completa, contém três módulos jogáveis
- Cada mundo é mais difícil que o anterior

Dentro de cada mundo, o jogador tem três ações disponíveis:

| Módulo | Cor | O que faz |
|---|---|---|
| Capturar | Verde | Mini game de captura no campo + lago |
| Fusão | Roxo | Combina duas criaturas capturadas |
| Batalha | Vermelho | Escolhe personagem e luta contra inimigo |

Ao vencer a batalha, o jogador avança para o próximo mundo (e o personagem usado sobe de nível).

---

## Personagens (21 no total)

### Insetos (15)

| Emoji | Nome popular | Nome científico |
|---|---|---|
| 🦋 | Borboleta-monarca | *Danaus plexippus* |
| 🐝 | Abelha-europeia | *Apis mellifera* |
| 🐞 | Joaninha | *Coccinella septempunctata* |
| 🦗 | Gafanhoto-verde | *Tettigonia viridissima* |
| 🪲 | Besouro-hércules | *Dynastes hercules* |
| 🐛 | Lagarta-da-couve | *Pieris brassicae* |
| 🦟 | Mosquito-da-dengue | *Aedes aegypti* |
| 🪰 | Mosca-doméstica | *Musca domestica* |
| 🐜 | Formiga-saúva | *Atta sexdens* |
| 🪳 | Barata-americana | *Periplaneta americana* |
| 🔵 | Voador Azul | *Fictícius azulensis* |
| 🕷️ | Aranha-de-jardim | *Lycosa erythrognatha* |
| 🦂 | Escorpião-amarelo | *Tityus serrulatus* |
| 💡 | Vaga-lume | *Lampyris noctiluca* |
| 🐌 | Caracol-de-jardim | *Cornu aspersum* |

### Peixes (6)

| Emoji | Nome popular | Nome científico |
|---|---|---|
| 🐠 | Peixe-palhaço | *Amphiprion ocellaris* |
| 🦈 | Tubarão-branco | *Carcharodon carcharias* |
| 🐡 | Baiacu | *Tetraodon nigroviridis* |
| 🐟 | Peixe-espada | *Xiphias gladius* |
| 🪼 | Água-viva | *Physalia physalis* |
| 🐟 | Piranha | *Pygocentrus nattereri* |

### Voador Azul — personagem especial

Criação fictícia do Raul. Aparece como uma centopeia/dragão azul com asas translúcidas e presas laranjas. Diferente dos outros, é desenhado pixel a pixel via Canvas API (não usa emoji). Sempre disponível, tratado como personagem "lendário".

### Estrutura de cada personagem

Cada personagem tem **4 golpes** balanceados por dano e mana:

| Slot | Dano aprox | Mana aprox | Papel |
|---|---|---|---|
| 1 | 9–12 | 7–10 | Básico, barato |
| 2 | 13–19 | 12–17 | Médio |
| 3 | 19–25 | 18–25 | Forte |
| 4 (especial) | 28–37 | 30–38 | Definitivo, ganha bônus por nível |

Cada golpe tem nome temático, ícone e mensagem narrativa (ex: "A Borboleta liberou seu veneno!").

---

## Mundos (6 ao total, expansível)

| # | Dimensão | Nome | Vida inimigo | Mana inimigo | Golpes IA | Dificuldade |
|---|---|---|---|---|---|---|
| 1 | Floresta Verde | Clareira | 100 | 100 | 2 | ⭐ |
| 2 | Floresta Verde | Floresta Densa | 135 | 135 | 3 | ⭐⭐ |
| 3 | Floresta Verde | Pântano | 170 | 170 | 4 | ⭐⭐⭐ |
| 4 | Lago Profundo | Margem do Lago | 210 | 210 | 4 | ⭐⭐⭐⭐ |
| 5 | Lago Profundo | Águas Fundas | 260 | 260 | 4 | ⭐⭐⭐⭐⭐ |
| 6 | Lago Profundo | Fundo do Abismo | 320 | 320 | 4 | ⭐⭐⭐⭐⭐⭐ |

**Progressão de dificuldade:** o inimigo escala em vida/mana e, nos mundos iniciais, no número de golpes diferentes que pode usar. No mapa, mundos ficam bloqueados até o jogador concluir o anterior.

---

## Mecânicas detalhadas

### Mini game de captura

- Canvas 600×380px
- Campo dividido em duas zonas: **grama** (parte verde, topo) e **lago** (parte azul, fundo)
- **Insetos** aparecem na grama, **peixes** aparecem no lago
- 6 árvores 🌳 funcionam como obstáculos sólidos (colisão circular)
- Personagem (sprite pixel art do Ash) anda com as setas do teclado
- Velocidade reduzida em ~45% quando está dentro do lago
- Insetos desviam das árvores ao se mover
- Capturas acontecem por colisão: encostar na criatura captura
- Novas criaturas surgem automaticamente a cada ~2 segundos
- Capturas ficam salvas **apenas no mundo atual** (resetam ao avançar)

### Bônus de captura

Cada espécie diferente capturada no mundo atual concede:
- **+5 de vida máxima**
- **+5 de mana máxima**

Aplicado a qualquer personagem escolhido para a batalha daquele mundo. Incentiva a explorar o mini game antes de lutar.

### Máquina de Fusão

Módulo intermediário entre captura e batalha.

- Jogador seleciona 2 criaturas diferentes capturadas no mundo atual
- Resultado:
  - **Visual:** dois emojis lado a lado (ex: 🦋🐟)
  - **Nome:** concatenação dos dois (ex: "Borboleta-Tubarão")
  - **Vida:** 120 (fixo)
  - **Mana:** 120 (fixo)
  - **Golpes:** 2 golpes do primeiro + 2 do segundo personagem (4 no total)
- A fusão fica disponível como opção extra na tela de seleção
- **Dura apenas o mundo atual** — no próximo mundo, precisa criar nova fusão
- Fusões **não sobem de nível** (são temporárias)

### Sistema de nível

- Cada personagem (exceto fusões) começa no **nível 1**
- Cada **vitória** sobe o nível em +1
- Nível é **independente por personagem** (cada um sobe separadamente)
- Bônus por nível:
  - **+10 vida máxima** por nível
  - **+10 mana máxima** por nível
  - **+3 dano** no golpe especial (último) por nível
- Tela de "Subiu de Nível!" aparece animada entre a vitória e o próximo mundo
- Nível aparece na tela de seleção e na batalha

### Batalha por turnos

- Estilo Pokémon GameBoy
- Layout: inimigo no topo, jogador embaixo, campo de batalha no meio
- Cada um tem **barra de vida** (verde, fica vermelha abaixo de 25%) e **barra de mana** (azul)
- Jogador escolhe entre os 4 golpes (botões na parte inferior)
- Quando sem mana, aparece botão **Descansar** (+20 mana, mas passa o turno)
- Inimigo é controlado por IA de Combate: possui 70% de chance de priorizar o golpe disponível que causa maior dano (dentre os que ele consegue pagar com sua mana atual), e 30% de chance de escolher qualquer outro aleatório.
- Animações (Game Juice):
  - **Ataques Físicos vs Projétil**: Golpes com ícones de impacto físico (como `⚔️`, `🦷`, `💪`) fazem o atacante deslizar fisicamente até o defensor. Golpes de energia, veneno ou som (como `⚡`, `🧪`, `🔊`) disparam o ícone do golpe como projétil voador no Canvas.
  - **Screen Shake (Tremor de Tela)**: Tremores no cenário proporcional ao dano causado (super tremor no Golpe Especial).
  - **Partículas de Dano**: Explosão de partículas coloridas saindo do alvo atingido, correspondente à paleta de cores da criatura atacante.
  - **Piscar de Dano**: O alvo pisca intermitentemente após receber o impacto.
  - **Visual de Nocaute**: Ao ter sua vida reduzida a 0, o sprite da criatura desmaia (afunda 18px na plataforma, perde a sombra projetada e reduz a opacidade para 35%).

### Combinação de bônus

Ao iniciar uma batalha com personagem comum:

```
Vida final  = Vida base (100) + (capturas × 5) + ((nível − 1) × 10)
Mana final  = Mana base (100) + (capturas × 5) + ((nível − 1) × 10)
```

Com 16 capturas e nível 5 → Vida = 100 + 80 + 40 = 220 antes da luta começar.

---

## Telas do jogo

```
ENTRADA
   ↓
MAPA (visão geral das dimensões e mundos)
   ↓
MUNDO (3 ações disponíveis)
   ├→ CAPTURA → volta pro MUNDO
   ├→ FUSÃO → volta pro MUNDO
   └→ SELEÇÃO → BATALHA
                    ├→ vitória → NÍVEL → próximo MUNDO
                    └→ derrota → MUNDO (tentar de novo)
```

---

## Stack técnica

**Restrições explícitas:**

- HTML + CSS + JavaScript **vanilla** em arquivo único `.html`
- Sem React, sem frameworks, sem bibliotecas externas
- Canvas API nativa para desenhos
- Comentários em português, variáveis com nomes em português
- Código sempre completo e funcional, nunca trechos com "..."

**Por quê:**

- Roda abrindo o `.html` em qualquer navegador, sem build step
- O Raul vê o código fonte completo, sem caixas-pretas
- Facilita distribuição: WhatsApp, e-mail, Google Drive
- Compatível com WebView Android (para futuro empacotamento)

**Estilo de código:**
- Funções pequenas, nomeadas pelo que fazem (`fazerDinossauroPular`, não `handleJump`)
- Clareza > elegância
- Sem otimização prematura nem abstração desnecessária

---

## Histórico de versões

| Versão | Arquivo | Features principais |
|---|---|---|
| v1 | `cacador_de_insetos.html` | Mini game de captura, 6 insetos, sprite Ash pixel art |
| v2 | `para_lu.html` | Tela VS, batalha Voador Azul × Abelha, sprite em canvas |
| v2.5 | `para_lu_v2.html` | Captura + batalha integrados, bônus de captura, golpe especial |
| v3 | `para_lu_v3.html` | 11 insetos, 4 golpes cada, tela de seleção, inimigo aleatório |
| **v4** | `para_lu_v4.html` | 16 personagens (+5 peixes), 6 mundos em 2 dimensões, níveis, lago + árvores no mini game, Máquina de Fusão |

---

## Roadmap

### Próximas ideias possíveis (não implementadas)

- **Mais dimensões:** terceira/quarta dimensão (ex: Céu, Caverna) com novos tipos
- **Fusões permanentes:** salvar fusões entre mundos como criaturas extras
- **Direção do sprite:** virar o Ash para o lado do movimento
- **Nome do jogador:** campo de texto na tela de entrada
- **Tela de vitória final:** animação especial ao completar todos os mundos
- **Modo desafio:** jogador escolhe contra qual inimigo lutar
- **Salvamento de progresso:** localStorage para não perder níveis ao fechar

### Ambição de longo prazo

- **Adaptação para tablet/touch:** botões na tela para substituir setas do teclado
- **Publicação no Google Play:** empacotamento via WebView/Android Studio, distribuição a preço baixo como projeto motivacional

---

## Decisões de design tomadas no caminho

| Decisão | Razão |
|---|---|
| Fusão temporária (1 mundo) | Mantém o ciclo "capturar → fundir → lutar" relevante a cada mundo |
| Capturas resetam por mundo | Mesmo motivo: incentiva engajamento com cada fase |
| Nomes científicos em destaque | Aprendizado embutido sobre fauna real |
| Voador Azul desenhado em canvas | Personagem fictício do Raul merece tratamento único |
| Inimigo escala em golpes, não só stats | Dificuldade não é só "números maiores", é também variedade de ataques |
| Sprite Ash 2px por quadradinho | Pixel art reconhecível sem ser custoso de desenhar |

---

## Como rodar

1. Abrir `para_lu_v4.html` em qualquer navegador moderno (Chrome, Firefox, Safari, Edge)
2. Não precisa servidor, não precisa build
3. Setas do teclado para mover no mini game; cliques nos botões para batalhar
4. Funciona offline após carregado

---

*Documentação gerada em junho de 2026 — Para Lú! v4.0*
