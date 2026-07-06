# Arquitetura

O ScanPC é um sistema **cliente/servidor sem banco de dados**: o frontend guarda o
rascunho localmente e o backend é **stateless** — cada requisição carrega tudo o que
precisa (foto, schema da categoria, dados da operação). O backend só faz duas coisas
pesadas: chamar a IA e renderizar o PDF.

## Visão geral

```mermaid
flowchart LR
    subgraph Cliente["Frontend (React + Vite)"]
        UI[Páginas / componentes]
        IDB[(IndexedDB — rascunho)]
        LS[(localStorage — categorias custom)]
        UI <--> IDB
        UI <--> LS
    end

    subgraph Servidor["Backend (FastAPI — stateless)"]
        API[Rotas HTTP]
        UC[Use cases]
        API --> UC
    end

    Gemini[[Gemini 2.5 Flash]]
    Weasy[[WeasyPrint / PDFShift]]
    FB[[Firebase Auth]]

    UI -- "Bearer token" --> API
    API -. valida token .-> FB
    UC -- "foto + schema" --> Gemini
    UC -- "dados da operação" --> Weasy
    Weasy -- "PDF" --> UI
```

## Fluxo de uso

```mermaid
sequenceDiagram
    actor Agente
    participant Front as Frontend
    participant API as Backend
    participant IA as Gemini
    participant PDF as WeasyPrint

    Agente->>Front: Login (Firebase)
    Agente->>Front: Cadastra operação (equipe, local, suspeito)
    loop Cada item apreendido
        Agente->>Front: Tira foto do item
        Front->>API: POST /ai/extract-item (imagem + schema)
        API->>IA: prompt + imagem base64
        IA-->>API: { fields, confidence }
        API-->>Front: campos + confiança por campo
        Agente->>Front: Revisa / corrige e salva (rascunho no IndexedDB)
    end
    Agente->>Front: Encerra operação
    Front->>API: POST /operations/pdf (dados completos)
    API->>PDF: HTML (Jinja2) → PDF
    PDF-->>Front: application/pdf (download)
```

## Clean Architecture no backend

O backend segue **Clean Architecture** — dependências apontam sempre para dentro, e a
regra de negócio não conhece frameworks nem provedores externos.

```mermaid
flowchart TD
    Interfaces["interfaces/http<br/>rotas, DTOs, mappers, DI"] --> Application
    Infrastructure["infrastructure<br/>Gemini, WeasyPrint, Firebase"] -. implementa .-> Ports
    Application["application<br/>use cases + ports"] --> Domain
    Ports["application/ports<br/>VisionAI, PDFRenderer"]
    Application --> Ports
    Domain["domain<br/>entidades, categorias, erros"]
```

- **`domain`** — regra de negócio pura, zero dependências externas.
- **`application`** — casos de uso que dependem só de **ports** (interfaces abstratas).
- **`infrastructure`** — adapters concretos que implementam os ports (Gemini, WeasyPrint, Firebase).
- **`interfaces/http`** — FastAPI, DTOs Pydantic, mappers e injeção de dependência.

O isolamento via ports (`VisionAI`, `PDFRenderer`) permite **trocar o modelo de IA ou a
biblioteca de PDF sem tocar nos casos de uso**. Detalhes em
[Backend → Camadas](backend/camadas.md).

## Decisões de projeto

- **Sem banco.** O PDF é o artefato final. O rascunho vive no dispositivo (IndexedDB),
  então fechar o navegador não perde o trabalho em campo.
- **Backend stateless.** Facilita deploy e escala; nenhuma sessão do lado servidor.
- **IA como assistente, não autoridade.** Campos sensíveis (IMEI, chassi, nº de série de
  arma) **nunca** são extraídos pela IA — o agente preenche à mão para evitar alucinação.
