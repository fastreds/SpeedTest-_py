📊 Dashboard de Rendimiento de Red con Google Sheets
Este proyecto es un dashboard web avanzado para monitorear, analizar y visualizar los resultados de pruebas de velocidad de internet. Utiliza una hoja de cálculo de Google Sheets como base de datos y un Google Apps Script como backend, lo que lo convierte en una solución potente y sin costo de servidor.

![Imagen del Dashboard de Rendimiento de Red]

✨ Características Principales
Análisis Comparativo Automático: Compara automáticamente el período de tiempo seleccionado con el período anterior equivalente, mostrando la variación porcentual (%) y una flecha de tendencia (▲ mejora / ▼ empeoramiento).

Indicadores Clave de Rendimiento (KPIs): Mide y compara las 4 métricas esenciales: Descarga Promedio, Estabilidad (Percentil 5), Ping Promedio y Jitter Promedio.

Visualizaciones de Datos Avanzadas:

Mapa de Calor: Identifica patrones de congestión mostrando la velocidad promedio para cada hora de cada día de la semana.

Gráfico de Consistencia: Analiza la estabilidad de la conexión visualizando el peor 5%, la mediana y el mejor 5% de las pruebas.

Gráficos Históricos: Compara la evolución de las métricas a lo largo del tiempo para múltiples dispositivos a la vez.

Filtrado Dinámico: Permite filtrar los datos por rangos de fecha predefinidos (últimas 12h, 7 días, etc.), rangos personalizados y por uno o varios dispositivos.

Interfaz Moderna y Responsiva:

Diseño limpio construido con Tailwind CSS.

Tema claro y oscuro que se adapta a las preferencias del sistema.

Secciones de ayuda colapsables para una experiencia de usuario limpia.

Backend Eficiente: El Google Apps Script está optimizado para procesar grandes cantidades de datos rápidamente, filtrando la información en el servidor antes de enviarla al cliente.

🚀 Guía de Instalación
Sigue estos pasos para configurar tu propio dashboard.

Paso 1: Configurar la Hoja de Cálculo de Google
Crea una nueva Hoja de Cálculo de Google.

Dale el nombre que prefieras. La primera hoja debería llamarse Hoja1.

Ve a Extensiones > Apps Script para abrir el editor de scripts.

Paso 2: Implementar el Google Apps Script (Backend)
Borra cualquier código de ejemplo en el editor de Apps Script.

Copia el contenido del archivo Code.gs (proporcionado en el proyecto) y pégalo en el editor.

¡Importante! Modifica la variable SECRET_TOKEN en la línea 7. Reemplaza "Delta1818!" por una contraseña secreta y segura que solo tú conozcas.

Guarda el proyecto de script (icono de disquete).

Haz clic en Implementar > Nueva implementación.

En la ventana de configuración:

Tipo: Selecciona Aplicación web.

Descripción: Añade una descripción (ej. "Backend para Dashboard de Velocidad").

Ejecutar como: Yo.

Quién tiene acceso: Cualquier usuario.

Haz clic en Implementar.

La primera vez, Google te pedirá que autorices los permisos. Sigue los pasos para permitir que el script se ejecute.

Una vez implementado, copia la URL de la aplicación web. La necesitarás en el siguiente paso.

Paso 3: Configurar el Dashboard (Frontend)
Abre el archivo dashboard.html en un editor de texto o código.

Busca la constante SHEET_URL (alrededor de la línea 400).

Reemplaza la URL de ejemplo con la URL de la aplicación web que copiaste en el paso anterior.

// URL de tu Apps Script
const SHEET_URL = "URL_QUE_COPIASTE_DE_APPS_SCRIPT";

Guarda el archivo dashboard.html. ¡Ya está listo para usarse! Simplemente ábrelo en tu navegador web.

Paso 4: Recolectar Datos de Velocidad
El dashboard solo visualiza los datos; necesitas un script que realice las pruebas de velocidad y las envíe a tu hoja de cálculo.

A continuación, se muestra un ejemplo de un script en Python que puedes ejecutar en un dispositivo (como una Raspberry Pi o tu propio PC) para automatizar este proceso.

Requisitos (Python):

pip install speedtest-cli requests

Script de Ejemplo (speed_logger.py):

import speedtest
import requests
import json
from datetime import datetime

# --- CONFIGURACIÓN ---
# La misma URL de la aplicación web de tu Apps Script
APPS_SCRIPT_URL = "URL_QUE_COPIASTE_DE_APPS_SCRIPT"

# El mismo token secreto que definiste en tu Apps Script
SECRET_TOKEN = "TU_TOKEN_SECRETO"

# Un nombre para identificar el dispositivo donde se ejecuta la prueba
DEVICE_NAME = "PC-Principal"
# --------------------

def run_speed_test():
    print(f"Ejecutando prueba de velocidad para el dispositivo: {DEVICE_NAME}...")
    try:
        st = speedtest.Speedtest()
        st.get_best_server() # Encuentra el mejor servidor para la prueba
        
        download_speed = st.download() / 1_000_000  # Convertir a Mbps
        upload_speed = st.upload() / 1_000_000    # Convertir a Mbps
        ping = st.results.ping
        jitter = st.results.server['latency'] - ping # Una aproximación simple del Jitter

        print(f"Descarga: {download_speed:.2f} Mbps")
        print(f"Subida: {upload_speed:.2f} Mbps")
        print(f"Ping: {ping:.2f} ms")
        print(f"Jitter: {jitter:.2f} ms")

        return {
            "download_mbps": round(download_speed, 2),
            "upload_mbps": round(upload_speed, 2),
            "ping_ms": round(ping, 2),
            "jitter_ms": round(jitter, 2) # Jitter se guarda también
        }
    except Exception as e:
        print(f"Error durante la prueba de velocidad: {e}")
        return None

def send_data_to_sheet(data):
    payload = {
        "secret": SECRET_TOKEN,
        "device": DEVICE_NAME,
        "timestamp": datetime.now().isoformat(),
        **data
    }
    
    headers = {
        "Content-Type": "application/json",
    }
    
    print("Enviando datos a Google Sheets...")
    try:
        response = requests.post(APPS_SCRIPT_URL, headers=headers, data=json.dumps(payload))
        response.raise_for_status() # Lanza un error si la respuesta es 4xx o 5xx
        print(f"Datos enviados con éxito: {response.json()}")
    except requests.exceptions.RequestException as e:
        print(f"Error al enviar los datos: {e}")

if __name__ == "__main__":
    test_results = run_speed_test()
    if test_results:
        send_data_to_sheet(test_results)

Puedes automatizar la ejecución de este script usando cron en Linux/macOS o el Programador de Tareas en Windows.

🛠️ Tecnologías Utilizadas
Frontend: HTML, Tailwind CSS, Chart.js

Backend: Google Apps Script

Base de Datos: Google Sheets

🤝 Contribuciones
Las contribuciones son bienvenidas. Si tienes ideas para mejorar el dashboard o encuentras un error, por favor abre un issue o envía un pull request.

📄 Licencia
Este proyecto está bajo la Licencia MIT.