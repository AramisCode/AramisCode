## Hi there 👋

# Instalar bibliotecas
!pip install pandas matplotlib seaborn

# Importar tudo o que iremos usar

import pandas as pd
import numpy as py
import plotly.express as px

# Importar Arquivo
from google.colab import drive
drive.mount('/content/drive')

# Caminho do arquivo
caminho = '/content/drive/MyDrive/AI_Tools_List.csv'

# Carregar
df = pd.read_csv(caminho, encoding='latin1')

# Padronizar nomes de colunas pra facilitação de acesso ao conteúdo
df.columns = df.columns.str.strip().str.replace(' ', '_').str.replace('[^a-zA-Z0-9_]', '')

# Checagem dos nomes de todas as colunas
print(df.columns.tolist())

df.columns

#GRÁFICO DE POPULARIDADE
# Se preferível, converter ano e popularidade para string e tratá-los como categorias com astype(str)
df['Release_Year'] = df['Release_Year'].astype(str)

fig = px.parallel_categories(
    df,
    dimensions=['AI_Name', 'Developer/Company', 'AI_Type', 'Main_Use_Case', 'Release_Year'], #aqui escolher quais colunas quer que ele pegue pra formar os gráficos
    color='Popularity_Score_(1-10)',
    color_continuous_scale=px.colors.sequential.Inferno,
    labels={
        'AI_Name': 'Ferramenta',
        'Developer/Company': 'Empresa',
        'AI_Type': 'Tipo de IA',
        'Main_Use_Case': 'Uso Principal',
        'Release_Year': 'Ano de Lançamento',
        'Popularity_Score_(1-10)': 'Popularidade'
    },
    title='📊 Categorias Paralelas: Ferramentas de IA'
)

fig.show()



df = df.sort_values(by='Popularity_Score_(1-10)', ascending=True)

fig = px.bar(df,
             x='Popularity_Score_(1-10)',
             y='AI_Name',
             orientation='h',
             title='📊 Popularidade das Ferramentas de IA (Ordem Crescente)',
             labels={'Popularidade': 'Pontuação de Popularidade', 'AI_Name': 'Ferramenta de IA'},
             color='Popularity_Score_(1-10)',
                 color_continuous_scale=px.colors.sequential.Inferno,
)

# Layout refinado
fig.update_layout(
    yaxis=dict(categoryorder='total ascending'),
    margin=dict(t=60, l=100, r=40, b=40),
    bargap=0.2
)

fig.show()


#GRÁFICO DE VALOR ESTIMADO EM BILHÕES (lucros estimados, impacto no mercado mundial)

df_valor = df.sort_values(by='Estimated_Valuation_Billion_USD')

plt.figure(figsize=(14,8))
sns.barplot(data=df_valor, x='Estimated_Valuation_Billion_USD', y='AI_Name', palette='flare')
plt.title("Ranking de Valuation das IAs em Bilhões e Seu Impacto Mundial")
plt.xlabel("Valuation (Bilhões USD)")
plt.ylabel("IA")
plt.tight_layout()
plt.show()


# Contar categorias
categorias = df['Main_Use_Case'].value_counts().reset_index()
categorias.columns = ['Categoria', 'Quantidade']

# Gráfico interativo
fig = px.pie(categorias, names='Categoria', values='Quantidade',
             title='Distribuição das Casos de Uso Principais das IAs',
             color_discrete_sequence=px.colors.sequential.RdBu)

fig.update_traces(textposition='inside', textinfo='percent+label')
fig.show()



#GRÁFICO DE CRESCIMENTO DE TIPOS DE FERRAMENTAS DE IA POR ANO
# Agrupar por ano e tipo
crescimento = df.groupby(['Release_Year', 'AI_Type']).size().reset_index(name='Quantidade')

# Gráfico interativo com paleta Viridis
fig = px.bar(crescimento,
             x='Release_Year',
             y='Quantidade',
             color='AI_Type',
             title='📊 Crescimento de Tipos de Ferramentas de IA por Ano',
             labels={'Release_Year': 'Ano de Lançamento', 'Quantidade': 'Quantidade de Ferramentas', 'AI_Type': 'Tipo de IA'},
             color_discrete_sequence=px.colors.sequential.Viridis)

# Layout refinado
fig.update_layout(
    xaxis=dict(type='category'),
    yaxis=dict(title='Quantidade de Ferramentas'),
    legend_title_text='Tipo de IA',
    margin=dict(t=60, l=40, r=40, b=40),
    bargap=0.2
)

fig.show()
