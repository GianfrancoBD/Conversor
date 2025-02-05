import streamlit as st
import requests
import pandas as pd

# Función para obtener tasas de cambio desde la API
def obtener_tasas():
    url = "https://v6.exchangerate-api.com/v6/tu_api_key/latest/USD"
    response = requests.get(url)
    data = response.json()
    return data['conversion_rates']

# Función para convertir moneda
def convertir_moneda(valor, moneda_origen, moneda_destino, tasas):
    valor_convertido = valor * (tasas[moneda_destino] / tasas[moneda_origen])
    return valor_convertido

# Interfaz de usuario
st.title("Conversor de Monedas")

# Obtener las tasas de cambio
tasas = obtener_tasas()

# Selección de monedas
monedas = list(tasas.keys())
moneda_origen = st.selectbox("Selecciona la moneda de origen", monedas)
moneda_destino = st.selectbox("Selecciona la moneda de destino", monedas)

# Ingreso del valor a convertir
valor = st.number_input(f"Introduce la cantidad en {moneda_origen}", min_value=0.01, step=0.01)

if valor:
    valor_convertido = convertir_moneda(valor, moneda_origen, moneda_destino, tasas)
    st.write(f"{valor} {moneda_origen} es igual a {valor_convertido:.2f} {moneda_destino}")
import streamlit as st
import pandas as pd

# Crear un archivo de usuarios
def cargar_usuarios():
    try:
        return pd.read_csv("usuarios.csv")
    except FileNotFoundError:
        return pd.DataFrame(columns=["usuario", "contraseña"])

def registrar_usuario(usuarios, usuario, contraseña):
    if usuario not in usuarios["usuario"].values:
        usuarios = usuarios.append({"usuario": usuario, "contraseña": contraseña}, ignore_index=True)
        usuarios.to_csv("usuarios.csv", index=False)
        st.success("Registro exitoso.")
    else:
        st.warning("El usuario ya existe.")

def iniciar_sesion(usuarios, usuario, contraseña):
    if usuario in usuarios["usuario"].values and usuarios[usuarios["usuario"] == usuario]["contraseña"].values[0] == contraseña:
        st.session_state.usuario = usuario
        st.success(f"Bienvenido {usuario}")
    else:
        st.error("Credenciales incorrectas")

# Interfaz de usuario
st.title("Conversor de Monedas")

if "usuario" in st.session_state:
    st.write(f"Hola, {st.session_state.usuario}")
    # Aquí puedes poner el resto de la funcionalidad del conversor de monedas
else:
    # Formulario de inicio de sesión
    menu = ["Iniciar sesión", "Registrarse"]
    seleccion = st.selectbox("Selecciona una opción", menu)
    
    if seleccion == "Iniciar sesión":
        usuario = st.text_input("Usuario")
        contraseña = st.text_input("Contraseña", type="password")
        
        if st.button("Iniciar sesión"):
            usuarios = cargar_usuarios()
            iniciar_sesion(usuarios, usuario, contraseña)
    
    if seleccion == "Registrarse":
        usuario = st.text_input("Usuario")
        contraseña = st.text_input("Contraseña", type="password")
        
        if st.button("Registrarse"):
            usuarios = cargar_usuarios()
            registrar_usuario(usuarios, usuario, contraseña)
