# Detección de Transferencias Fraccionadas

Este proyecto identifica posibles casos de transferencias fraccionadas en una base de transacciones masiva, utilizando ventanas móviles y un modelo supervisado. El modelo despues de analizar las transacciones en una misma ventana de 24 horas reporta como sospechosa la ultima transaccion que cumple con los criterios. De este modo identifica a partir de un nuemro determinado de transacciones cuando se puede considerar fraccionamiento.

## Objetivo

Detectar comportamientos donde un usuario realiza múltiples transferencias pequeñas a una o pocas cuentas en un período corto, lo cual podría indicar intento de evasión o fraccionamiento de montos.

##  Metodología

1. **Carga y exploración de datos** (EDA):
   - Limpieza y análisis exploratorio con Polars
   - Visualización de montos, horarios y patrones de transacción
   - Se analiza cuales son los montos normales, los dias y horas normales y el nuemro promedio de trasferencias que realiza un usuario así como 
    la distribución del monto de las tranferencias

2. **Construcción de etiquetas (`sospechoso_24h`)**
   - Ventana móvil por usuario de 24 horas
   - Se marca como sospechosa la transacción si:
     - Se han hecho al menos 2 transacciones en ese periodo
     - El monto total supera 100 (menor a la media y a la mediana)
     - Se han enviado a 2 o menos cuentas, es decir repite.

3. **Ingeniería de features**
   - Transacciones por hora, día, usuario
   - Proporción de montos bajos
   - Desviación estándar y diferencia promedio de montos
   - Flag si varias transacciones en una misma hora

4. **Modelado**
   - Clasificación con XGBoost + SMOTE para balancear clases
   - Validación cruzada estratificada (f1, precision, recall, ROC AUC)

5. **Resultados**
   - Aplicación del modelo a una muestra de 150.000 transacciones (el computador donde realicé la prueba no me permitía cargar muchos más datos por la RAM. por esto decidi hacer muestras aleatorias que será representativa para los resultados de la totalidad)
   - Se detectaron 310 usuarios con 314 transacciones sospechosas, aprox. 0.2%

##  Archivos principales

- `modelo_fraccionamiento.ipynb`: notebook con todo el pipeline, desde la exploración de la base hasta la prueba en la muestra después de entrenar.
- `pipeline_fraccionamiento.pkl`: modelo entrenado con el mejor resultado (explore diferentes especificaciones pero solo dejé la de XGBoost)
- `usuarios_sospechosos_muestra.csv`: resultado aplicado a muestra
- El archivo PDF busca mostrar el proceso mental que se siguió, qué variables se analizaron y qué camino se tomó para construir el modelo.

##  Requisitos

- Polars
- scikit-learn
- imbalanced-learn
- xgboost
- pandas

Instala con:

```bash
pip install -r requirements.txt
