# Categorias

Cada item apreendido pertence a uma **categoria**, que define os campos do formulário
e quais deles a IA pode preencher. Há **5 categorias padrão** (no backend) e o usuário
pode criar **categorias custom** (no dispositivo).

## Categorias padrão

Definidas em
[`backend/app/domain/categories_default.py`](https://github.com/FCTE-UNB-EPS5/ScanPC/blob/main/backend/app/domain/categories_default.py)
e servidas por `GET /categories/defaults`.

| Categoria (`id`) | Campos | Extraíveis por IA |
|------------------|--------|-------------------|
| Eletrônicos (`eletronico`) | tipo, marca, modelo, cor, IMEI, nº série, observações | tipo, marca, modelo, cor, IMEI, nº série, observações |
| Veículos (`veiculo`) | tipo, marca, modelo, cor, placa, chassi, ano, observações | todos acima |
| Armas (`arma`) | tipo, marca, modelo, calibre, nº série, observações | todos acima |
| Dinheiro (`dinheiro`) | moeda, valor, observações | — (nenhum) |
| Documentos (`documento`) | tipo, número, titular, CPF, RG, órgão emissor, nascimento, emissão, filiação (pai/mãe), naturalidade, observações | todos os campos |

Cada campo tem um `type` (`text`, `textarea`, `number`, `select`) e, quando `select`,
uma lista de `options` (ex.: tipo de eletrônico = Celular / Notebook / Tablet / Outro).

!!! warning "Campos sensíveis e a IA"
    O flag `ai_extractable` por campo controla o que a IA pode tocar. Campos que a IA
    tende a alucinar — como **valor em dinheiro** — ficam marcados como não-extraíveis
    e são preenchidos à mão pelo agente. Ver [Extração por IA](ia-extracao.md).

## Categorias custom

O usuário pode criar uma categoria nova (ID + rótulo + lista de campos, cada um com
tipo e flag de IA). Ela é salva no `localStorage` do dispositivo e enviada ao backend
nas requisições de IA e de PDF, ao lado das padrão. Ver
[Frontend → Estado e persistência](../frontend/estado-persistencia.md).
