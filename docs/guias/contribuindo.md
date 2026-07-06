# Contribuindo

Guia rápido para trabalhar no ScanPC.

## Fluxo de branches

- `main` — branch estável.
- Branches de trabalho por feature (ex.: `feat-layout`, `feat-quality`).
- Abra um **Pull Request** para a `main`; evite commitar direto.

## Antes de abrir o PR

Rode as verificações locais e garanta que passam:

```bash
# Backend
cd backend
pytest            # testes unitários
ruff check .      # lint

# Frontend
cd frontend
npm run build     # tsc -b && vite build (typecheck + build)
npm run lint      # eslint
```

## Convenções

- **Commits** seguem o padrão *Conventional Commits* (ex.:
  `feat(ai): auto-deteccao de categoria pela IA na captura de itens`).
- **Backend:** respeite as camadas da Clean Architecture — regra de negócio em
  `domain`/`application`, dependências externas só em `infrastructure`. Ver
  [Backend → Camadas](../backend/camadas.md).
- **Frontend:** organize por feature; o que for transversal vai para `shared/`.

## Documentação

Esta documentação vive em `docs/` e é publicada automaticamente no GitHub Pages a cada
push na `main` que altere `docs/**` ou `mkdocs.yml`.

Para pré-visualizar localmente:

```bash
pip install -r docs/requirements.txt
mkdocs serve          # http://127.0.0.1:8000
mkdocs build --strict # valida links e referências
```

## Segredos

Nunca commite `.env`, `.env.local` nem a service account do Firebase — todos estão no
`.gitignore`. Ver [Segurança](../referencia/pendencias.md#seguranca).
