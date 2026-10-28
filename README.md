# QUE TEMPO FIXO?

# 1 Descripción del Proyecto y de la estación
- Nombre: A Coruna / Alvedro (Aeropuerto)
- Indicativo: 08002
- Coordenadas: 43.3000 N, -8.3833 W
- Altitud: 97 m

# 2 Fuente de los Datos
- Origen: Meteostat
- URL de descarga:https://raw.githubusercontent.com/bigdatawirtz/datasets/main/datasets/meteo_alvedro_2021_2025.csv
- Licencia:CC BY-NC 4.0
- Fecha de descarga:27 de octubre de 2026

# 3 Ejecución del proyecto

- 1 Clonar el repositorio y acceder a la carpeta:

```bash
git clone git@github.com:VA3N/que-tempo-fixo.git
cd que-tempo-fixo
```

- 2 Crear y activar el entorno virtual:

```bash
python3 -m venv venv
source venv/bin/activate
```

- 3 Instalar las dependencias de Python:

```bash
pip install -r requirements.txt
```

- 4 Levantar la base de datos PostgreSQL en Docker:

```bash
docker compose up -d
```

- 5 Ejecutar el pipeline:

Abrir y ejecutar todas las celdas del cuaderno notebooks/01_pipeline_meteo.ipynb seleccionando el entorno virtual.

# 4 Decisiones de Limpieza

| Nº | Problema Encontrado | Como se Detectó |  Solución Aplicada |
|---|---|---|---|
| 1 | Codificación ISO-8859-1 | Error de lectura al intentar abrir con UTF-8 | Lectura explícita con `encoding='latin1'` |
| 2 | Metadatos en la cabecera | Inspección visual de las primeras 6 líneas del CSV | Omitidas mediante el parámetro `skiprows=6` |
| 3 | Formato europeo (delimitador y decimal) | Separador `;` y decimales indicados con coma `,` | Parámetro `sep=';'` y reemplazo de `,` por `.` |
| 4 | Separador de miles en presión | Cadenas de texto formateadas como `1.015,7` | Eliminación del punto de miles antes de convertir a flotante |
| 5 | Lluvia inapreciable (`Ip`) | Presencia del texto `Ip` en la columna de precipitaciones | Reemplazo explícito por `0.0` mm |
| 6 | Valores sentinela `-9999` | Caídas anómalas en la exploración gráfica inicial | Conversión directa a nulos `NaN` mediante `na_values` |
| 7 | Fechas heterogéneas | Mezcla de formatos `YYYY-MM-DD` y `DD/MM/YYYY` | Conversión unificada con `pd.to_datetime(..., format='mixed')` |
| 8 | Registros duplicados | Fechas repetidas al final del archivo | Eliminación de duplicados con `drop_duplicates(subset=['data'])` |
| 9 | Outlier térmico imposible | Valor extremo de 31.3 °C en febrero de 2025 | Reemplazo por `NaN` e imputación con la media de mín/máx |
| 10 | Secuencia temporal discontinua | Detección de 27 días ausentes en la serie | Reindexado a 1826 días continuos e interpolación lineal |

# 5 Uso de la IA 
- Herramienta utilizada: Gemini
- Casos de uso:
He usado gemini para diferentes casos como para generación de código(con su debida explicación de código acompañando al script), 
problemas con la configuración del docker,ayuda con la selección de kernel(instalar las librerías necesarias y que no se instalaran, era debido a tener seleccionado el kernel equivocado) y creación del formato tabla que nos piden en "decisiones de limpieza".