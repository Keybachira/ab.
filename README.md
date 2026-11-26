# ab-armazem

Cofre central dos meus projectos, ao lado do GitHub: fichas, links, notas, ficheiros e segredos encriptados de cada projecto, com ligação ao Bachira HUB (analytics).

- **Frontend:** Next.js (App Router, TypeScript, Tailwind)
- **Backend:** Django + Django REST Framework
- **Base de dados:** Neon (Postgres). Sem Docker.
- **Design:** inspirado em carterogunsola.com. Escuro por defeito, grão, pílulas de estado ao vivo, roleta de projectos.

Estado actual: planeamento concluído e protótipo de design pronto. Ainda não há código de aplicação.

## Estrutura

```
ab-armazem/
├── README.md
├── .gitignore
├── docs/
│   ├── PLANO.md            plano de backend e frontend, fases e marcos
│   └── DESIGN-SYSTEM.md    cores, tipos, textura, cartões, movimento
├── design/
│   ├── index.html          protótipo da página inicial (roleta)
│   └── tokens.css          variáveis do design system
└── apps/
    ├── api/                Django + DRF (a criar, ver apps/api/README.md)
    │   ├── README.md
    │   ├── requirements.txt
    │   └── .env.example
    └── web/                Next.js (a criar, ver apps/web/README.md)
        ├── README.md
        └── .env.example
```

## Ver o protótipo

Abre `design/index.html` no browser. É um único ficheiro, sem build. Precisa de internet só para carregar as fontes (Geist e Geist Mono).

- O scroll, as setas ↑↓ ou o arrastar no telemóvel rodam a roleta. A página não faz scroll.
- `↵` ou clicar no cartão central abre o detalhe (a página `projecto.html` ainda não existe).
- `⌘K` ou `Ctrl+K` abre a paleta de comandos.
- Edita `OWNER` e `MOCK` no topo do script para mudar o dono e os projectos.
- Quando o Django estiver pronto, preenche `API_URL` e a roleta lê a API.

## Pendente

1. Connection strings do Neon (directa e pooled) para `apps/api/.env`.
2. Nome do dono, para o painel da direita.
3. Decidir: monorepo (recomendado) ou dois repositórios.
4. Decidir: npm ou pnpm.

## Próximo passo

Marco 1: B0 a B2 no backend e F0 a F2 no frontend. Ver `docs/PLANO.md`.
