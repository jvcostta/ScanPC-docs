# Backend — Endpoints da API

Todas as rotas exigem um **Bearer ID token do Firebase** no header
`Authorization`, **exceto** `GET /health`. Em dev, a autenticação pode ser desligada
com `AUTH_DISABLED=true` (ver [Configuração](configuracao.md)).

| Método | Rota | Descrição | Auth |
|--------|------|-----------|:----:|
| `GET` | `/health` | Healthcheck | ❌ |
| `GET` | `/categories/defaults` | Lista as categorias padrão + schema de campos | ✅ |
| `POST` | `/ai/extract-item` | Extrai campos de foto(s) via Gemini | ✅ |
| `POST` | `/operations/pdf` | Gera o PDF da operação | ✅ |

Autenticação: `Authorization: Bearer <ID_TOKEN>`, validado por `firebase-admin`.

---

## `GET /health`

Healthcheck simples.

```json
{ "status": "ok" }
```

## `GET /categories/defaults`

Retorna as categorias padrão com o schema de cada campo (`key`, `label`, `type`,
`ai_extractable`, `options`). Consumido pelo frontend para montar os formulários.

## `POST /ai/extract-item`

Recebe uma ou mais imagens (base64) + o `category_id` e eventuais categorias custom.
Chama o Gemini e retorna os campos extraídos com **confiança por campo**, além de uma
**detecção automática de categoria**.

**Request (resumo):**

```json
{
  "category_id": "eletronico",
  "images": [{ "base64": "<...>", "mime": "image/jpeg" }],
  "custom_categories": []
}
```

Também aceita `image_base64` (imagem única) por compatibilidade. Pelo menos uma
imagem é obrigatória, senão retorna `400`.

**Response (resumo):**

```json
{
  "fields": { "marca": "Samsung", "modelo": "Galaxy S21" },
  "confidence": { "marca": 0.92, "modelo": 0.61 },
  "detected_category_id": "eletronico",
  "category_confidence": 0.88,
  "category_alternatives": [{ "id": "documento", "confidence": 0.10 }]
}
```

**Erros:**

| Status | Quando |
|--------|--------|
| `400` | nenhuma imagem enviada ou base64 inválido |
| `404` | `category_id` desconhecido (`CategoryNotFound`) |
| `502` | falha na extração da IA (`AIExtractionFailed`) |
| `503` | IA sobrecarregada (`AIUnavailable`) — envia `Retry-After: 5` |

Detalhes do fluxo em [Funcionalidades → Extração por IA](../funcionalidades/ia-extracao.md).

## `POST /operations/pdf`

Recebe os dados completos da operação (metadados, equipe, itens, categorias custom) e
retorna o PDF.

- **Response:** `application/pdf` com
  `Content-Disposition: attachment; filename="<nome_da_operacao>.pdf"`.
- O frontend dispara o download e oferece limpar o rascunho.

Detalhes em [Funcionalidades → Geração de PDF](../funcionalidades/pdf.md).
