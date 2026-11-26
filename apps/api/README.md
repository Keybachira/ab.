# apps/api: Django + DRF

Backend do ab-armazem. Ver `../../docs/PLANO.md` para modelos, endpoints e fases (B0 a B6).

## Estrutura planeada

```
apps/api/
├── config/             settings (base, dev, prod), urls, wsgi
├── apps/
│   ├── accounts/       utilizador e login JWT
│   ├── projects/       Project, Stack, Link, Note
│   ├── vault/          Secret (encriptado)
│   ├── activity/       feed de novidades
│   └── integrations/   GitHub e Bachira (fase 2)
├── manage.py
├── requirements.txt
└── .env.example
```

## Arranque (B0)

```bash
cd apps/api
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
cp .env.example .env             # preenche DATABASE_URL, DATABASE_URL_DIRECT e SECRETS_KEY
django-admin startproject config .
```

Depois de instalar, fixa as versões com `pip freeze > requirements.lock.txt`.

## Neon

- `DATABASE_URL`: endereço **pooled** (host com `-pooler`), usado pela app.
- `DATABASE_URL_DIRECT`: endereço **directo**, usado só para `python manage.py migrate`.
- Na app: `conn_max_age=0` e `DISABLE_SERVER_SIDE_CURSORS=True`; `sslmode=require`.
- Branch `dev` para desenvolver e `main` para produção.

## Regras do cofre

- Os segredos guardam-se encriptados com Fernet (`SECRETS_KEY`).
- O valor nunca volta nas listas; só o endpoint `reveal` o devolve, com limite de pedidos e registo na actividade.
- Nunca fazer commit de `.env`.
