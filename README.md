# Preço-do-Ouro
Análise da evolução do preço do ouro no Brasil
# Biblioteca para manipulação de dados
from pandas import concat, read_excel

# Biblioteca para criação de gráficos estáticos
import matplotlib.pyplot as plt

# Biblioteca para criação de gráficos dinâmicos e interativos
import plotly.express as px
import plotly.graph_objects as go  # Adicionado para trabalhar com anotações

# Cria uma lista vazia que vai armazenar DataFrames de diferentes anos
dfs = []

# Loop pelos anos de 2019 a 2024 (2025 não é incluído, pois range exclui o último número)
for ano in range(2019, 2025):
    
    # Lê o arquivo Excel 'Ouro.xlsx', selecionando a aba correspondente ao ano atual no loop
    df = read_excel("Ouro.xlsx", sheet_name=str(ano))
    
    # Adiciona o DataFrame lido para o ano atual à lista 'dfs'
    dfs.append(df)

# Concatena todos os DataFrames na lista 'dfs' em um único DataFrame
df = concat(dfs)

# Renomeia as colunas "Column1" para "Data" e "Column3" para "Preço" para facilitar o uso
df = df.rename(columns={
    "Column1": "Data",    # Renomeia "Column1" para "Data"
    "Column3": "Preço",   # Renomeia "Column3" para "Preço"
})

# Exibe o DataFrame concatenado com as colunas renomeadas
print(df)

# Exibe os tipos de dados de cada coluna do DataFrame, útil para verificar se os dados estão corretos
print(df.dtypes)

# Gráfico estático usando Matplotlib (como antes)
fig = plt.figure()
ax = fig.add_subplot()
ax.set_title("Grama do Ouro em BRL")
ax.plot(df["Data"], df["Preço"], color="gold")
ax.fill_between(df["Data"], df["Preço"], color="gold", alpha=0.5)
ax.set_xlim(df["Data"].iloc[0], df["Data"].iloc[-1])
ax.set_ylim(0, df["Preço"].max() * 1.1)
ax.grid(axis="y")
fig.tight_layout()
plt.show()

# Gráfico dinâmico usando Plotly Express
fig_interativo = px.line(
    df,  # DataFrame que contém os dados
    x="Data",  # Coluna a ser usada para o eixo x
    y="Preço",  # Coluna a ser usada para o eixo y
    title="Grama do Ouro em BRL (Gráfico Interativo)",  # Define o título do gráfico
    labels={"Data": "Data", "Preço": "Preço do Ouro (BRL)"},  # Define os rótulos dos eixos
    line_shape="linear"  # Define o formato da linha como linear
)

# Atualiza o layout do gráfico para ativar o modo de hover em qualquer posição no eixo x
fig_interativo.update_layout(
    hovermode="x unified",  # Mostra a linha de hover em qualquer posição no eixo x
    hoverlabel=dict(bgcolor="white", font_size=12, font_family="Arial")  # Ajusta o estilo do label do hover
)

# Atualiza a cor da linha do gráfico para dourada
fig_interativo.update_traces(line_color='gold')

# Adicionando destaques ao gráfico:
# Descobre o preço máximo e mínimo no DataFrame para destacar esses pontos
preco_maximo = df["Preço"].max()
preco_minimo = df["Preço"].min()

# Data correspondente ao preço máximo e mínimo
data_maxima = df[df["Preço"] == preco_maximo]["Data"].values[0]
data_minima = df[df["Preço"] == preco_minimo]["Data"].values[0]

# Adiciona anotações no gráfico para destacar o ponto máximo e mínimo
fig_interativo.add_trace(go.Scatter(
    x=[data_maxima], y=[preco_maximo],  # Coordenadas do ponto máximo
    text=[f"Máximo: {preco_maximo} BRL"],  # Texto da anotação
    mode="markers+text",  # Mostra marcadores e o texto
    textposition="top right",  # Posição do texto
    marker=dict(color="red", size=10, symbol="circle")  # Personalização do marcador
))

fig_interativo.add_trace(go.Scatter(
    x=[data_minima], y=[preco_minimo],  # Coordenadas do ponto mínimo
    text=[f"Mínimo: {preco_minimo} BRL"],  # Texto da anotação
    mode="markers+text",  # Mostra marcadores e o texto
    textposition="bottom right",  # Posição do texto
    marker=dict(color="blue", size=10, symbol="circle")  # Personalização do marcador
))

# Exibe o gráfico interativo com destaques
fig_interativo.show()
