# Backend — Visão geral

Serviço **FastAPI** em Python 3.11+, **stateless** e organizado em
**Clean Architecture**. Expõe uma API HTTP para extração de campos por IA e geração de
PDF, além de servir o schema das categorias padrão.

## Responsabilidades

- Validar o token do Firebase em cada requisição protegida.
- Receber uma foto + schema de categoria e chamar a IA (Gemini) para extrair campos.
- Receber os dados completos de uma operação e renderizar o PDF.
- Fornecer as categorias padrão para o frontend.

O que o backend **não** faz: persistir dados. Não há banco — cada requisição é
autossuficiente.

## Dependências principais

Definidas em [`backend/pyproject.toml`](https://github.com/FCTE-UNB-EPS5/ScanPC/blob/main/backend/pyproject.toml):

| Pacote | Uso |
|--------|-----|
| `fastapi` + `uvicorn[standard]` | framework web + servidor ASGI |
| `pydantic` + `pydantic-settings` | DTOs e configuração via env |
| `google-genai` | cliente do Gemini |
| `weasyprint` + `jinja2` | renderização de PDF a partir de HTML |
| `firebase-admin` | verificação de ID token do Firebase |
| `python-multipart` | upload de formulários/arquivos |

Dev: `pytest`, `pytest-asyncio`, `httpx`, `ruff`.

## Ponto de entrada

`app/main.py` monta a aplicação em `create_app()`:

- Configura CORS a partir de `CORS_ORIGINS`.
- Registra o healthcheck `GET /health`.
- Inclui os routers `ai`, `pdf` e `categories`.

```python
def create_app() -> FastAPI:
    app = FastAPI(title="EPS Backend", version="0.1.0")
    app.add_middleware(CORSMiddleware, allow_origins=settings.cors_origin_list, ...)

    @app.get("/health")
    def health() -> dict[str, str]:
        return {"status": "ok"}

    app.include_router(ai.router)
    app.include_router(pdf.router)
    app.include_router(categories.router)
    return app
```

Consulte também [Camadas](camadas.md), [Endpoints](endpoints.md) e
[Configuração](configuracao.md).
