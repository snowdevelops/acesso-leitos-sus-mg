# Acesso a leitos SUS em Minas Gerais

Análise da distribuição de leitos hospitalares do SUS entre as regiões de saúde de Minas Gerais, a partir dos dados do Cadastro Nacional de Estabelecimentos de Saúde (CNES).

---

## O achado

**As regiões mais pobres de Minas Gerais têm mais leitos SUS por habitante que as regiões metropolitanas e industriais. A relação se inverte quando se olha para leitos de UTI.**

A capacidade instalada no interior não é escassa — é de baixa complexidade. Quem precisa de terapia intensiva no Nordeste mineiro ou no Jequitinhonha depende de deslocamento.

| Macrorregião | Leitos SUS / mil hab. | UTI SUS / mil hab. | UTI como % dos leitos |
|---|---|---|---|
| Nordeste | 2,41 | 0,079 | 3,3% |
| Jequitinhonha | 2,22 | 0,168 | 7,6% |
| Sudeste | 2,01 | 0,240 | 11,9% |
| Centro | 1,50 | 0,185 | 12,4% |
| Vale do Aço | 1,15 | 0,186 | 16,1% |

O Nordeste lidera em leitos gerais e é o **último** colocado em UTI. O Vale do Aço é o último em leitos gerais e tem mais que o dobro da UTI per capita do Nordeste. A intensidade de cuidado varia cerca de cinco vezes entre os extremos.

---

## A hipótese inicial estava errada

O projeto partiu da expectativa de encontrar "desertos assistenciais" no interior — regiões pobres com pouca capacidade instalada, em contraste com uma capital bem servida. Os dados contrariaram essa hipótese em duas etapas.

**Primeiro, a leitura municipal engana.** Dos 853 municípios de Minas, apenas **409 (48%)** possuem estabelecimento com leito cadastrado. Os 444 restantes aparecem como vazios absolutos — o que é literalmente verdadeiro e praticamente falso, já que a população se trata em municípios vizinhos. Belo Horizonte sozinha concentra 19% dos leitos SUS do estado (6.403 de 33.607), e os 10 maiores municípios somam 40,3%.

**Segundo, a desigualdade regional aponta na direção oposta à esperada.** Agregando por região de saúde, as regiões com maior taxa de leitos SUS por habitante são justamente Nordeste, Jequitinhonha e Vale do Rio Doce — historicamente as mais pobres do estado. As menores taxas estão em regiões metropolitanas e industriais: Vale do Aço, Oeste, Extremo Sul.

Apenas ao separar UTI dos leitos gerais o padrão fica inteligível: não se trata de melhor ou pior assistência, mas de uma divisão territorial entre baixa e alta complexidade.

---

## Fontes de dados

| Fonte | Conteúdo | Recorte |
|---|---|---|
| [CNES — Leitos](https://dadosabertos.saude.gov.br/dataset/hospitais-e-leitos) | Estabelecimentos, leitos existentes, leitos SUS e leitos de UTI, por competência mensal | 01/2026 a 06/2026 |
| [SES-MG — Planilha de Regionalização](https://www.saude.mg.gov.br/estudos-assistenciais-e-regionalizacao/) | Municípios, população e classificação por unidade regional, microrregião e macrorregião de saúde | Versão 2026 (ajuste do PDR 2025) |

O conjunto do CNES está catalogado no [Portal Brasileiro de Dados Abertos](https://dados.gov.br/).

A planilha da SES-MG cobre os 853 municípios do estado, distribuídos em 89 microrregiões, 16 macrorregiões e 28 unidades regionais de saúde, sem valores ausentes.

---

## Decisões metodológicas

**Agregação por região de saúde, não por município.** Apenas 409 dos 853 municípios de MG possuem estabelecimento com leito. Uma análise municipal produziria 444 falsos desertos assistenciais. As regiões de saúde do PDR-MG refletem os fluxos reais de atendimento, e não coincidem com as divisões geográficas do IBGE.

**Macrorregião como recorte principal, microrregião como verificação.** Na escala microrregional, a razão entre a maior e a menor taxa é de 5,5x. Na escala macrorregional, cai para 2,1x. A diferença revela um efeito de vizinhança: microrregiões limítrofes a grandes polos (Contagem, Betim, Vespasiano) aparecem artificialmente vazias porque sua população é atendida na capital. Como o padrão sobrevive à agregação macrorregional, ele não é artefato de fronteira — mas o recorte macro é o mais conservador e foi adotado para as conclusões.

**`LEITOS_SUS`, não `LEITOS_EXISTENTES`.** A base do CNES inclui estabelecimentos privados sem vínculo com o SUS. Contabilizar leitos existentes superestimaria o acesso público — é comum um estabelecimento privado registrar dezenas de leitos existentes e zero leitos SUS.

**UTI não é somada ao total.** Os campos `UTI_*_SUS` são subconjunto de `LEITOS_SUS`, não contagem paralela. Verificação: em nenhum registro a UTI excede o total de leitos, e não há UTI em estabelecimento sem leitos SUS. Somar os dois campos produziria dupla contagem. A UTI é analisada como recorte separado.

**População agregada a partir da base completa de municípios.** O denominador populacional vem da planilha da SES-MG com os 853 municípios, não do resultado da junção com o CNES. Duas razões: a junção contém apenas os 409 municípios com estabelecimento, e nela cada município aparece uma vez por estabelecimento — somar a população desse recorte produziria mais de 220 milhões de habitantes para um estado de 21,4 milhões.

**Chave de junção: código DATASUS de 6 dígitos.** O campo `CO_IBGE` do CNES usa o código municipal sem o dígito verificador, enquanto o código IBGE completo tem 7 dígitos. A planilha da SES-MG traz as duas versões, permitindo junção direta sem conversão. Validado: nenhum registro do CNES ficou sem região de saúde correspondente.

**Competência 06/2026 para o retrato principal.** Cada estabelecimento aparece uma vez por competência. As seis competências disponíveis permitem observar variação ao longo do primeiro semestre.

---

## Limitações

**Dados autodeclarados.** A documentação do CNES registra que as informações são enviadas pelos gestores locais de saúde. A qualidade do cadastro varia entre municípios, e este trabalho não tem como verificar a acurácia dos registros.

**Leito cadastrado não é leito em operação.** A existência de um leito no cadastro não garante disponibilidade efetiva, equipe ou insumos.

**A separação entre leito geral e UTI não esgota a complexidade.** UTI é um indicador de alta complexidade, mas não o único. Serviços como oncologia, hemodinâmica e transplantes não são capturados por esta análise.

**Capacidade, não desfecho.** O estudo mede oferta instalada, não acesso efetivo, tempo de espera, taxa de ocupação ou qualidade do atendimento.

**Fluxos interestaduais não considerados.** Municípios de divisa podem ter parte de sua população atendida em outros estados, o que não é capturado por um recorte restrito a Minas Gerais.

**Recorte temporal curto.** Seis competências de um mesmo semestre não permitem conclusões sobre tendências de longo prazo.

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