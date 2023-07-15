---
jupyter:
  deepnote: {}
  deepnote_notebook_id: 627d4a3cbd704a3aa7befa90201972f4
  deepnote_persisted_session:
    createdAt: "2023-07-15T00:46:27.643Z"
  nbformat: 4
  nbformat_minor: 0
  orig_nbformat: 2
---

::: {.cell .markdown cell_id="c3394ae0337947dd9d2b3d605639d624" deepnote_cell_type="text-cell-h1" formattedRanges="[]"}
# UnB em Dados - IEEE Trainee S1
:::

::: {.cell .markdown cell_id="f0e33a7f207943a2b9e996dc83c3955c" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Durante a Semana 1 do processo Trainee do IEEE objetivou-se aplicar
alguns conceitos introdutórios de Análise de Dados em Python para em um
dataset de escolha livre.
:::

::: {.cell .markdown cell_id="9fe9e03a67e3438d9287f5513609f898" deepnote_cell_type="text-cell-p" formattedRanges="[{\"fromCodePoint\":231,\"ranges\":[],\"toCodePoint\":251,\"type\":\"link\",\"url\":\"http://dados.unb.br/\"}]"}
Minha escolha foi o dataset gastos-por-unidade-06-2023.csv, que
apresenta uma compilação dos gastos das instituições e unidades
acadêmicas da UnB. Esse dataset, assim como vários outros relacionados a
UnB estão disponíveis no site <http://dados.unb.br/>
:::

::: {.cell .markdown cell_id="d76b5c359bdd4316979f5d38685cfbb8" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Primeiramente, foram levantas as seguintes perguntas a serem
respondidas:
:::

::: {.cell .markdown cell_id="856c7e8b33cd4eadb4681243e0e85907" deepnote_cell_type="text-cell-bullet" formattedRanges="[]"}
-   Quais as unidades que mais consumiram recursos no período da
    análise?
:::

::: {.cell .markdown cell_id="ed07c5fd09374ddcab1c1ca3bf76be28" deepnote_cell_type="text-cell-bullet" formattedRanges="[]"}
-   Quais as principais finalidades (natureza da despesa)?
:::

::: {.cell .markdown cell_id="89928e2e41b340169413f8f3d43e6034" deepnote_cell_type="text-cell-bullet" formattedRanges="[]"}
-   Qual dos cursos(faculdades) gastou maior verba no período?
:::

::: {.cell .markdown cell_id="c52051e6a7f3442c879a04c189d98ca0" deepnote_cell_type="text-cell-bullet" formattedRanges="[]"}
-   Analisando as Engenharias, qual delas consumiu maior verba?
:::

::: {.cell .markdown cell_id="b85ac288b1eb4aa48fcc70864a42baac" deepnote_cell_type="text-cell-bullet" formattedRanges="[]"}
-   Seria possível estimar os gastos dos anos seguintes com base nos
    dados passados?
:::

::: {.cell .markdown cell_id="4072a9d916bd4aa58c2011f982e22095" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Sendo assim, vamos começar a análise, sendo o primeiro passo o de
pré-processamento dos dados:
:::

::: {.cell .code execution_count="1" cell_id="6806eea8777048869f449df98899e78b" deepnote_cell_type="code" deepnote_to_be_reexecuted="false" execution_millis="1318" execution_start="1689382707246" source_hash="\"78748374\""}
``` python
#Inicialização e instalação das dependências necessárias
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
```
:::

::: {.cell .markdown cell_id="3b00527ec25c4a18bde843a7c5985438" deepnote_cell_type="text-cell-h1" formattedRanges="[]"}
# Pre-processamento dos Datasets
:::

::: {.cell .markdown cell_id="87e78757b7a84d379295855dec0133bb" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Para o pré-processamento foram removidos os símbolos financeiros (R\$)
dos valores, de forma a transformá-los em unicamente numéricos. Também
foi criado um dataset com o união dos dados de 2021 até 2023, para os
meses que dispõem de dados.
:::

::: {.cell .code execution_count="2" cell_id="5d2c93b1ba594a8da5434880ef001b55" deepnote_cell_type="code" deepnote_table_loading="false" deepnote_table_state="{\"filters\":[],\"pageIndex\":9,\"pageSize\":10,\"sortBy\":[]}" deepnote_to_be_reexecuted="false" execution_millis="549" execution_start="1689382708561" source_hash="fa8e2750"}
``` python
def organize(path):
    result = pd.read_csv(path)
    result['valor'] = result['valor'].str.replace('.','', regex=False).str.replace('$','',regex=False).str.replace('R','',regex=False).str.replace(' ','',regex=False).str.replace(',','.',regex=False)
    result['valor'] = result['valor'].astype('float')
    result['periodo'] = path[-8:-4] + "-" + path[-11:-9]
    return result

#2021
df21_07 = organize('db_gastos/gastos-por-unidade-07-2021.csv')
df21_08 = organize('db_gastos/gastos-por-unidade-08-2021.csv')
df21_09 = organize('db_gastos/gastos-por-unidade-09-2021.csv')
df21_10 = organize('db_gastos/gastos-por-unidade-10-2021.csv')

#2022
df22_02 = organize('db_gastos/gastos-por-unidade-02-2022.csv')
df22_03 = organize('db_gastos/gastos-por-unidade-03-2022.csv')
df22_05 = organize('db_gastos/gastos-por-unidade-05-2022.csv')
df22_06 = organize('db_gastos/gastos-por-unidade-06-2022.csv')
df22_07 = organize('db_gastos/gastos-por-unidade-07-2022.csv')

#2023
df23_03 = organize('db_gastos/gastos-por-unidade-03-2023.csv')
df23_04 = organize('db_gastos/gastos-por-unidade-04-2023.csv')
df23_05 = organize('db_gastos/gastos-por-unidade-05-2023.csv')

#Unindo os datasets
df_gastos = pd.concat([df21_07,df21_08,df21_09,df21_10,df22_02,df22_03,df22_05,df22_06,df22_07,df23_03,df23_04,df23_05], ignore_index=True)
df_gastos
```

::: {.output .execute_result execution_count="2"}
``` json
{"column_count":5,"columns":[{"dtype":"object","name":"natureza_despesa","stats":{"categories":[{"count":3588,"name":"SERV. PESSOA JURÍDICA"},{"count":2171,"name":"CUSTEIO"},{"count":13604,"name":"50 others"}],"nan_count":0,"unique_count":52}},{"dtype":"int64","name":"id_unidade","stats":{"histogram":[{"bin_end":227.9,"bin_start":69,"count":1885},{"bin_end":386.8,"bin_start":227.9,"count":1992},{"bin_end":545.7,"bin_start":386.8,"count":3064},{"bin_end":704.6,"bin_start":545.7,"count":4430},{"bin_end":863.5,"bin_start":704.6,"count":2068},{"bin_end":1022.4000000000001,"bin_start":863.5,"count":2892},{"bin_end":1181.3,"bin_start":1022.4000000000001,"count":217},{"bin_end":1340.2,"bin_start":1181.3,"count":1770},{"bin_end":1499.1000000000001,"bin_start":1340.2,"count":498},{"bin_end":1658,"bin_start":1499.1000000000001,"count":547}],"max":"1658","min":"69","nan_count":0,"unique_count":424}},{"dtype":"float64","name":"valor","stats":{"histogram":[{"bin_end":1.60339389956e8,"bin_start":0,"count":19223},{"bin_end":3.20678779912e8,"bin_start":1.60339389956e8,"count":59},{"bin_end":4.8101816986800003e8,"bin_start":3.20678779912e8,"count":28},{"bin_end":6.41357559824e8,"bin_start":4.8101816986800003e8,"count":12},{"bin_end":8.0169694978e8,"bin_start":6.41357559824e8,"count":7},{"bin_end":9.620363397360001e8,"bin_start":8.0169694978e8,"count":12},{"bin_end":1.122375729692e9,"bin_start":9.620363397360001e8,"count":2},{"bin_end":1.282715119648e9,"bin_start":1.122375729692e9,"count":9},{"bin_end":1.443054509604e9,"bin_start":1.282715119648e9,"count":4},{"bin_end":1.60339389956e9,"bin_start":1.443054509604e9,"count":7}],"max":"1603393899.56","min":"0.0","nan_count":0,"unique_count":4334}},{"dtype":"object","name":"unidade","stats":{"categories":[{"count":288,"name":"DGP - FOLHA DE PAGAMENTO"},{"count":193,"name":"SECRETARIA DE INFRAESTRUTURA"},{"count":18882,"name":"464 others"}],"nan_count":0,"unique_count":466}},{"dtype":"object","name":"periodo","stats":{"categories":[{"count":1778,"name":"2023-05"},{"count":1774,"name":"2023-04"},{"count":15811,"name":"10 others"}],"nan_count":0,"unique_count":12}},{"dtype":"int64","name":"_deepnote_index_column"}],"row_count":19363,"rows":[{"_deepnote_index_column":90,"id_unidade":671,"natureza_despesa":"MAT. CONSUMO","periodo":"2021-07","unidade":"CENTRO PESQ. APLIC. BAMBU FIBRAS NATURAI","valor":11989.11},{"_deepnote_index_column":91,"id_unidade":671,"natureza_despesa":"EQUIP. MATERIAL PERMANENTE","periodo":"2021-07","unidade":"CENTRO PESQ. APLIC. BAMBU FIBRAS NATURAI","valor":12115},{"_deepnote_index_column":92,"id_unidade":671,"natureza_despesa":"SERV. PESSOA JURÍDICA","periodo":"2021-07","unidade":"CENTRO PESQ. APLIC. BAMBU FIBRAS NATURAI","valor":602929.64},{"_deepnote_index_column":93,"id_unidade":671,"natureza_despesa":"PASSAGENS","periodo":"2021-07","unidade":"CENTRO PESQ. APLIC. BAMBU FIBRAS NATURAI","valor":95000},{"_deepnote_index_column":94,"id_unidade":671,"natureza_despesa":"AUX. FINANCEIRO ESTUDANTE","periodo":"2021-07","unidade":"CENTRO PESQ. APLIC. BAMBU FIBRAS NATURAI","valor":225600},{"_deepnote_index_column":95,"id_unidade":671,"natureza_despesa":"DIÁRIAS","periodo":"2021-07","unidade":"CENTRO PESQ. APLIC. BAMBU FIBRAS NATURAI","valor":2500},{"_deepnote_index_column":96,"id_unidade":670,"natureza_despesa":"EQUIP. MATERIAL PERMANENTE","periodo":"2021-07","unidade":"CENTRO PESQUISA ARQUITETURA INFORMACAO","valor":25000},{"_deepnote_index_column":97,"id_unidade":659,"natureza_despesa":"CUSTEIO","periodo":"2021-07","unidade":"CENTRO REF. CONS. NATU. RECUP. A. DEGRAD","valor":101956.3},{"_deepnote_index_column":98,"id_unidade":659,"natureza_despesa":"DIÁRIAS","periodo":"2021-07","unidade":"CENTRO REF. CONS. NATU. RECUP. A. DEGRAD","valor":942.78},{"_deepnote_index_column":99,"id_unidade":659,"natureza_despesa":"SERV. PESSOA JURÍDICA","periodo":"2021-07","unidade":"CENTRO REF. CONS. NATU. RECUP. A. DEGRAD","valor":1777.9}]}
```
:::
:::

::: {.cell .markdown cell_id="a94bdd8693d34d7a87017256a5fe8e8d" deepnote_cell_type="text-cell-h1" formattedRanges="[]"}
# 1.1 - Quais as unidades que mais consumiram recursos no período total da análise? {#11---quais-as-unidades-que-mais-consumiram-recursos-no-período-total-da-análise}
:::

::: {.cell .code execution_count="3" cell_id="0debcc7b86524e0cb26edcd47757a5dc" deepnote_cell_type="code" deepnote_table_loading="false" deepnote_table_state="{\"filters\":[],\"pageIndex\":0,\"pageSize\":10,\"sortBy\":[]}" deepnote_to_be_reexecuted="false" execution_millis="14" execution_start="1689382708853" source_hash="b9ffd20f"}
``` python
df_total_unidade = df_gastos.groupby('unidade',as_index=False).sum('valor').sort_values(by=['valor'],ascending=False)
df_total_unidade.head()
```

::: {.output .execute_result execution_count="3"}
``` json
{"column_count":3,"columns":[{"dtype":"object","name":"unidade","stats":{"categories":[{"count":1,"name":"DGP - FOLHA DE PAGAMENTO"},{"count":1,"name":"UNIVERSIDADE DE BRASÍLIA"},{"count":3,"name":"3 others"}],"nan_count":0,"unique_count":5}},{"dtype":"int64","name":"id_unidade","stats":{"histogram":[{"bin_end":36761.4,"bin_start":15470,"count":1},{"bin_end":58052.8,"bin_start":36761.4,"count":0},{"bin_end":79344.20000000001,"bin_start":58052.8,"count":2},{"bin_end":100635.6,"bin_start":79344.20000000001,"count":0},{"bin_end":121927,"bin_start":100635.6,"count":1},{"bin_end":143218.40000000002,"bin_start":121927,"count":0},{"bin_end":164509.80000000002,"bin_start":143218.40000000002,"count":0},{"bin_end":185801.2,"bin_start":164509.80000000002,"count":0},{"bin_end":207092.6,"bin_start":185801.2,"count":0},{"bin_end":228384,"bin_start":207092.6,"count":1}],"max":"228384","min":"15470","nan_count":0,"unique_count":5}},{"dtype":"float64","name":"valor","stats":{"histogram":[{"bin_end":6.751092797299004e9,"bin_start":2.963549583560001e9,"count":2},{"bin_end":1.0538636011038006e10,"bin_start":6.751092797299004e9,"count":1},{"bin_end":1.4326179224777008e10,"bin_start":1.0538636011038006e10,"count":0},{"bin_end":1.811372243851601e10,"bin_start":1.4326179224777008e10,"count":0},{"bin_end":2.1901265652255013e10,"bin_start":1.811372243851601e10,"count":1},{"bin_end":2.5688808865994015e10,"bin_start":2.1901265652255013e10,"count":0},{"bin_end":2.9476352079733017e10,"bin_start":2.5688808865994015e10,"count":0},{"bin_end":3.326389529347202e10,"bin_start":2.9476352079733017e10,"count":0},{"bin_end":3.705143850721102e10,"bin_start":3.326389529347202e10,"count":0},{"bin_end":4.083898172095002e10,"bin_start":3.705143850721102e10,"count":1}],"max":"40838981720.95002","min":"2963549583.560001","nan_count":0,"unique_count":5}},{"dtype":"int64","name":"_deepnote_index_column"}],"row_count":5,"rows":[{"_deepnote_index_column":85,"id_unidade":228384,"unidade":"DGP - FOLHA DE PAGAMENTO","valor":4.083898172095002e10},{"_deepnote_index_column":464,"id_unidade":114950,"unidade":"UNIVERSIDADE DE BRASÍLIA","valor":1.8288867860870007e10},{"_deepnote_index_column":131,"id_unidade":73810,"unidade":"FUNDAÇÃO UNIVERSIDADE DE BRASÍLIA","valor":8.286598382639998e9},{"_deepnote_index_column":95,"id_unidade":68220,"unidade":"DIRETORIA DE CONTRATOS ADMINISTRATIVOS","valor":5.28725961687e9},{"_deepnote_index_column":165,"id_unidade":15470,"unidade":"PREFEITURA DA UNB","valor":2.963549583560001e9}]}
```
:::
:::

::: {.cell .markdown cell_id="09939720d62a4254a4e94b33aee592a1" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Ao avaliar o período total, podemos perceber que os maiores gatos da UnB
foram em relação à folha de pagamentos e gastos de mantenimento da
infraestrutura da Universidade.
:::

::: {.cell .markdown cell_id="21d2227bffa1494082a87c04aaadea1b" deepnote_cell_type="text-cell-h1" formattedRanges="[]"}
# 1.1 - Quais as principais finalidades (natureza da despesas)? {#11---quais-as-principais-finalidades-natureza-da-despesas}
:::

::: {.cell .code execution_count="4" cell_id="ee0defa9d86649f19a780c61b76fe875" deepnote_cell_type="code" deepnote_table_loading="false" deepnote_table_state="{\"filters\":[],\"pageIndex\":0,\"pageSize\":10,\"sortBy\":[]}" deepnote_to_be_reexecuted="false" execution_millis="147" execution_start="1689382708867" source_hash="f32d3ca"}
``` python
df_natureza = df_gastos.groupby(['unidade','natureza_despesa'], as_index=False)['valor'].sum()
df_natureza.sort_values(by=['valor'],ascending=False)
```

::: {.output .execute_result execution_count="4"}
``` json
{"column_count":3,"columns":[{"dtype":"object","name":"unidade","stats":{"categories":[{"count":29,"name":"UNIVERSIDADE DE BRASÍLIA"},{"count":26,"name":"FUNDAÇÃO UNIVERSIDADE DE BRASÍLIA"},{"count":1971,"name":"464 others"}],"nan_count":0,"unique_count":466}},{"dtype":"object","name":"natureza_despesa","stats":{"categories":[{"count":357,"name":"SERV. PESSOA JURÍDICA"},{"count":223,"name":"CUSTEIO"},{"count":1446,"name":"50 others"}],"nan_count":0,"unique_count":52}},{"dtype":"float64","name":"valor","stats":{"histogram":[{"bin_end":1.6194911745519996e9,"bin_start":0,"count":2013},{"bin_end":3.238982349103999e9,"bin_start":1.6194911745519996e9,"count":5},{"bin_end":4.858473523655998e9,"bin_start":3.238982349103999e9,"count":2},{"bin_end":6.477964698207998e9,"bin_start":4.858473523655998e9,"count":3},{"bin_end":8.097455872759998e9,"bin_start":6.477964698207998e9,"count":0},{"bin_end":9.716947047311996e9,"bin_start":8.097455872759998e9,"count":2},{"bin_end":1.1336438221863997e10,"bin_start":9.716947047311996e9,"count":0},{"bin_end":1.2955929396415997e10,"bin_start":1.1336438221863997e10,"count":0},{"bin_end":1.4575420570967997e10,"bin_start":1.2955929396415997e10,"count":0},{"bin_end":1.6194911745519997e10,"bin_start":1.4575420570967997e10,"count":1}],"max":"16194911745.519997","min":"0.0","nan_count":0,"unique_count":1926}},{"dtype":"int64","name":"_deepnote_index_column"}],"row_count":2026,"rows":[{"_deepnote_index_column":588,"natureza_despesa":"VENCIMENTOS E VANTAGENS FIXAS - PESSOAL CIVIL (319011)","unidade":"DGP - FOLHA DE PAGAMENTO","valor":1.6194911745519997e10},{"_deepnote_index_column":1993,"natureza_despesa":"CUSTEIO","unidade":"UNIVERSIDADE DE BRASÍLIA","valor":9.63023401525e9},{"_deepnote_index_column":565,"natureza_despesa":"APOSENT.RPPS, RESER.REMUNER. E REFOR.MILITAR (319001)","unidade":"DGP - FOLHA DE PAGAMENTO","valor":9.073275622369999e9},{"_deepnote_index_column":578,"natureza_despesa":"OBRIGAÇÕES PATRONAIS - OP. INTRA-ORÇAMENTÁRIAS","unidade":"DGP - FOLHA DE PAGAMENTO","valor":5.47351512256e9},{"_deepnote_index_column":584,"natureza_despesa":"SENTENÇAS JUDICIAIS","unidade":"DGP - FOLHA DE PAGAMENTO","valor":5.140896081129999e9},{"_deepnote_index_column":932,"natureza_despesa":"CUSTEIO","unidade":"FUNDAÇÃO UNIVERSIDADE DE BRASÍLIA","valor":4.97316999898e9},{"_deepnote_index_column":629,"natureza_despesa":"LOC. MÃO-DE-OBRA","unidade":"DIRETORIA DE CONTRATOS ADMINISTRATIVOS","valor":4.78106973401e9},{"_deepnote_index_column":2010,"natureza_despesa":"PESSOAL E ENCARGOS SOCIAIS","unidade":"UNIVERSIDADE DE BRASÍLIA","valor":3.7362110374400005e9},{"_deepnote_index_column":1206,"natureza_despesa":"SERV. PESSOA JURÍDICA","unidade":"PREFEITURA DA UNB","valor":2.55421693861e9},{"_deepnote_index_column":2012,"natureza_despesa":"SERV. PESSOA JURÍDICA","unidade":"UNIVERSIDADE DE BRASÍLIA","valor":2.39824492538e9}]}
```
:::
:::

::: {.cell .markdown cell_id="7fe74f57ce7349d480710b5a62f82d20" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Ao avaliar o período total, podemos perceber que os maiores gatos da UnB
foram em relação à folha de pagamentos e gastos de mantenimento da
infraestrutura da Universidade.
:::

::: {.cell .markdown cell_id="9d2d393d20314cec9891c9332a4b11af" deepnote_cell_type="text-cell-h1" formattedRanges="[]"}
# 1.2 - Qual dos cursos(faculdades) gastou maior verba no período? {#12---qual-dos-cursosfaculdades-gastou-maior-verba-no-período}
:::

::: {.cell .code execution_count="5" cell_id="dacb52687e514019822d9de217d9f66d" deepnote_cell_type="code" deepnote_table_loading="false" deepnote_table_state="{\"filters\":[],\"pageIndex\":0,\"pageSize\":10,\"sortBy\":[]}" deepnote_to_be_reexecuted="false" execution_millis="87" execution_start="1689382708926" source_hash="3c21d229"}
``` python
filtro = (df_gastos['unidade'].str.contains('DEPTO|FACULDADE|DEPARTAMENTO|INSTITUTO', regex=True))
df_faculdades = df_gastos[filtro].groupby(['unidade'], as_index=False)['valor'].sum()
df_faculdades.sort_values(by=['valor'],ascending=False).reset_index(drop=True).head(15)
```

::: {.output .execute_result execution_count="5"}
``` json
{"column_count":2,"columns":[{"dtype":"object","name":"unidade","stats":{"categories":[{"count":1,"name":"FACULDADE DE MEDICINA"},{"count":1,"name":"FACULDADE DE  CIÊNCIAS DA SAÚDE"},{"count":13,"name":"13 others"}],"nan_count":0,"unique_count":15}},{"dtype":"float64","name":"valor","stats":{"histogram":[{"bin_end":1.3597000829399994e8,"bin_start":1.1907310497999996e8,"count":3},{"bin_end":1.5286691160799995e8,"bin_start":1.3597000829399994e8,"count":3},{"bin_end":1.6976381492199993e8,"bin_start":1.5286691160799995e8,"count":1},{"bin_end":1.8666071823599994e8,"bin_start":1.6976381492199993e8,"count":2},{"bin_end":2.0355762154999992e8,"bin_start":1.8666071823599994e8,"count":1},{"bin_end":2.204545248639999e8,"bin_start":2.0355762154999992e8,"count":1},{"bin_end":2.373514281779999e8,"bin_start":2.204545248639999e8,"count":0},{"bin_end":2.542483314919999e8,"bin_start":2.373514281779999e8,"count":0},{"bin_end":2.711452348059999e8,"bin_start":2.542483314919999e8,"count":0},{"bin_end":2.880421381199999e8,"bin_start":2.711452348059999e8,"count":4}],"max":"288042138.1199999","min":"119073104.97999996","nan_count":0,"unique_count":15}},{"dtype":"int64","name":"_deepnote_index_column"}],"row_count":15,"rows":[{"_deepnote_index_column":0,"unidade":"FACULDADE DE MEDICINA","valor":2.880421381199999e8},{"_deepnote_index_column":1,"unidade":"FACULDADE DE  CIÊNCIAS DA SAÚDE","valor":2.8527549522999996e8},{"_deepnote_index_column":2,"unidade":"FACULDADE ECONOMIA,  ADMINISTRAÇÃO,  CONTABILIDADE E  GEST POL PÚBLICAS","valor":2.8428858157e8},{"_deepnote_index_column":3,"unidade":"FACULDADE DE PLANALTINA","valor":2.7680438811e8},{"_deepnote_index_column":4,"unidade":"FACULDADE DE CEILÂNDIA","valor":2.1976023474999997e8},{"_deepnote_index_column":5,"unidade":"FACULDADE DE EDUCAÇÃO FÍSICA","valor":1.9028914580000004e8},{"_deepnote_index_column":6,"unidade":"FACULDADE DE  TECNOLOGIA","valor":1.779024615699998e8},{"_deepnote_index_column":7,"unidade":"INSTITUTO DE GEOCIÊNCIAS","valor":1.7772421498999998e8},{"_deepnote_index_column":8,"unidade":"INSTITUTO DE CIÊNCIAS BIOLÓGICAS","valor":1.5712954337999997e8},{"_deepnote_index_column":9,"unidade":"INSTITUTO DE LETRAS","valor":1.4986454501999995e8}]}
```
:::
:::

::: {.cell .markdown cell_id="bc5eabf0fb474ec982150816609a6720" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
As Faculdades de Medicina, Ciências da Saúde e a FACE, lideram o top 3
do Hanking. Dados interessantes que podem ser notados, são que as
faculdades inteiras de Planaltina e Ceilândia gastaram menos que as
lideres do hacking.
:::

::: {.cell .markdown cell_id="ad2d7149b95243609c07696ae4d656eb" deepnote_cell_type="text-cell-h1" formattedRanges="[]"}
# 1.3 - Analisando as Engenharias, qual delas consumiu maior verba? {#13---analisando-as-engenharias-qual-delas-consumiu-maior-verba}
:::

::: {.cell .code execution_count="6" cell_id="963edaeca8e74feb879a1763afd04658" deepnote_cell_type="code" deepnote_table_loading="false" deepnote_table_state="{\"filters\":[],\"pageIndex\":1,\"pageSize\":10,\"sortBy\":[]}" deepnote_to_be_reexecuted="false" execution_millis="236" execution_start="1689382708969" source_hash="9ebd81be"}
``` python
filtro = (df_gastos['unidade'].str.contains('ENGENHARIA', regex=True))
df_engenharias = df_gastos[filtro].groupby(['unidade'], as_index=False)['valor'].sum()
df_engenharias.sort_values(by=['valor'],ascending=False).reset_index(drop=True).head(15)
```

::: {.output .execute_result execution_count="6"}
``` json
{"column_count":2,"columns":[{"dtype":"object","name":"unidade","stats":{"categories":[{"count":1,"name":"DEPTO ENGENHARIA ELETRICA"},{"count":1,"name":"PROGRAMA DE PÓS-GRADUAÇÃO EM ENGENHARIA ELÉTRICA"},{"count":7,"name":"7 others"}],"nan_count":0,"unique_count":9}},{"dtype":"float64","name":"valor","stats":{"histogram":[{"bin_end":6054136.539999999,"bin_start":57380,"count":8},{"bin_end":1.2050893079999998e7,"bin_start":6054136.539999999,"count":0},{"bin_end":1.8047649619999997e7,"bin_start":1.2050893079999998e7,"count":0},{"bin_end":2.4044406159999996e7,"bin_start":1.8047649619999997e7,"count":0},{"bin_end":3.0041162699999996e7,"bin_start":2.4044406159999996e7,"count":0},{"bin_end":3.6037919239999995e7,"bin_start":3.0041162699999996e7,"count":0},{"bin_end":4.2034675779999994e7,"bin_start":3.6037919239999995e7,"count":0},{"bin_end":4.803143231999999e7,"bin_start":4.2034675779999994e7,"count":0},{"bin_end":5.402818885999999e7,"bin_start":4.803143231999999e7,"count":0},{"bin_end":6.002494539999999e7,"bin_start":5.402818885999999e7,"count":1}],"max":"60024945.39999999","min":"57380.0","nan_count":0,"unique_count":9}},{"dtype":"int64","name":"_deepnote_index_column"}],"row_count":9,"rows":[{"_deepnote_index_column":0,"unidade":"DEPTO ENGENHARIA ELETRICA","valor":6.002494539999999e7},{"_deepnote_index_column":1,"unidade":"PROGRAMA DE PÓS-GRADUAÇÃO EM ENGENHARIA ELÉTRICA","valor":2462806.55},{"_deepnote_index_column":2,"unidade":"DEPARTAMENTO DE ENGENHARIA MECANICA","valor":891230.6200000001},{"_deepnote_index_column":3,"unidade":"PROGRAMA DE PÓS-GRADUAÇÃO EM ENGENHARIA BIOMÉDICA","valor":757004.68},{"_deepnote_index_column":4,"unidade":"DEPTO ENGENHARIA DE PRODUCAO","valor":433536.9199999999},{"_deepnote_index_column":5,"unidade":"DEPTO ENGENHARIA CIVIL E AMBIENTAL","valor":322399.2},{"_deepnote_index_column":6,"unidade":"DEPTO ENGENHARIA FLORESTAL","valor":157410.84000000003},{"_deepnote_index_column":7,"unidade":"PROGRAMA DE PÓS-GRADUAÇÃO EM ENGENHARIA DE SISTEMAS ELETRÔNICOS E AUTOMAÇÃO","valor":120124.79999999997},{"_deepnote_index_column":8,"unidade":"PROGRAMA DE PÓS-GRADUAÇÃO EM INTEGRIDADE DE MATERIAIS DA ENGENHARIA","valor":57380}]}
```
:::
:::

::: {.cell .markdown cell_id="4bc47a79ef104c0c8b69bb84170060ad" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
O departamento de Engenharia Elétrica é o líder do hanking de gastos
nesse período.
:::

::: {.cell .markdown cell_id="7f796126820c4e57978a538113550a8c" deepnote_cell_type="text-cell-h1" formattedRanges="[]"}
# 1.4 - Previsão dos custos {#14---previsão-dos-custos}
:::

::: {.cell .markdown cell_id="f75d72a2dd3442069a1bb61411e4352c" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
O método que será utilizado para tentar prever os custo de 2023 com base
nos dados de 2021 e 2022 será a Regressão Linear, já que iremos avaliar
apenas o valor total dos gastos de cada mês disponibilizado.
:::

::: {.cell .code execution_count="31" cell_id="d52a1ffa3d2f481da778cda780acc266" deepnote_cell_type="code" deepnote_to_be_reexecuted="false" execution_millis="355" execution_start="1689383219227" source_hash="73e262a6"}
``` python
df_total = df_gastos.groupby(['periodo'], as_index=False)['valor'].sum()
plt.figure(figsize=(10,7))
plt.scatter(df_total.periodo, df_total.valor)
plt.show()
```

::: {.output .display_data}
![](https://github.com/diogoschwartz/IEEE-CIS_Trainee/blob/main/Semana%2001/UnB_em_dados-plot1png.png?raw=true){height="597"
width="835"}
:::
:::

::: {.cell .markdown cell_id="1ac37eef8add44318896a428270747b3" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Podemos perceber que existe uma discrepância bem grande entre os dados
de cada ano, sendo que em 2022 teve uma grande alta. Assim, percebe-se
que seriam necessárias mais informações para poder driblar essas mudança
brusca e analisar melhor os valores
:::

::: {.cell .markdown cell_id="87fd63f4184644b98085104d38051c78" deepnote_cell_type="text-cell-h1" formattedRanges="[]"}
# Parte 2 - Avaliação do uso de ferramentas de análise de dado e Machine Learning nesse dataset
:::

::: {.cell .markdown cell_id="18191d6e25b84078b00d4788af794c46" deepnote_cell_type="text-cell-h2" formattedRanges="[]"}
## 2.1 - Codificação One-Hot {#21---codificação-one-hot}
:::

::: {.cell .markdown cell_id="f420c746ea614daf97bffacb7457415d" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Como vimos, esse dataset apresenta um valor categóricos de extrema
importância, que é a natureza da despesa. Contudo para aplicar uma
estratégias de Machine Learning nesse dataset, precisaríamos que todos
os dados fossem numéricos, para isso seria preciso transformar esses
valores categóricos para um equivalente numérico.
:::

::: {.cell .markdown cell_id="a9605f31d01040f98f15283191961f64" deepnote_cell_type="text-cell-h2" formattedRanges="[]"}
## 2.2 - Clusterização {#22---clusterização}
:::

::: {.cell .markdown cell_id="06be472901884787a1de85e9b5506f27" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
A técnica de Clusterização permite agrupar e categorizar uma grande
quantidade de dados, com base em características semelhantes. Aplicar
essa abordagem seria muito interessante caso do dataset apresentasse
mais detalhadamente informações sobre as despesas, como as datas de
solicitação e execução, valor parcelado ou não, forma de pagamento,
entre outros. Assim, seria possível avaliar com mais detalhes quais são
as despesas que são menos atendidas pelo comissão orçamentária, quais
demoram mais para serem aprovadas, como são realizados os pagamentos,
etc\...
:::

::: {.cell .markdown cell_id="604e69dfc68b4abf975155cc470f5e18" deepnote_cell_type="text-cell-h2" formattedRanges="[]"}
## 2.3 - Conclusão {#23---conclusão}
:::

::: {.cell .markdown cell_id="83a70b44f5b44afc96fe6f0c80d51466" deepnote_cell_type="text-cell-p" formattedRanges="[]"}
Portanto, por meio das análises e perspectivas futuras, foi possível
perceber a necessidade de um quantitativo maior de dados para poder
extrair maiores informações sobre os gastos da UnB. Como perspectiva
futura, seria interessante o treinamento de uma rede neural para poder
analisar diariamente os gastos da UnB, com objetivo de poder auxiliar na
tomada de decisões e na avaliação de caminhos a serem seguido pelo
reitoria da universidade.
:::

::: {.cell .markdown created_in_deepnote_cell="true" deepnote_cell_type="markdown"}
`<a style='text-decoration:none;line-height:16px;display:flex;color:#5B5B62;padding:10px;justify-content:end;' href='https://deepnote.com?utm_source=created-in-deepnote-cell&projectId=5035e523-83e5-4212-bc1c-f484fb40f44e' target="_blank">`{=html}
`<img alt='Created in deepnote.com' style='display:inline;max-height:16px;margin:0px;margin-right:7.5px;' src='data:image/svg+xml;base64,PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4KPHN2ZyB3aWR0aD0iODBweCIgaGVpZ2h0PSI4MHB4IiB2aWV3Qm94PSIwIDAgODAgODAiIHZlcnNpb249IjEuMSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB4bWxuczp4bGluaz0iaHR0cDovL3d3dy53My5vcmcvMTk5OS94bGluayI+CiAgICA8IS0tIEdlbmVyYXRvcjogU2tldGNoIDU0LjEgKDc2NDkwKSAtIGh0dHBzOi8vc2tldGNoYXBwLmNvbSAtLT4KICAgIDx0aXRsZT5Hcm91cCAzPC90aXRsZT4KICAgIDxkZXNjPkNyZWF0ZWQgd2l0aCBTa2V0Y2guPC9kZXNjPgogICAgPGcgaWQ9IkxhbmRpbmciIHN0cm9rZT0ibm9uZSIgc3Ryb2tlLXdpZHRoPSIxIiBmaWxsPSJub25lIiBmaWxsLXJ1bGU9ImV2ZW5vZGQiPgogICAgICAgIDxnIGlkPSJBcnRib2FyZCIgdHJhbnNmb3JtPSJ0cmFuc2xhdGUoLTEyMzUuMDAwMDAwLCAtNzkuMDAwMDAwKSI+CiAgICAgICAgICAgIDxnIGlkPSJHcm91cC0zIiB0cmFuc2Zvcm09InRyYW5zbGF0ZSgxMjM1LjAwMDAwMCwgNzkuMDAwMDAwKSI+CiAgICAgICAgICAgICAgICA8cG9seWdvbiBpZD0iUGF0aC0yMCIgZmlsbD0iIzAyNjVCNCIgcG9pbnRzPSIyLjM3NjIzNzYyIDgwIDM4LjA0NzY2NjcgODAgNTcuODIxNzgyMiA3My44MDU3NTkyIDU3LjgyMTc4MjIgMzIuNzU5MjczOSAzOS4xNDAyMjc4IDMxLjY4MzE2ODMiPjwvcG9seWdvbj4KICAgICAgICAgICAgICAgIDxwYXRoIGQ9Ik0zNS4wMDc3MTgsODAgQzQyLjkwNjIwMDcsNzYuNDU0OTM1OCA0Ny41NjQ5MTY3LDcxLjU0MjI2NzEgNDguOTgzODY2LDY1LjI2MTk5MzkgQzUxLjExMjI4OTksNTUuODQxNTg0MiA0MS42NzcxNzk1LDQ5LjIxMjIyODQgMjUuNjIzOTg0Niw0OS4yMTIyMjg0IEMyNS40ODQ5Mjg5LDQ5LjEyNjg0NDggMjkuODI2MTI5Niw0My4yODM4MjQ4IDM4LjY0NzU4NjksMzEuNjgzMTY4MyBMNzIuODcxMjg3MSwzMi41NTQ0MjUgTDY1LjI4MDk3Myw2Ny42NzYzNDIxIEw1MS4xMTIyODk5LDc3LjM3NjE0NCBMMzUuMDA3NzE4LDgwIFoiIGlkPSJQYXRoLTIyIiBmaWxsPSIjMDAyODY4Ij48L3BhdGg+CiAgICAgICAgICAgICAgICA8cGF0aCBkPSJNMCwzNy43MzA0NDA1IEwyNy4xMTQ1MzcsMC4yNTcxMTE0MzYgQzYyLjM3MTUxMjMsLTEuOTkwNzE3MDEgODAsMTAuNTAwMzkyNyA4MCwzNy43MzA0NDA1IEM4MCw2NC45NjA0ODgyIDY0Ljc3NjUwMzgsNzkuMDUwMzQxNCAzNC4zMjk1MTEzLDgwIEM0Ny4wNTUzNDg5LDc3LjU2NzA4MDggNTMuNDE4MjY3Nyw3MC4zMTM2MTAzIDUzLjQxODI2NzcsNTguMjM5NTg4NSBDNTMuNDE4MjY3Nyw0MC4xMjg1NTU3IDM2LjMwMzk1NDQsMzcuNzMwNDQwNSAyNS4yMjc0MTcsMzcuNzMwNDQwNSBDMTcuODQzMDU4NiwzNy43MzA0NDA1IDkuNDMzOTE5NjYsMzcuNzMwNDQwNSAwLDM3LjczMDQ0MDUgWiIgaWQ9IlBhdGgtMTkiIGZpbGw9IiMzNzkzRUYiPjwvcGF0aD4KICAgICAgICAgICAgPC9nPgogICAgICAgIDwvZz4KICAgIDwvZz4KPC9zdmc+' >`{=html}
`</img>`{=html} Created in
`<span style='font-weight:600;margin-left:4px;'>`{=html}Deepnote`</span>`{=html}`</a>`{=html}
:::
