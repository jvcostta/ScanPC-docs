# ScanPC — Documentação

Repositório **público** que hospeda a documentação do **ScanPC** (app de apreensão
policial — FCTE/UnB, EPS). O código-fonte do app vive no repositório privado
[`FCTE-UNB-EPS5/ScanPC`](https://github.com/FCTE-UNB-EPS5/ScanPC); este repo existe
apenas para publicar a documentação no GitHub Pages (o Pages não publica a partir de
repositórios privados no plano Free).

📖 **Site publicado:** <https://jvcostta.github.io/ScanPC-docs/>

## Stack da doc

- [MkDocs](https://www.mkdocs.org/) + [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/)
- Publicação automática via GitHub Actions (`.github/workflows/deploy-docs.yml`) a cada
  push na `main`, para a branch `gh-pages`.

## Rodar localmente

```bash
python -m pip install -r docs/requirements.txt
mkdocs serve          # http://127.0.0.1:8000
mkdocs build --strict # valida links e referências
```

## Publicar

O deploy é automático via Actions ao dar push na `main`. Para publicar manualmente
(sem Actions):

```bash
mkdocs gh-deploy --force
```

Após o primeiro deploy, habilite o Pages em **Settings → Pages → Source: branch
`gh-pages` / `/ (root)`**.

## Estrutura

```text
mkdocs.yml                    # configuração do site
docs/                         # conteúdo (Markdown)
  requirements.txt            # dependências da doc
.github/workflows/deploy-docs.yml  # build + deploy no Pages
```
