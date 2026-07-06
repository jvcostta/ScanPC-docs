# Frontend — Visão geral

SPA em **React 18 + Vite + TypeScript**, estilizada com **TailwindCSS** e pensada
**mobile-first** (o app é usado no celular, em campo). Organização **por feature**.

## Responsabilidades

- Autenticar o usuário (Firebase Auth) e proteger as rotas.
- Guiar o fluxo: cadastro da operação → inventário de itens → encerramento/PDF.
- Chamar a IA para pré-preencher campos a partir de fotos e exibir a confiança.
- Persistir o rascunho localmente (IndexedDB) e as categorias custom (localStorage).
- Disparar o download do PDF gerado pelo backend.

## Dependências principais

Definidas em [`frontend/package.json`](https://github.com/FCTE-UNB-EPS5/ScanPC/blob/main/frontend/package.json):

| Pacote | Uso |
|--------|-----|
| `react` / `react-dom` 18 | UI |
| `react-router-dom` 6 | roteamento |
| `firebase` | autenticação (e-mail/senha) |
| `@tanstack/react-query` | estado de servidor |
| `idb` | wrapper de IndexedDB (rascunho) |
| `react-hook-form` + `zod` | formulários e validação |
| `lucide-react`, `clsx` | ícones e classes condicionais |

Build/dev: `vite` 5, `tailwindcss` 3, `typescript` 5, `eslint`.

## Scripts

```bash
npm run dev      # servidor de desenvolvimento (Vite) — http://localhost:5173
npm run build    # tsc -b && vite build
npm run preview  # pré-visualiza o build de produção
npm run lint     # eslint
```

Requer `.env.local` (copie de `.env.example`) com `VITE_API_URL` e as variáveis
`VITE_FIREBASE_*`. Ver [Como rodar](../guias/como-rodar.md).

Continue em [Estrutura](estrutura.md) e [Estado e persistência](estado-persistencia.md).
