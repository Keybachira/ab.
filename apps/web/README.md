# apps/web: Next.js

Frontend do ab-armazem. Ver `../../docs/PLANO.md` (fases F0 a F6) e `../../docs/DESIGN-SYSTEM.md`.

## Estrutura planeada

```
apps/web/
├── app/
│   ├── layout.tsx            fontes, grão, HUD, nav
│   ├── page.tsx              home com a roleta
│   ├── projectos/[slug]/     detalhe
│   ├── login/
│   └── api/auth/             route handlers (cookies httpOnly)
├── components/               hud, nav, theme-toggle, command-palette,
│                             project-wheel, project-info, grain, tag
├── lib/                      api.ts (cliente tipado), use-wheel.ts
└── styles/tokens.css         copiar de design/tokens.css
```

## Arranque (F0)

```bash
cd apps/web
npx create-next-app@latest . --typescript --tailwind --app
npm i next-themes cmdk framer-motion
npm i -D openapi-typescript
cp ../../design/tokens.css styles/tokens.css
cp .env.example .env.local
```

- Fontes: usar `next/font/google` com `Geist` e `Geist_Mono`, ligadas às variáveis `--sans` e `--mono`.
- A lógica da roleta está em `../../design/index.html` (scroll, toque, teclado, roleta circular). Passa para um hook `use-wheel`.
- Tipos da API: `npx openapi-typescript http://localhost:8000/api/schema/ -o lib/api-types.ts`.

## Login

O Next guarda os tokens JWT em cookies httpOnly (route handlers em `app/api/auth/`) e chama o Django a partir do servidor. O JavaScript do browser não vê os tokens.
