# Extração por IA

O ScanPC usa **Gemini 2.5 Flash** para pré-preencher os campos de um item a partir de
foto(s). A IA é uma **assistente**: o agente sempre revisa antes de salvar.

## Como funciona

1. O agente tira uma foto do item.
2. O frontend chama `POST /ai/extract-item` com a(s) imagem(ns) em base64 + o
   `category_id` (e categorias custom, se houver).
3. O backend monta um prompt estruturado com o **schema da categoria** e envia a
   imagem ao Gemini.
4. O Gemini responde `{ fields, confidence }` — valor e **confiança 0.0–1.0 por campo**.
5. O backend normaliza a resposta (descarta chaves fora do schema, clampa a confiança)
   e devolve ao frontend, que exibe os valores com badges de confiança.

## O prompt

O prompt estruturado:

- Pede que a IA identifique **apenas** os campos marcados como `ai_extractable`.
- Força o uso exato das `options` em campos `select` (ex.: tipo do item).
- **Proíbe inventar** serial/IMEI/chassi e pede baixa confiança quando não há certeza.
- Retorna confiança por campo para o frontend sinalizar visualmente.

## Detecção automática de categoria

Além dos campos, a resposta traz uma sugestão de categoria a partir da imagem:

- `detected_category_id` — categoria mais provável.
- `category_confidence` — confiança da detecção.
- `category_alternatives` — outras hipóteses com suas confianças.

Isso permite o app sugerir a categoria certa já na captura do item.

## Confiança no frontend

O componente `ConfidenceBadge` mostra um selo colorido ao lado de cada campo extraído:

| Confiança | Cor | Leitura |
|-----------|-----|---------|
| ≥ 80% | 🟢 verde | provável correto |
| 50–79% | 🟡 amarelo | revisar |
| < 50% | 🔴 vermelho | provavelmente errado |

Editar o campo manualmente **limpa o badge** — o valor passa a ser humano, e a
confiança da IA não vale mais.

## Robustez

- **Fallback de modelos:** se o modelo principal (`GEMINI_MODEL`) falhar, o backend
  tenta os modelos de `GEMINI_FALLBACK_MODELS` em ordem.
- **Erros tratados:** IA sobrecarregada retorna `503` com `Retry-After`; falha de
  extração retorna `502`. Ver [Endpoints](../backend/endpoints.md#post-aiextract-item).
