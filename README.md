# Acesso a leitos SUS em Minas Gerais

Análise da distribuição de leitos hospitalares do SUS entre os municípios e regiões de saúde de Minas Gerais, a partir dos dados do Cadastro Nacional de Estabelecimentos de Saúde (CNES).

> **Status:** em desenvolvimento. Exploração inicial concluída; agregação por região de saúde e análise final pendentes.

---

## A pergunta

A capacidade hospitalar do SUS em Minas Gerais está distribuída de forma desigual entre os municípios — e essa desigualdade só é interpretável quando se considera que o atendimento é organizado por regiões de saúde, não por fronteiras municipais.

---

## Achados preliminares

**Menos da metade dos municípios possui leito hospitalar.** Dos 853 municípios de Minas Gerais, apenas **409 (48%)** contam com ao menos um estabelecimento com leito cadastrado no CNES (competência 06/2026). Os demais 444 municípios não possuem nenhum.

**A capacidade é fortemente concentrada.** Os 10 maiores municípios respondem por **40,3%** dos leitos SUS do estado. Belo Horizonte sozinha concentra **19%** — 6.403 dos 33.607 leitos SUS de Minas.

| Município | Leitos SUS (06/2026) |
|---|---|
| Belo Horizonte | 6.403 |
| Juiz de Fora | 1.679 |
| Uberlândia | 1.205 |
| Montes Claros | 904 |
| Teófilo Otoni | 738 |
| Uberaba | 716 |
| Contagem | 496 |
| Ipatinga | 477 |
| Betim | 475 |
| Governador Valadares | 459 |

Esses números são o ponto de partida, não a conclusão: a leitura municipal isolada sugere que metade do estado é um vazio assistencial, o que não corresponde à realidade — pessoas se deslocam para se tratar. A análise por região de saúde, em andamento, busca medir o acesso de forma mais fiel.

---

## Fontes de dados

| Fonte | Conteúdo | Recorte |
|---|---|---|
| [CNES — Leitos](https://dadosabertos.saude.gov.br/dataset/hospitais-e-leitos) | Estabelecimentos, leitos existentes e leitos SUS, por competência mensal | 01/2026 a 06/2026 |
| [SES-MG — Planilha de Regionalização](https://www.saude.mg.gov.br/estudos-assistenciais-e-regionalizacao/) | Municípios, população e classificação por unidade regional, microrregião e macrorregião de saúde | Versão 2026 (ajuste do PDR 2025) |

O conjunto do CNES está catalogado no [Portal Brasileiro de Dados Abertos](https://dados.gov.br/).

A planilha da SES-MG cobre os 853 municípios do estado, distribuídos em 89 microrregiões, 16 macrorregiões e 28 unidades regionais de saúde, sem valores ausentes.

---

## Decisões metodológicas

**Agregação por região de saúde, não por município.** Apenas 409 dos 853 municípios de MG possuem estabelecimento com leito. Uma análise municipal produziria 444 falsos "desertos assistenciais", já que a população se trata fora do município de residência. As microrregiões de saúde do PDR-MG refletem os fluxos reais de atendimento, e não coincidem com as microrregiões geográficas do IBGE.

**`LEITOS_SUS`, não `LEITOS_EXISTENTES`.** A base do CNES inclui estabelecimentos privados sem vínculo com o SUS. Contabilizar leitos existentes superestimaria o acesso público — na base, é comum um estabelecimento privado registrar dezenas de leitos existentes e zero leitos SUS.

**População obtida da própria planilha de regionalização.** A SES-MG publica a população municipal na mesma planilha que define as regiões de saúde. Usar essa fonte, em vez de baixar as estimativas do IBGE separadamente, elimina o risco de divergência entre a base populacional e a base de regionalização — ambas passam a vir do mesmo documento e da mesma data de referência.

**Chave de junção: código DATASUS de 6 dígitos.** O campo `CO_IBGE` do CNES utiliza o código municipal sem o dígito verificador (6 dígitos), enquanto o código IBGE completo tem 7. A planilha da SES-MG traz as duas versões, o que permite a junção direta pelo campo de 6 dígitos, sem conversão. A junção foi validada: nenhum registro do CNES ficou sem região de saúde correspondente.

**Uma competência para o retrato, seis para a série temporal.** Cada estabelecimento aparece uma vez por competência no arquivo. A competência 06/2026 é usada para o retrato principal; as seis competências disponíveis permitem observar variação ao longo do primeiro semestre.

---

## Limitações

**Dados autodeclarados.** A documentação do CNES registra que as informações são enviadas pelos gestores locais de saúde, e que podem ocorrer limitações de conteúdo em razão da forma de captura. A qualidade do cadastro varia entre municípios, e este trabalho não tem como verificar a acurácia dos registros.

**Leito cadastrado não é leito em operação.** A existência de um leito no cadastro não garante disponibilidade efetiva, equipe ou insumos.

**Recorte temporal curto.** Seis competências de um mesmo semestre não permitem conclusões sobre tendências de longo prazo.

**Capacidade, não desfecho.** O estudo mede oferta instalada, não acesso efetivo, tempo de espera ou qualidade do atendimento.

**Fluxos interestaduais não considerados.** Municípios de divisa podem ter parte de sua população atendida em outros estados, o que não é capturado por um recorte restrito a Minas Gerais.

---

## Reprodução

```bash
git clone https://github.com/snowdevelops/acesso-leitos-sus-mg.git
cd acesso-leitos-sus-mg
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Os dados brutos não são versionados (ver `.gitignore`). Baixe os arquivos das fontes listadas acima e coloque em `data/raw/`:

- `Leitos_2026.csv` — CNES (distribuído em `.zip`; extrair antes de usar)
- `Planilha-de-Regionalizacao_Versao-2026.xlsx` — SES-MG

O CSV do CNES usa `;` como separador e codificação `latin-1`. A planilha da SES-MG possui cabeçalho institucional nas duas primeiras linhas e uma linha de fonte ao final:

```python
leitos = pd.read_csv('../data/raw/Leitos_2026.csv', sep=';', encoding='latin-1')

regiao = pd.read_excel(
    '../data/raw/Planilha-de-Regionalizacao_Versao-2026.xlsx',
    skiprows=2,
    nrows=853
)
```

---

## Estrutura

```
├── data/
│   ├── raw/          # dados originais (não versionados)
│   └── processed/    # dados tratados
├── notebooks/
│   └── 01-exploracao.ipynb
├── figuras/
└── README.md
```

---

## Autor

Luiz Fernando Lemos Gondim — [@snowdevelops](https://github.com/snowdevelops)

Projeto desenvolvido para o 2º Concurso de Reúso de Dados Abertos da CGU.