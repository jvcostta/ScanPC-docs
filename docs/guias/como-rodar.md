# Como rodar

Há dois caminhos: **Docker** (recomendado, sobe tudo) ou **dev local** (backend e
frontend separados). Em ambos, os arquivos `.env` precisam existir — use os
`.env.example` como base.

## Com Docker (recomendado)

Pré-requisitos: Docker Desktop. Crie `backend/.env` e `frontend/.env` a partir dos
`.env.example`.

```bash
# Dev — hot-reload no backend (uvicorn --reload) e frontend (Vite HMR)
docker compose -f docker-compose.dev.yml up --build

# Produção local — frontend buildado servido por nginx
docker compose up --build -d
```

- Frontend: <http://localhost:5173>
- Backend: <http://localhost:8000> (healthcheck em `/health`)

Para derrubar: `docker compose -f docker-compose.dev.yml down` (adicione `-v` para
limpar o volume de `node_modules`).

!!! note "Notas"
    - O backend usa `AUTH_DISABLED=true` por padrão em dev — não precisa de
      `firebase-credentials.json`.
    - As variáveis `VITE_*` são *build-args* no compose de produção (o Vite as embute no
      bundle). Em dev, são lidas de `frontend/.env`.

## Dev local (sem Docker)

### Backend

```bash
cd backend
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\activate
pip install -e ".[dev]"
cp .env.example .env      # preencha GEMINI_API_KEY (e Firebase, se usar auth)
uvicorn app.main:app --reload --port 8000
```

!!! warning "WeasyPrint depende de libs de sistema"
    Em Ubuntu/Debian: `sudo apt install libpango-1.0-0 libpangoft2-1.0-0`. Se não
    quiser instalar, defina `PDFSHIFT_API_KEY` para gerar o PDF via API. Ver
    [Configuração](../backend/configuracao.md).

!!! note "Firebase Admin"
    Baixe a service account JSON no console do Firebase e aponte
    `FIREBASE_CREDENTIALS_PATH` para o arquivo. Para dev, dá para usar
    `AUTH_DISABLED=true` temporariamente.

### Frontend

```bash
cd frontend
npm install
cp .env.example .env.local   # preencha VITE_FIREBASE_* e VITE_API_URL
npm run dev
```

Abra <http://localhost:5173>.

## Testes e verificações

```bash
# Backend
cd backend && pytest          # testes unitários
ruff check .                  # lint

# Frontend
cd frontend && npm run build  # tsc -b && vite build (typecheck + build)
npm run lint                  # eslint
```
