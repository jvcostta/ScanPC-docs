# Pendências e roadmap

Estado do MVP: as funcionalidades centrais estão entregues (auth, operação,
inventário, IA, PDF). O que falta depende principalmente de insumos do cliente.

## Bloqueadores do cliente

1. **PDF-modelo oficial do delegado** — o template atual
   (`operation.html.j2`) é placeholder. Quando o cliente enviar o modelo, ajustar o
   template para casar o layout. Ver [Geração de PDF](../funcionalidades/pdf.md).
2. **Campos exatos por categoria** — sairão do formulário oficial de apreensão. Hoje
   os campos foram inferidos da descrição do cliente. Ver
   [Categorias](../funcionalidades/categorias.md).
3. **Design final** — pendente do designer (Mateus).
4. **Reaproveitamento de código do semestre anterior** — confirmar com o professor se
   é possível aproveitar algo de um app parecido feito por outra turma.

## Evoluções possíveis

- **Auth de verdade em produção:** ligar `FIREBASE_CREDENTIALS_PATH` +
  `AUTH_DISABLED=false` para validar tokens no backend.
- **Prompt mais robusto:** few-shot com exemplos, ou `response_schema` estruturado do
  Gemini em vez de JSON livre.
- **Compressão de imagem no front** antes de enviar à IA (reduz custo/latência).
- **Múltiplas fotos por item** com agregação das extrações.
- **Exportar/importar rascunho** para backup manual.

## Segurança

!!! danger "Rotacionar a chave Gemini"
    Uma `GEMINI_API_KEY` foi exposta em conversa durante o desenvolvimento —
    **rotacione-a** antes de publicar em repositório compartilhado.

- `.env` (backend) e `.env.local` (frontend) estão no `.gitignore`.
- A chave do Gemini e a service account do Firebase **nunca** devem ir para o Git.
- Em produção, mantenha `AUTH_DISABLED=false`.
