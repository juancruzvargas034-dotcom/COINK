import streamlit as st
import polars as pl
import matplotlib.pyplot as plt
st.title("Panel de Evaluación de Usuarios Coink 🐷")
# Cargue de datos
@st.cache_data
def load_data():
    # Leer archivo descargado
    df = pl.read_csv("depositos_oink.csv")
    return df
df = load_data()
# Cálculo de Métricas por Usuario
# Se asume que el CSV contiene columnas: user_id, oink_id, monto, fecha
user_metrics = df.group_by("user_id").agg([
    pl.col("monto").sum().alias("total_depositado"),
    pl.col("monto").count().alias("num_depositos"),
    pl.col("oink_id").n_unique().alias("oinks_diferentes")
])
st.subheader("Métricas Calculadas")
st.dataframe(user_metrics.head(10).to_pandas())

# Gráfica de Distribución
fig, ax = plt.subplots(figsize=(8, 4))
ax.hist(user_metrics["total_depositado"].to_list(), bins=20, color='#FF69B4', edgecolor='black')
ax.set_title("Distribución del Total Depositado por Usuario")
ax.set_xlabel("Monto ($)")
ax.set_ylabel("Número de Usuarios")
st.pyplot(fig)
