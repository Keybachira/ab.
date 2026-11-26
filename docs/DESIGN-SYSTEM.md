# Design system ab-armazem (v0.2)

Inspirado na estrutura e nas interacções de carterogunsola.com. Cores, textos e capas são originais. O código CSS está em `design/tokens.css`; o protótipo completo em `design/index.html`.

## Princípios
1. **Grão em tudo:** uma camada de ruído sobre a página dá matéria ao escuro.
2. **O sistema está vivo:** hora, FPS e estado em pílulas reais.
3. **Cartões com peso:** capas inclinadas, placas cortadas nos cantos, montras de projecto.
4. **⌘K e um acento:** tudo pelo teclado; o acento marca só o que está activo ou em foco.

## Cor

| Token | Escuro | Claro |
|---|---|---|
| `--bg` Fundo | `#1E1E1E` | `#F1EFE9` |
| `--surface` Superfície | `#262626` | `#E8E5DD` |
| `--line` Linha | `#3A3A38` | `#CFCAC0` |
| `--muted` Texto suave | `#9A978F` | `#5F5B54` |
| `--fg` Texto | `#ECE9E2` | `#1E1E1E` |
| `--accent` Acento | `#D4FF3F` | `#44580A` |
| `--ink` Texto sobre acento | `#1E1E1E` | `#F1EFE9` |
| `--amber` Pausado | `#E0A84B` | `#9A6A12` |

Estados do projecto: **Activo** (acento), **Pausado** (âmbar), **Arquivado** (texto suave). O estado nunca depende só da cor: leva sempre o rótulo.

## Tipografia
- **Sans:** Geist (400, 500, 600). Alternativas testadas: Inter Tight, Space Grotesk, Manrope.
- **Mono:** Geist Mono (400, 500). Alternativas testadas: JetBrains Mono, IBM Plex Mono, DM Mono.

| Uso | Fonte | Tamanho / linha | Tracking |
|---|---|---|---|
| Display | Sans 600 | 96–148 / 92% | -4,5% |
| Título | Sans 600 | 40–56 / 44–60 | -3,5% |
| Corpo | Sans 400 | 17 / 26 | 0 |
| Etiqueta | Mono 400/500 | 12 / 16, maiúsculas | +8% |
| Dados | Mono 400 | 13 / 20 | +4% |

## Espaço e forma
- Base de 4 px: 4, 8, 12, 16, 24, 32, 48, 64, 96.
- Raios: 0 (padrão), 4 (chips e kbd), 6 (pílulas de HUD), 10 (caixas, toasts), 14 (cartões), 999 (tags).
- Separação por linhas de 1 px. Sem sombras, sem gradientes decorativos.
- Alvos de toque com pelo menos 44 px.

## Textura (grão)
SVG com `feTurbulence` (`baseFrequency` 0.85, 2 oitavas, sem cor) por cima de toda a página, `mix-blend-mode: overlay`, `pointer-events: none`. Opacidade 0.35 no escuro e 0.2 no claro.

## Componentes
- **Pílula de HUD:** `LUA::hora`, `FPS::n`, `PROJ::n`, `API::MOCK|LIVE`. Hora de Luanda e FPS são reais.
- **Cabeçalho:** logo "A B" em círculos, navegação em grupo com borda, botão ⌘K, sino, alternador de tema.
- **Botões:** primário (acento), suave (superfície), contorno, fantasma, desactivado. Foco: 2 px de acento a 3 px de distância.
- **Tag e estado:** pílula redonda em mono maiúsculas; o estado leva um ponto de cor.
- **Cartão de projecto (roleta):** capa 16:10 com raio 14, inclinada em Y (-6°), com etiqueta `N.ºNN` e nome.
- **Painel de informação:** N.º, nome, descrição, dono, factos (estado, stack, ano, links), botão "Abrir projecto".
- **Paleta ⌘K:** projectos, acções e tema; ↑↓ navegam, ↵ escolhe, esc fecha.
- **Campo de segredo:** valor mascarado, selo "Encriptado", botões Revelar e Copiar.
- **Toast de presença, linha de projecto, feed de novidades.**

## Movimento
| Animação | Duração | Notas |
|---|---|---|
| Transição de página | 280 ms, ease-out | Deslize vertical de 8 px com fade |
| Entrada em cascata | 480 ms, 40 ms de passo | Sobe 14 px com fade |
| Hover de linha | 120 ms | Fundo e seta |
| Capa (hover) | 320 ms | Endireita, sobe 6 px, cresce 2% |
| Paleta ⌘K | 180 ms, spring | Escala 0.98 para 1 |
| Pulso de presença | 1,6 s | Ponto a piscar |
| Roleta | por frame | Interpolação de 15% por frame até ao alvo |

Respeitar sempre `prefers-reduced-motion`: sem animação, só estados.

## Página inicial (comportamento)
- A página não faz scroll. O scroll do rato ou do trackpad, as setas, PageUp/PageDown, Home/End e o arrastar no telemóvel rodam a roleta.
- A roleta é circular.
- À direita fica o painel com o dono e os dados básicos do projecto activo, até se abrir o detalhe (`↵`, clique no cartão central ou botão "Abrir projecto").
- Em ecrãs estreitos (até 900 px) a roleta passa para cima e o painel para baixo.

## Placeholders
Valores desconhecidos aparecem como `[ANO]`, `[STACK]`, `[O teu nome]`, `[Capa do projecto]`. Só ab-armazem e Bachira HUB têm dados reais.
