# Plano ab-armazem: backend e frontend

Sem Docker. Base de dados: Neon.

## Backend (Django + DRF)

### Dependências
- Django 5 e Django REST Framework
- `djangorestframework-simplejwt` (login JWT)
- `django-cors-headers`, `django-filter`
- `psycopg[binary]`, `dj-database-url`, `python-dotenv`
- `cryptography` (Fernet, para os segredos)
- `drf-spectacular` (esquema OpenAPI, origem dos tipos TypeScript do front)
- `gunicorn` e `whitenoise` (produção)
- `pytest-django` (testes)

### Estrutura
```
apps/api/
├── config/             settings (base, dev, prod), urls, wsgi
├── apps/
│   ├── accounts/       utilizador e login JWT
│   ├── projects/       Project, Stack, Link, Note
│   ├── vault/          Secret (encriptado). Não usar o nome "secrets": colide com a stdlib
│   ├── activity/       feed de novidades
│   └── integrations/   GitHub e Bachira (fase 2)
└── manage.py
```

### Modelos
- `Project`: slug, nome, descrição, estado (Activo, Pausado, Arquivado), ano, dono (FK para utilizador), capa, stacks (M2M), ordem, `is_public`.
- `Stack`: nome e slug.
- `Link`: projecto, tipo, rótulo, URL.
- `Note`: projecto, título, corpo em markdown.
- `Secret`: projecto, rótulo (ex.: `DATABASE_URL`), valor encriptado, data da última revelação.
- `Activity`: tipo, projecto, título, texto, data.
- Fase 2: `Asset` (ficheiros) e `AnalyticsBinding` (ID do projecto no Bachira).

O JSON de `GET /api/projects/` tem o mesmo formato do `MOCK` de `design/index.html`:

```
{ slug, name, desc, status, year, stack: [..], owner: { name }, cover, links: [{ label, url }] }
```

### Endpoints (`/api/`)

| Rota | Função |
|---|---|
| `auth/token/` e `auth/refresh/` | Login e renovação JWT |
| `projects/` | Lista com `?q=`, `?status=`, `?stack=`; cria |
| `projects/{slug}/` | Detalhe, edição, remoção |
| `projects/{slug}/links/` e `notes/` | CRUD |
| `projects/{slug}/secrets/` | Cria e lista (valor sempre mascarado) |
| `secrets/{id}/reveal/` | Devolve o valor e regista na actividade |
| `activity/` e `stacks/` | Feed e tags |
| `health/` | Verificação |
| `schema/` e `docs/` | OpenAPI |

### Segurança e Neon
- Chave Fernet em `SECRETS_KEY` no `.env`. O valor do segredo é só de escrita; a revelação tem limite de pedidos.
- Access token de 15 min, refresh de 7 dias. CORS só para a origem do front.
- Dois endereços do Neon: o directo para `migrate`, o pooled (host com `-pooler`) para a app, com `conn_max_age=0` e `DISABLE_SERVER_SIDE_CURSORS=True`.
- Branch `dev` para desenvolver e `main` para produção.
- Projectos privados por defeito. Só os `is_public` ficam legíveis sem login.

### Fases
1. **B0 Base:** venv, settings, `.env`, ligação ao Neon, `/api/health/`.
2. **B1 Modelos:** migrações, admin, comando `seed_projects` (ab-armazem e Bachira HUB).
3. **B2 Leitura:** lista e detalhe de projectos, filtros, pesquisa e testes.
4. **B3 Auth e escrita:** JWT, permissões, CRUD de projectos, links e notas.
5. **B4 Cofre:** Fernet, reveal com auditoria e limite de pedidos, testes.
6. **B5 Actividade:** feed por signals e esquema OpenAPI estável.
7. **B6 Deploy:** Render ou Railway, migrações pelo endereço directo.
8. **Depois:** sincronização com o GitHub, ficheiros (Cloudflare R2), métricas do Bachira.

## Frontend (Next.js)

### Dependências
- Next.js (App Router), TypeScript, Tailwind
- `next/font` com Geist e Geist Mono
- `next-themes`, `cmdk` (⌘K), Framer Motion
- `openapi-typescript` (tipos vindos do Django)

### Estrutura
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
└── styles/tokens.css         variáveis do design system (ver design/tokens.css)
```

### Decisões
- **Login:** o Next guarda os tokens em cookies httpOnly e fala com o Django por servidor. O JavaScript do browser nunca vê o JWT.
- **Home:** é o `design/index.html` portado. A lógica de scroll, toque e teclado vira o hook `use-wheel`, com loop em `requestAnimationFrame` e refs. O projecto activo fica no URL (`?p=slug`) para o botão "voltar" do detalhe regressar à mesma posição.
- **Detalhe `/projectos/[slug]`:** cabeçalho com dono e estado; secções Links, Notas (markdown), Cofre (mascarado, revelar e copiar), Actividade. Ficheiros e Analytics entram depois. A capa pode animar da roleta para o detalhe com View Transitions.
- **Design:** os tokens passam a variáveis CSS mapeadas no Tailwind. O grão, a hora e o FPS vão no layout.

### Fases
1. **F0 Base:** scaffold, tokens, fontes, grão, tema, HUD, nav.
2. **F1 Roleta com dados de exemplo:** igual ao protótipo.
3. **F2 Ligação à API:** tipos gerados e lista real.
4. **F3 Detalhe:** leitura e transição.
5. **F4 Login:** cookies, middleware, formulários de criar e editar projecto.
6. **F5 Cofre e notas** no detalhe.
7. **F6 ⌘K com pesquisa real**, página de actividade, acessibilidade, deploy no Vercel.

## Ordem de trabalho conjunta

| Marco | Backend | Frontend | Resultado |
|---|---|---|---|
| 1 | B0–B2 | F0–F2 | Roleta com projectos reais do Neon |
| 2 | B3 | F3–F4 | Detalhe e login, criar e editar |
| 3 | B4–B5 | F5 | Cofre, notas e actividade |
| 4 | B6 | F6 | Tudo em produção |

## Por decidir
1. Connection strings do Neon (directa e pooled), branch `dev`.
2. Nome do dono.
3. Monorepo (recomendado) ou dois repositórios.
4. npm ou pnpm.
