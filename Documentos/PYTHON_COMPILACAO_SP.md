# 📄 Compilação de Dados – Importação e Exportação do Estado de São Paulo
**Data de criação:** 15/09/2025  
**Última atualização:** 10/11/2025  
**Autores:** Jackson Penaforte e Vitor Amaral  

---

```python
# Importa a biblioteca necessária
import pandas as pd

# Monta o Google Drive para acessar os arquivos
from google.colab import drive
drive.mount('/content/drive')

# Define o caminho da pasta onde estão os arquivos de dados
origem = '/content/drive/MyDrive/api_python/exportcao_importacao/'

# Define os caminhos dos arquivos CSV
arq_7 = origem + 'EXP_2023_MUN.csv'
arq_8 = origem + 'IMP_2023_MUN.csv'
arq_9 = origem + 'EXP_2024_MUN.csv'
arq_10 = origem + 'IMP_2024_MUN.csv'
arq_11 = origem + 'EXP_2025_MUN.csv'
arq_12 = origem + 'IMP_2025_MUN.csv'
ncm = origem + 'NCM.csv'
país = origem + 'PAIS.csv'
via = origem +'VIA.csv'
urf = origem + 'URF.csv'
uf_m = origem + 'UF_MUN.csv'
uf = origem + 'UF.csv'
sh = origem + 'NCM_SH.csv'

# Lê os arquivos de exportação e importação municipais
exp23m = pd.read_csv(arq_7, low_memory=False, sep=';', encoding='UTF-8')
imp23m = pd.read_csv(arq_8, low_memory=False, sep=';', encoding='UTF-8')
exp24m = pd.read_csv(arq_9, low_memory=False, sep=';', encoding='UTF-8')
imp24m = pd.read_csv(arq_10, low_memory=False, sep=';', encoding='UTF-8')
exp25m = pd.read_csv(arq_11, low_memory=False, sep=';', encoding='UTF-8')
imp25m = pd.read_csv(arq_12, low_memory=False, sep=';', encoding='UTF-8')

# Lê tabelas auxiliares de referência
NCM = pd.read_csv(ncm, low_memory=False, sep=';', encoding='latin1')
PAÍS = pd.read_csv(país, low_memory=False, sep=';', encoding='latin1')
VIA = pd.read_csv(via, low_memory=False, sep=';', encoding='latin1')
URF = pd.read_csv(urf, low_memory=False, sep=';', encoding='latin1')
UF_MUN = pd.read_csv(uf_m, low_memory=False, sep=';', encoding='latin1')
UF = pd.read_csv(uf, low_memory=False, sep=';', encoding='latin1')
SH = pd.read_csv(sh, low_memory=False, sep=';', encoding='latin1')

# Remove linhas duplicadas em tabelas auxiliares
PAÍS = PAÍS[['CO_PAIS','NO_PAIS_ING']].drop_duplicates(subset='CO_PAIS')
UF_MUN = UF_MUN[['CO_MUN_GEO','NO_MUN']].drop_duplicates(subset='CO_MUN_GEO')
SH = SH[['CO_SH4','NO_SH4_POR']].drop_duplicates(subset='CO_SH4')

# Une dados de exportação de 2023 a 2025
finalexpm = pd.concat([exp23m, exp24m, exp25m], ignore_index=True)

# Mescla informações de país, município e classificação SH
finalexpm = finalexpm.merge(PAÍS[['CO_PAIS','NO_PAIS_ING']], on='CO_PAIS', how='left')
finalexpm = finalexpm.merge(UF_MUN[['CO_MUN_GEO','NO_MUN']], left_on='CO_MUN', right_on='CO_MUN_GEO', how='left')
finalexpm = finalexpm.merge(SH[['CO_SH4','NO_SH4_POR']], left_on='SH4', right_on='CO_SH4', how='left')

# Une dados de importação de 2023 a 2025
finalimpm = pd.concat([imp23m, imp24m, imp25m], ignore_index=True)

# Mescla informações de país, município e classificação SH
finalimpm = finalimpm.merge(PAÍS[['CO_PAIS','NO_PAIS_ING']], on='CO_PAIS', how='left')
finalimpm = finalimpm.merge(UF_MUN[['CO_MUN_GEO','NO_MUN']], left_on='CO_MUN', right_on='CO_MUN_GEO', how='left')
finalimpm = finalimpm.merge(SH[['CO_SH4','NO_SH4_POR']], left_on='SH4', right_on='CO_SH4', how='left')

# Filtra dados apenas do estado de São Paulo (SP)
finalimpSP = finalimpm[finalimpm['SG_UF_MUN'] == 'SP']
finalexpSP = finalexpm[finalexpm['SG_UF_MUN'] == 'SP']

# Visualiza as últimas linhas dos dados tratados
finalexpSP.tail(5)
finalimpSP.tail(5)

# Exporta os arquivos finais limpos e filtrados por SP
finalimpSP.to_csv(origem + 'finalimpSP.csv', index=False)
finalexpSP.to_csv(origem + 'finalexpSP.csv', index=False)
```
