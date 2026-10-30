# Análise de base da Receita Federal sobre Concentração de Renda, Tributação e Isenções do IRPF no Brasil

## Visão Geral do Projeto

#### Objetivo:
Desenvolver uma solução analítica interativa em Power BI a partir de dados públicos da Receita Federal (RFB) para auditar a distribuição da renda por centis, a regressividade da carga tributária (alíquota efetiva) e o perfil dos rendimentos isentos e tributáveis no país.

#### Tecnologias Utilizadas:
Power BI Desktop, Power Query (ETL), Linguagem DAX, Modelagem Relacional e Design UX/UI.

## Fontes de Dados
#### Origem:
Portal de Dados Abertos do Governo Federal / Receita Federal do Brasil (RFB).

#### Dataset Principal:
Distribuição de Renda apurada pelos contribuintes do Imposto de Renda da Pessoa Física (IRPF), agregada por centis (1 a 100) em nível nacional e por Unidade da Federação (UF).

#### Granularidade:
Cada linha representa um centil de contribuintes por ano-calendário e ente federativo.

### Arquitetura e Pipeline de Dados (ETL / Power Query)
O processo de extração, transformação e carga (ETL) foi executado no Power Query com as seguintes etapas:

#### Ingestão:
Leitura do ficheiro CSV delimitado por ponto e vírgula (;) com codificação UTF-8.

#### Filtragem de Granularidade:
Remoção de sub-frações do centil 100 (ex: 100.10.1) para padronizar estritamente os 100 centis principais.

#### Tratamento de Erros e Valores Nulos:
Substituição de erros de sistema e células vazias (null) por 0 nas colunas numéricas e monetárias para evitar quebra de linhas e falhas em cálculos de agregação.

#### Tipagem de Dados:
Conversão correta de colunas textuais (Ente Federativo), numéricas inteiras (Centil, Ano-calendário, Quantidade de Contribuintes) e decimais fixas/moeda (Imposto Devido, Rendimentos).

#### Enriquecimento (Colunas Calculadas):
* Criação da coluna condicional Faixa de Centil agrupando os dados em categorias analíticas (50% Base, 50% Intermediários, Top 10%, Top 1%).
* Criação de colunas personalizadas para consolidação dos rendimentos totais e mapeamento regional por Unidade da Federação.

## Modelagem de Dados e Dicionário de Medidas (DAX)
O modelo foi estruturado para garantir alta performance de cálculo e reatividade nos filtros cruzados. As principais medidas DAX implementadas foram:
* Total Imposto Devido = SUM(distribuicao_renda[Imposto Devido [R$ milhões]])
* Total Rendimento Tributavel = SUM(distribuicao_renda[Rendimentos Tributaveis - Soma da RTB do Centil [R$ milhões]])
* Aliquota Efetiva % = DIVIDE([Total Imposto Devido], [Total Rendimento Tributavel], 0)
* Renda Media Por Contribuinte = DIVIDE(SUM(distribuicao_renda[Total_Rendimentos_M]) * 1000000, SUM(distribuicao_renda[Quantidade de Contribuintes]), 0)
* Medidas de Isenções e Deduções: Consolidação de lucros/dividendos, rendimentos do Simples Nacional e despesas dedutíveis (saúde/instrução).

## Desenvolvimento e Estrutura de Visões
O relatório adota os padrões de design executivo moderno (estilo corporativo/institucional):
* Padrão de Cores: Azul corporativo da Receita Federal com elementos de contraste otimizados.
* Gestão de Visões (Bookmarks & Painel de Seleção):
  * Visão de Tributos e Impostos: Focada na curva de concentração, alíquota efetiva por centil e total de imposto devido por faixa de renda.
  * Visão de Isenções e Benefícios: Focada na análise de lucros, dividendos isentos e despesas dedutíveis concentradas no topo da pirâmide.
* Segmentadores (Slicers): Filtros dinâmicos globais por Ano-calendário e Ente Federativo (UF).
