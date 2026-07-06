# ScanPC — App de Apreensão

App web responsivo (mobile-first) que substitui o formulário manual de inventário
de apreensões policiais. A equipe em campo registra os itens apreendidos por
categoria, usa **IA para extrair campos a partir de fotos** e gera o **PDF oficial**
para upload no sistema da delegacia.

!!! info "Projeto acadêmico"
    Desenvolvido na disciplina de **Engenharia de Produto de Software (EPS) — FCTE/UnB**,
    em parceria com um delegado da **PCDF**.

## Equipe e projeto

| Item        | Detalhe                                                              |
| ----------- | -------------------------------------------------------------------- |
| Projeto     | App de Apreensão Policial (APP 2)                                    |
| Disciplina  | Engenharia de Produto de Software                                    |
| Cliente     | Delegado da PCDF                                                     |
| Grupo       | 18                                                                   |
| Repositório | [FCTE-UNB-EPS5/ScanPC](https://github.com/FCTE-UNB-EPS5/ScanPC)      |

**Dupla:**

| Nome                      | Matrícula |
| ------------------------- | --------- |
| João Manoel Barreto Neto  | 211039519 |
| João Victor Costa Andrade | 211061977 |

## Contexto

Hoje a equipe policial preenche à mão um formulário de inventário durante as
apreensões e, depois, sobe o PDF no sistema interno da delegacia. O MVP substitui o
formulário físico por um app web usado no celular em campo, que registra os itens,
usa IA para pré-preencher campos a partir de fotos (com o agente revisando) e gera o
PDF final para download.

## Escopo do MVP

- Cadastrar operação (equipe, localização, suspeito).
- Adicionar itens apreendidos por categoria, com campos específicos por tipo.
- Usar IA para extrair campos a partir de fotos — o agente revisa antes de salvar.
- Gerar o PDF ao encerrar, baixado no próprio dispositivo.
- **Sem banco de dados próprio.** O rascunho persiste localmente (IndexedDB).

## Stack

| Camada | Tecnologia |
|--------|------------|
| Frontend | React 18 + Vite + TypeScript + TailwindCSS (mobile-first) |
| Backend | Python 3.11+ + FastAPI (Clean Architecture, stateless) |
| IA | Gemini 2.5 Flash (via `google-genai`) |
| PDF | WeasyPrint (HTML + CSS → PDF) — ou PDFShift como alternativa |
| Auth | Firebase Authentication (e-mail/senha) |
| Persistência local | IndexedDB (rascunho) + localStorage (categorias custom) |

## Por onde começar

<div class="grid cards" markdown>

- :material-sitemap: **[Arquitetura](arquitetura.md)** — visão geral e fluxo de dados.
- :material-server: **[Backend](backend/visao-geral.md)** — camadas, endpoints e configuração.
- :material-react: **[Frontend](frontend/visao-geral.md)** — estrutura e persistência.
- :material-rocket-launch: **[Como rodar](guias/como-rodar.md)** — Docker e dev local.

</div>
