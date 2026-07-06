# Backend — Configuração

A configuração é lida de variáveis de ambiente (arquivo `.env`) via
`pydantic-settings` (`app/settings.py`). Use `backend/.env.example` como base.

!!! danger "Segredos nunca vão para o Git"
    `GEMINI_API_KEY` e a service account do Firebase **não** devem ser commitados.
    `.env`, `.env.local` e `*serviceAccount*.json` estão no `.gitignore`. Veja as
    notas em [Pendências → Segurança](../referencia/pendencias.md#seguranca).

## Variáveis

| Variável | Padrão | Descrição |
|----------|--------|-----------|
| `GEMINI_API_KEY` | `""` | Chave de acesso ao Gemini. **Obrigatória** para a IA. |
| `GEMINI_MODEL` | `gemini-2.5-flash` | Modelo principal usado na extração. |
| `GEMINI_FALLBACK_MODELS` | `gemini-flash-latest,gemini-2.5-pro` | Modelos de fallback (lista separada por vírgula) se o principal falhar. |
| `FIREBASE_CREDENTIALS_PATH` | `""` | Caminho para o JSON da service account do Firebase. |
| `FIREBASE_PROJECT_ID` | `""` | ID do projeto Firebase. |
| `CORS_ORIGINS` | `http://localhost:5173` | Origens permitidas (lista separada por vírgula). |
| `AUTH_DISABLED` | `false` | Se `true`, pula a validação do token Firebase (só dev/smoke test). |
| `PDFSHIFT_API_KEY` | `""` | Se definido, gera o PDF via API do **PDFShift** (HTML→PDF), dispensando WeasyPrint/GTK. |

## Autenticação em dev

Para rodar sem configurar o Firebase, defina `AUTH_DISABLED=true`. Nesse modo as rotas
protegidas respondem sem exigir Bearer token — **use apenas localmente**. Em produção,
mantenha `AUTH_DISABLED=false` e aponte `FIREBASE_CREDENTIALS_PATH` para a service account.

## PDF sem WeasyPrint

O WeasyPrint depende de libs de sistema (Pango, Cairo). Em ambientes onde instalá-las é
inconveniente, definir `PDFSHIFT_API_KEY` faz a geração usar a API do PDFShift em vez do
WeasyPrint local. Ver [Geração de PDF](../funcionalidades/pdf.md).
