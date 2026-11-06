# Pra onde vai a emenda?

Análise das **emendas parlamentares individuais (2023–2026)** de dois deputados federais: **Nikolas Ferreira (PL-MG)** e **Erika Hilton (PSOL-SP)**.

Todo deputado tem a mesma cota de emenda individual. A pergunta, portanto, não é *quanto* cada um indica, e sim **para onde, para quê e por qual modalidade** o dinheiro vai:

- quanto foi empenhado e pago, e quanto saiu via **transferência especial ("emenda Pix")**, comparado com todos os parlamentares;
- em que **áreas** (função → subfunção → ação orçamentária);
- **quem recebeu** de fato: prefeituras, fundos, governos estaduais, organizações da sociedade civil, empresas e bancos intermediários;
- **para quê**, pelo objeto dos convênios.

O notebook foi escrito para ser **auditado**. Cada etapa informa de onde vem o dado, o que o código faz, que decisão metodológica foi tomada e como conferir o resultado. Há células de checagem (🔎) e avisos (⚠️) ao longo do texto, e um checklist no final.

## Conteúdo

| Arquivo | Descrição |
|---|---|
| [`emendas_nikolas_erika.ipynb`](emendas_nikolas_erika.ipynb) | Notebook completo: extração, filtros, análises, checagens e limitações |

Os dados brutos (`data/`) e as tabelas geradas (`out/`) **não ficam no repositório**: o notebook baixa os dados das fontes oficiais e gera as tabelas na própria execução.

## Fonte dos dados

[Portal da Transparência — Emendas parlamentares (arquivo único)](https://portaldatransparencia.gov.br/download-de-dados/emendas-parlamentares/UNICO), cerca de 33 MB zipado, com três CSVs:

| CSV | Conteúdo |
|---|---|
| `EmendasParlamentares.csv` | Uma linha por emenda × classificação orçamentária × localidade. Traz autor, tipo e valores empenhado, liquidado, pago e restos a pagar |
| `EmendasParlamentares_PorFavorecido.csv` | Quem recebeu o dinheiro (CNPJ/nome, natureza jurídica, município, valor recebido) |
| `EmendasParlamentares_Convenios.csv` | Convênios firmados com o dinheiro da emenda (convenente, objeto, valor acordado) |

## Como rodar

Requisitos: Python 3.10+ e Jupyter.

```bash
git clone https://github.com/desviopadrao/pra-onde-vai-a-emenda.git
```

```bash
cd pra-onde-vai-a-emenda
```

```bash
pip install pandas matplotlib unidecode requests jupyter
```

```bash
jupyter notebook emendas_nikolas_erika.ipynb
```

Rode com **Restart Kernel & Run All**. Na primeira execução, o arquivo do Portal é baixado para `data/emendas.zip`, o que pode demorar. Nas execuções seguintes ele é lido do cache. As tabelas exportadas vão para `out/`.

Para analisar outros parlamentares ou outro período, altere apenas `DEPUTADOS` e `ANOS` na célula de setup.

### Reprodutibilidade

O Portal atualiza os dados continuamente, então **os números mudam conforme a data do download**. A célula *Proveniência* (seção 1) imprime a data e o hash SHA-256 do arquivo usado. Anote os dois ao citar qualquer número: outra pessoa com o mesmo hash deve chegar aos mesmos resultados. Para refazer a análise com dados novos, apague `data/emendas.zip` e rode tudo de novo.

## Estrutura da análise

0. Setup e parâmetros
1. Extração e proveniência
2. Filtro dos deputados e variáveis derivadas
3. Quanto foi pago e por qual modalidade, com comparação do uso de Pix entre todos os parlamentares
4. Localidade da programação orçamentária
5. Áreas: função, subfunção e ação
6. Quem recebeu (favorecidos) e por qual canal de entrega
7. Convênios: o "para quê" em texto
8. Limitações e checklist do auditor

## Pontos de atenção

- **Localidade não é destino.** A coluna `LOCALIDADE DE APLICACAO DO RECURSO` indica onde a despesa foi *programada* no orçamento (`NACIONAL`, `MULTIPLO`, `(UF)`), não para onde o dinheiro foi. O destino real está no arquivo de favorecidos (seção 6).
- **Duas medidas de "pago".** `pago_total` (pago no exercício + restos a pagar pagos) é a melhor medida do dinheiro que saiu. Algumas tabelas usam só `pago`, e o notebook indica qual medida cada uma usa.
- **Favorecido ≠ beneficiário final.** Fundos, bancos operadores e entidades podem repassar o dinheiro adiante.
- **Valor de convênio é valor acordado**, não pago, e não deve ser somado ao `pago_total`.
- **O "canal" de entrega é um agrupamento desta análise**, feito a partir da natureza jurídica do favorecido. Não é uma categoria oficial.
- **O benchmark de Pix** inclui senadores e usa um corte de R$ 20 milhões de pago total (decisão do analista).
- **O filtro de autor é por trecho de nome.** O notebook confere que não entrou nenhum homônimo.

A lista completa de limitações está na seção 8 do notebook.

## Licença

[MIT](LICENSE) © 2026 Desvio Padrão
