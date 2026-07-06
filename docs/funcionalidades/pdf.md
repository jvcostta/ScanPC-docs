# Geração de PDF

O PDF é o **artefato final** do app — o documento oficial que o agente sobe no sistema
da delegacia. É gerado pelo backend a partir dos dados completos da operação.

## Fluxo

1. Ao encerrar a operação, o frontend chama `POST /operations/pdf` com os dados
   completos (metadados, localização, suspeito, equipe, itens e categorias custom).
2. O backend renderiza um **template Jinja2** (`operation.html.j2`) em HTML e converte
   para PDF.
3. A resposta é `application/pdf` com
   `Content-Disposition: attachment; filename="<nome_da_operacao>.pdf"`.
4. O frontend dispara o download e oferece limpar o rascunho.

## Motor de renderização

- **Padrão: WeasyPrint** (HTML + CSS → PDF). Depende de libs de sistema (Pango, Cairo)
  — ver [Como rodar](../guias/como-rodar.md).
- **Alternativa: PDFShift.** Se `PDFSHIFT_API_KEY` estiver definido, a geração usa a
  API do PDFShift, dispensando as dependências de sistema do WeasyPrint. Ver
  [Configuração](../backend/configuracao.md).

Ambos ficam por trás do port `PDFRenderer` — o use case `GenerateOperationPDF` não
sabe qual está em uso.

## Conteúdo do documento

O template inclui: cabeçalho, metadados da operação, localização, suspeito, equipe em
tabela, itens **agrupados por categoria** (com os campos corretos de cada uma) e espaço
para assinatura.

!!! warning "Template é placeholder"
    O layout atual é um **placeholder** (marcado com aviso no topo do documento) até o
    delegado enviar o **PDF-modelo oficial**. Quando chegar, ajustar
    [`backend/app/infrastructure/pdf/templates/operation.html.j2`](https://github.com/FCTE-UNB-EPS5/ScanPC/blob/main/backend/app/infrastructure/pdf/templates/operation.html.j2)
    para casar o layout. Ver [Pendências](../referencia/pendencias.md).
