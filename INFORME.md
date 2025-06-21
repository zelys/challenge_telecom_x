# 📘 **Introducción**

Telecom X, una empresa del sector de telecomunicaciones, enfrenta un desafío crítico: una **alta tasa de cancelación de servicios por parte de sus clientes**, también conocida como evasión o churn. Este fenómeno tiene un impacto directo en los ingresos recurrentes de la compañía, eleva los costos de adquisición de nuevos usuarios y reduce la estabilidad financiera a largo plazo.

Con el objetivo de **entender las causas del abandono de clientes**, se desarrolló un análisis exploratorio de datos utilizando Python y sus principales bibliotecas para ciencia de datos (pandas, numpy, matplotlib, seaborn). Este estudio busca responder preguntas como:

- ¿Qué características comparten los clientes que se evaden?
- ¿Existen patrones o comportamientos comunes en términos de facturación, permanencia o perfil del cliente?
- ¿Qué segmentos presentan mayor riesgo de cancelación?

Al identificar los factores asociados con la evasión, Telecom X podrá **diseñar estrategias proactivas de retención**, mejorar sus servicios y personalizar sus campañas de fidelización.

# 🧹 **Limpieza y Tratamiento de Datos**

Con el objetivo de preparar los datos para su análisis, se siguió un proceso de ETL (Extracción, Transformación y Carga) que incluyó la importación, exploración, normalización, limpieza y estandarización de los datos entregados por Telecom X.

## 🔽 **Extracción de datos**

Se importó el conjunto de datos en formato JSON desde una fuente en línea y se cargó en un DataFrame de pandas. Los datos contenían información estructurada en campos anidados tipo diccionario (JSON string) para atributos como datos del cliente, teléfono, servicios contratados y detalles de facturación.

**Primeros 5 registros del DataFrame**:

| index | customerID | Churn | customer                                                                                       | phone                                             | internet                                                                                                                                                                            | account                                                                                                                                                |
| ----- | ---------- | ----- | ---------------------------------------------------------------------------------------------- | ------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 0     | 0002-ORFBO | No    | \{'gender': 'Female', 'SeniorCitizen': 0, 'Partner': 'Yes', 'Dependents': 'Yes', 'tenure': 9\} | \{'PhoneService': 'Yes', 'MultipleLines': 'No'\}  | \{'InternetService': 'DSL', 'OnlineSecurity': 'No', 'OnlineBackup': 'Yes', 'DeviceProtection': 'No', 'TechSupport': 'Yes', 'StreamingTV': 'Yes', 'StreamingMovies': 'No'\}          | \{'Contract': 'One year', 'PaperlessBilling': 'Yes', 'PaymentMethod': 'Mailed check', 'Charges': \{'Monthly': 65\.6, 'Total': '593\.3'\}\}             |
| 1     | 0003-MKNFE | No    | \{'gender': 'Male', 'SeniorCitizen': 0, 'Partner': 'No', 'Dependents': 'No', 'tenure': 9\}     | \{'PhoneService': 'Yes', 'MultipleLines': 'Yes'\} | \{'InternetService': 'DSL', 'OnlineSecurity': 'No', 'OnlineBackup': 'No', 'DeviceProtection': 'No', 'TechSupport': 'No', 'StreamingTV': 'No', 'StreamingMovies': 'Yes'\}            | \{'Contract': 'Month-to-month', 'PaperlessBilling': 'No', 'PaymentMethod': 'Mailed check', 'Charges': \{'Monthly': 59\.9, 'Total': '542\.4'\}\}        |
| 2     | 0004-TLHLJ | Yes   | \{'gender': 'Male', 'SeniorCitizen': 0, 'Partner': 'No', 'Dependents': 'No', 'tenure': 4\}     | \{'PhoneService': 'Yes', 'MultipleLines': 'No'\}  | \{'InternetService': 'Fiber optic', 'OnlineSecurity': 'No', 'OnlineBackup': 'No', 'DeviceProtection': 'Yes', 'TechSupport': 'No', 'StreamingTV': 'No', 'StreamingMovies': 'No'\}    | \{'Contract': 'Month-to-month', 'PaperlessBilling': 'Yes', 'PaymentMethod': 'Electronic check', 'Charges': \{'Monthly': 73\.9, 'Total': '280\.85'\}\}  |
| 3     | 0011-IGKFF | Yes   | \{'gender': 'Male', 'SeniorCitizen': 1, 'Partner': 'Yes', 'Dependents': 'No', 'tenure': 13\}   | \{'PhoneService': 'Yes', 'MultipleLines': 'No'\}  | \{'InternetService': 'Fiber optic', 'OnlineSecurity': 'No', 'OnlineBackup': 'Yes', 'DeviceProtection': 'Yes', 'TechSupport': 'No', 'StreamingTV': 'Yes', 'StreamingMovies': 'Yes'\} | \{'Contract': 'Month-to-month', 'PaperlessBilling': 'Yes', 'PaymentMethod': 'Electronic check', 'Charges': \{'Monthly': 98\.0, 'Total': '1237\.85'\}\} |
| 4     | 0013-EXCHZ | Yes   | \{'gender': 'Female', 'SeniorCitizen': 1, 'Partner': 'Yes', 'Dependents': 'No', 'tenure': 3\}  | \{'PhoneService': 'Yes', 'MultipleLines': 'No'\}  | \{'InternetService': 'Fiber optic', 'OnlineSecurity': 'No', 'OnlineBackup': 'No', 'DeviceProtection': 'No', 'TechSupport': 'Yes', 'StreamingTV': 'Yes', 'StreamingMovies': 'No'\}   | \{'Contract': 'Month-to-month', 'PaperlessBilling': 'Yes', 'PaymentMethod': 'Mailed check', 'Charges': \{'Monthly': 83\.9, 'Total': '267\.4'\}\}       |

## 🔄 **Transformación inicial**

Se identificó que varias columnas (`'customer', 'phone', 'internet', 'account'`) almacenaban información anidada. Estas columnas fueron normalizadas utilizando `pd.json_normalize()` dentro de un bucle, lo que permitió descomponerlas en múltiples columnas con datos atómicos.

Luego, se eliminaron las columnas originales anidadas y se conservaron únicamente las columnas desglosadas.

## ✅ **Verificación y corrección de inconsistencias**

Se realizó una auditoría de calidad de datos que reveló:

- La columna `'Charges.Total'` tenía valores vacíos y estaba en formato object, por lo que se convirtió a `float64` reemplazando valores nulos por 0.
- La columna `'SeniorCitizen'` se transformó correctamente a tipo booleano (True / False).
- Se descartaron columnas irrelevantes como `'customerID'`.

Además, se agregó una nueva variable `'Cargo Diario'`, estimada dividiendo la facturación mensual entre 30 días, para proporcionar un indicador más granular del comportamiento de pago.

## 🧪 **Estandarización de valores**

Varias columnas contenían respuestas tipo "Yes" / "No", las cuales se transformaron a valores booleanos (True / False). Esto facilitó el análisis numérico y la construcción de visualizaciones más precisas.

También se tradujeron al español los nombres de las columnas y los valores categóricos para mejorar la comprensión del análisis y facilitar su interpretación por parte del equipo de negocio.

## 🧾 **Resultado final**

El _DataFrame_ final contiene **7267 registros** con columnas relevantes y limpias, listas para ser utilizadas en el análisis exploratorio. Las variables clave como `'Evasión'`, `'Permanencia'`, `'Cargo Total'`, `'Tipo Contrato'` y `'Método de Pago'` fueron validadas, transformadas y estandarizadas correctamente.

Además, el conjunto de datos fue exportado como archivo `.csv` bajo el nombre `Datos_TelecomX.csv`, listo para su uso en procesos posteriores.

**Primeros 5 registros del DataFrame Final**:

| index | Evasión | Género    | Adulto Mayor | Tiene Pareja | Dependiente | Permanencia | Servicio Telefónico | Líneas Múltiples | Servicio Internet | Seguridad en Línea | Respaldo en Linea | Protección Dispositivo | Soporte Técnico | Streaming de TV | Streaming de Películas | Tipo Contrato | Facturación Electronica | Método de Pago     | Cargo Mensual | Cargo Total |
| ----- | ------- | --------- | ------------ | ------------ | ----------- | ----------- | ------------------- | ---------------- | ----------------- | ------------------ | ----------------- | ---------------------- | --------------- | --------------- | ---------------------- | ------------- | ----------------------- | ------------------ | ------------- | ----------- |
| 0     | false   | Femenino  | false        | true         | true        | 9           | true                | false            | DSL               | false              | true              | false                  | true            | true            | false                  | Un año        | true                    | Cheque por correo  | 65\.6         | 593\.3      |
| 1     | false   | Masculino | false        | false        | false       | 9           | true                | true             | DSL               | false              | false             | false                  | false           | false           | true                   | Mes-a-Mes     | false                   | Cheque por correo  | 59\.9         | 542\.4      |
| 2     | true    | Masculino | false        | false        | false       | 4           | true                | false            | Fibra óptica      | false              | false             | true                   | false           | false           | false                  | Mes-a-Mes     | true                    | Cheque electrónico | 73\.9         | 280\.85     |
| 3     | true    | Masculino | true         | true         | false       | 13          | true                | false            | Fibra óptica      | false              | true              | true                   | false           | true            | true                   | Mes-a-Mes     | true                    | Cheque electrónico | 98\.0         | 1237\.85    |
| 4     | true    | Femenino  | true         | true         | false       | 3           | true                | false            | Fibra óptica      | false              | false             | false                  | true            | true            | false                  | Mes-a-Mes     | true                    | Cheque por correo  | 83\.9         | 267\.4      |

# 📊 **Análisis Exploratorio de Datos (EDA)**

Con el conjunto de datos limpio y estandarizado, se procedió a realizar un análisis exploratorio para comprender mejor los patrones de comportamiento de los clientes, especialmente en relación con la evasión del servicio. Se utilizaron visualizaciones para detectar tendencias, relaciones y características clave asociadas con los clientes que abandonan Telecom X.

## 📉 **Distribución general de evasión**

Se analizaron las proporciones generales de clientes que permanecen y los que se evaden.

- **Gráfico de barras**: muestra el número absoluto de clientes en cada categoría (Permanece vs Abandona).

<img src="https://drive.google.com/uc?id=1KzJ45XEXdqrTqbMjsDbDaSIsen38l8TP&" alt="evasión" width="400">

- **Gráfico de torta**: permite observar la proporción relativa de evasión.

<img src="https://drive.google.com/uc?id=1tkup42KP9QrwmLVvuWxtx_eGBRZYsiCZ" alt="proporción evasión" width="400">

**Resultado**: Aproximadamente 25.7% de los clientes han abandonado el servicio, mientras que 74.3% se mantienen activos.

## 📦 **Evasión según permanencia**

Se utilizó un boxplot para evaluar la relación entre el tiempo de permanencia (en meses) y la evasión.

<img src="https://drive.google.com/uc?id=1ccmj5KHrV9QMoBvEH2w14vQKVyeE2A7W" alt="evasión según permanencia" width="500">

**Se observó que**:

- Los clientes que abandonan tienden a tener menor tiempo de permanencia.
- Los clientes con contratos más prolongados tienden a mantenerse, lo que sugiere una correlación entre fidelidad y permanencia.

## 🧑‍🤝‍🧑 **Evasión según variables categóricas**

Se analizaron diversas variables categóricas para identificar patrones en la evasión. Se utilizaron gráficos de barras agrupadas para comparar las tasas de evasión en distintas categorías:

**Género**: No se evidencian diferencias significativas entre hombres y mujeres.

<img src="https://drive.google.com/uc?id=12sE2RH6eYO9n_twPPrdCIPy71rsOzbPB" alt="evasión según género" width="700">

**Tipo de contrato**: Los clientes con contrato mensual (Mes-a-Mes) presentan una tasa mucho mayor de evasión, mientras que los contratos de uno o dos años se asocian con una mayor retención.

<img src="https://drive.google.com/uc?id=1Hr8UcbAmzj94gjrmFV-b5325iHeMKcO0" alt="evasón según contrato" width="700">

**Dependencia laboral**: Los clientes independientes (sin dependientes) tienen una mayor probabilidad de evasión.

<img src="https://drive.google.com/uc?id=1JfSbEoz-fyHGeA-8n1IglAIIRa6NtiKq" alt="evasión según dependencia" width="700">

## 💳 **Evasión según método de pago**

Dado que hay varias categorías de métodos de pago, se utilizó un gráfico de barras horizontales para observar mejor la variación:

<img src="https://drive.google.com/uc?id=10Hrv6h9ogFwVl6p2KILJWqzYVXz51HcW" alt="evasión según método de pago" width="800">

- Los clientes que utilizan cheque electrónico tienen la mayor tasa de evasión.
- En contraste, quienes usan tarjeta de crédito automática presentan una menor probabilidad de abandonar el servicio.

Esta diferencia sugiere que los métodos de pago automáticos podrían estar relacionados con una mayor fidelidad.

## 💵 **Evasión según variables numéricas**

Finalmente, se exploró la variable Cargo Total mediante un boxplot:

<img src="https://drive.google.com/uc?id=1IMJqPAldJt6A4ilHtNdKnxMaSVVsGx-3" alt="evasión según cargo total" width="600">

Los clientes que abandonan el servicio tienen, en general, menores cargos acumulados, lo que concuerda con la observación previa de menor tiempo de permanencia.

## ✅ Conclusiones e Insights

El análisis realizado sobre el comportamiento de los clientes de Telecom X reveló hallazgos clave que explican parte del problema de evasión. A continuación, se destacan los principales:

1. **Alta tasa de evasión**: Aproximadamente **1 de cada 4 clientes** abandona el servicio, lo que representa un desafío significativo para la empresa.
1. **Menor permanencia, mayor evasión**: Los clientes que cancelan tienden a haber estado suscritos por menos tiempo. Existe una clara correlación entre **baja permanencia y alta probabilidad de evasión**.
1. **Contratos mensuales están más expuestos**: Los clientes con contratos de tipo "**Mes-a-Mes**" presentan una **tasa de evasión sustancialmente mayor**, comparado con aquellos con contratos anuales o bianuales.
1. **Métodos de pago asociados a abandono**: Se identificó que clientes que utilizan **cheque electrónico o por correo** tienen una tasa más alta de evasión. En contraste, los métodos automáticos (tarjeta o transferencia) están asociados con mayor retención.
1. **Perfil demográfico y servicios**: No se encontraron diferencias notables en evasión por género, pero sí por **situación familiar**: quienes no **tienen pareja o dependientes** tienden a cancelar con mayor frecuencia. También se observó que ciertos servicios opcionales como **soporte técnico** o **respaldo en línea** pueden influir positivamente en la retención.

## 🎯 **Recomendaciones Estratégicas**

1. **Fomentar contratos de largo plazo**
   - Implementar incentivos o beneficios especiales para quienes opten por contratos anuales o bianuales, ya que estos están claramente asociados con menor evasión.
1. **Promover métodos de pago automáticos**
   - Otorgar descuentos o recompensas a clientes que se cambien a **tarjeta de crédito** o **transferencia automática**.
   - Simplificar el proceso de migración de método de pago.
1. **Detectar clientes en riesgo temprano**
   - Crear alertas para clientes con menos de 6 meses de permanencia o con cargos bajos acumulados, ya que estos perfiles son más propensos a abandonar.
1. **Optimizar servicios complementarios**
   - Aumentar la visibilidad y valor percibido de servicios como **respaldo en línea**, **soporte técnico** y **protección de dispositivos**, que se relacionan con mayor retención.
1. **Segmentar y personalizar la comunicación**
   - Usar los hallazgos del perfil de evasión para diseñar campañas dirigidas según tipo de cliente, contrato y método de pago.
1. **Explorar encuestas de salida**
   - Implementar encuestas automatizadas a clientes que cancelan para identificar razones específicas y validar los patrones observados en los datos.

# 📌 **¡Extra! Análisis de correlación entre variables**

## 📊 **1. Matriz de correlación**

<img src="https://drive.google.com/uc?id=1viRn9cqmJRLanY1CuBjKc9tiNZLQzOyX" alt="correlación evasión" width="600">

## 🔍 **Insights observados**

- `Tipo Contrato (Mes-a-Mes)` y `Método de Pago (Cheque electrónico)` tienen fuerte correlación positiva con la evasión.
- `Permanencia`, `Cargo Total` y `Cargo Diario` muestran correlación negativa, indicando que clientes con más tiempo o mayor inversión suelen permanecer más.
- Servicios como `Soporte Técnico`, `Protección Dispositivo` y `Seguridad en Línea` tienen ligera correlación negativa, sugiriendo una leve relación entre acceso a estos servicios y menor evasión.

## 🔢 **2. Relación entre número de servicios contratados y evasión**

<img src="https://drive.google.com/uc?id=1F-SYCkgK51ODzL6WAjVxyLLc3pckbl4i" alt="servicios evasión" width="600">

## ✅ **Conclusión del análisis de correlación**

- Cuantos menos servicios tiene un cliente, **es más probable que abandone la compañía**.
- Las **cuentas diarias más bajas** también muestran mayor relación con evasión.
- Este tipo de análisis es especialmente útil como **base para modelos de machine learning**, ya que permite seleccionar las variables más influyentes en la predicción de abandono.
