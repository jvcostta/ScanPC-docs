# Backend — Camadas

O backend segue **Clean Architecture**. A dependência aponta sempre para dentro:
`interfaces` → `application` → `domain`, com `infrastructure` implementando os ports
que a camada `application` define.

```
backend/app/
├── domain/           # entidades + categorias default (regra de negócio pura)
├── application/
│   ├── ports/        # interfaces: VisionAI, PDFRenderer
│   └── use_cases/    # ExtractItemFromImage, GenerateOperationPDF
├── infrastructure/   # adapters concretos
│   ├── gemini/       # GeminiVisionAI
│   ├── pdf/          # WeasyprintRenderer + template Jinja2
│   └── auth/         # FirebaseVerifier
└── interfaces/http/  # rotas FastAPI, DTOs, mappers, DI (deps.py)
```

## `domain` — regra de negócio pura

Sem dependências externas. Contém as entidades (`Category`, `FieldSchema`,
`Operation`, etc.), os erros de domínio (`errors.py` — `CategoryNotFound`,
`AIUnavailable`, `AIExtractionFailed`) e as **categorias padrão**
(`categories_default.py`). Ver [Funcionalidades → Categorias](../funcionalidades/categorias.md).

## `application` — casos de uso e ports

Os casos de uso orquestram a regra de negócio dependendo **apenas de interfaces
abstratas** (ports), nunca de implementações concretas.

- **Ports** (`application/ports/`): `VisionAI` (extração por IA) e `PDFRenderer`
  (renderização de PDF).
- **Use cases** (`application/use_cases/`):
    - `ExtractItemFromImage` — recebe imagem(ns) + schema, chama o `VisionAI` e
      normaliza a resposta.
    - `GenerateOperationPDF` — recebe a operação + categorias e chama o `PDFRenderer`.

## `infrastructure` — adapters concretos

Implementações reais dos ports:

- `gemini/` — `GeminiVisionAI`, que fala com a API do Gemini.
- `pdf/` — `WeasyprintRenderer` + o template Jinja2 `templates/operation.html.j2`.
- `auth/` — `FirebaseVerifier`, que valida o Bearer ID token.

Trocar de provedor de IA ou de biblioteca de PDF significa escrever um novo adapter e
religá-lo na injeção de dependência — **sem tocar nos use cases**.

## `interfaces/http` — entrada HTTP

Rotas FastAPI, DTOs Pydantic, mappers (DTO ↔ entidade de domínio) e a **injeção de
dependência** em `deps.py`, que constrói e conecta os adapters concretos aos use
cases via `Depends()`.
