# Frontend — Estrutura

Organização **por feature**: cada área de negócio agrupa suas páginas, componentes,
hooks e tipos. O que é transversal fica em `shared/`.

```
frontend/src/
├── app/              # router + AppShell (header, logout)
├── features/
│   ├── auth/         # login/cadastro Firebase, RequireAuth (rota protegida)
│   ├── operations/   # fluxo principal: lista, formulário, inventário
│   │   ├── components/   # ex.: NewCategoryDialog, ConfidenceBadge
│   │   ├── hooks/        # ex.: useDraftOperation
│   │   └── ...           # páginas e tipos da operação
│   └── categories/   # categorias default + custom (useCustomCategories)
└── shared/
    ├── api/          # client.ts — apiFetch / apiJson / apiBlob (wrapper de fetch)
    └── storage/      # indexeddb.ts — persistência do rascunho
```

## `app/`

Define o roteamento (`react-router-dom`) e o **AppShell** (cabeçalho com logout). A
rota raiz `/` é protegida por `RequireAuth` e redireciona para `/login` quando não há
sessão.

## `features/auth/`

Login e cadastro via **Firebase Auth** (e-mail/senha), com toggle entrar/cadastrar na
mesma tela e validação de senha (≥ 6 caracteres). Erros do Firebase são traduzidos
para mensagens em português. `RequireAuth.tsx` guarda as rotas autenticadas.

## `features/operations/`

O coração do app: cadastro dos metadados da operação (nome, chefe, localização,
suspeito, equipe), o inventário de itens por categoria e o encerramento que dispara a
geração do PDF. Inclui a UI de extração por IA (badges de confiança por campo).

## `features/categories/`

Gerencia as categorias — as **padrão** (vindas do backend) e as **custom** criadas
pelo usuário. Ver [Funcionalidades → Categorias](../funcionalidades/categorias.md).

## `shared/`

- `api/client.ts` — helpers `apiFetch` / `apiJson` / `apiBlob` que embrulham o `fetch`,
  anexam o token e apontam para `VITE_API_URL`.
- `storage/indexeddb.ts` — wrapper do IndexedDB para persistir o rascunho.
