# Frontend — Estado e persistência

Como não há banco de dados, o **estado durável mora no dispositivo**. São três
camadas de estado no frontend:

## IndexedDB — rascunho da operação

- Banco `eps`, store `operations`.
- O rascunho da operação em andamento é persistido automaticamente (hook
  `useDraftOperation`), então fechar/reabrir o navegador **retoma o trabalho** em campo.
- Ao encerrar e baixar o PDF, o app oferece **limpar o rascunho**.
- Acesso encapsulado em `shared/storage/indexeddb.ts` (usa a lib `idb`).

## localStorage — categorias custom

- As categorias criadas pelo usuário (ID + rótulo + lista de campos) ficam salvas no
  `localStorage` do dispositivo (`useCustomCategories`).
- São enviadas ao backend nas requisições de IA e de PDF, junto com as padrão.

## React Query — estado de servidor

- `@tanstack/react-query` gerencia o estado vindo do backend (ex.: categorias padrão).
- Uso é **mínimo** no MVP atual, dado que o backend é stateless e a maior parte do
  estado é local.

!!! note "Resumo"
    | Camada | O que guarda | Escopo |
    |--------|--------------|--------|
    | IndexedDB | rascunho da operação | por dispositivo, persistente |
    | localStorage | categorias custom | por dispositivo, persistente |
    | React Query | dados do servidor (cache) | em memória, por sessão |
