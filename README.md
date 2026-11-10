# Laboratorio 7: Pipeline ETL, Limpieza Condicionada y Arquitectura de Data Lake


## Descripción del Proyecto
Implementación de un flujo ETL y perfilamiento estadístico sobre un Data Lake con arquitectura de dos capas (RAW y Curated), procesando 5,050,000 registros sintéticos de transacciones con ruido inyectado (nulos, duplicados y outliers)

## Resumen del Pipeline ETL
* **Ingesta y Capa RAW:** Se generaron 5,000,000 de registros base más 50,000 duplicados, un 5% de nulos en `monto` (252,510 filas) y 100 outliers de gran magnitud (9,999,999.99). Los datos crudos se guardaron inalterados en `./datalake/raw/ingesta_transacciones_sucio.parquet` (74.41 MB).
* **Eliminación de Duplicados:** Remoción de 49,999 registros exactos repetidos mediante `drop_duplicates()`.
* **Tratamiento Condicional de Nulos:** Se eliminaron 62,513 filas en estado 'Cancelado' que no tenían monto. En los registros restantes, se imputó el monto nulo utilizando la mediana de su categoría correspondiente.
* **Filtrado de Outliers (IQR):** Se aplicó la regla del Rango Intercuartílico ($IQR = Q_3 - Q_1$). Las 101 filas con valores atípicos superiores al límite ($Q_3 + 1.5 \times IQR$) fueron removidas y enviadas a cuarentena en `./datalake/quarantine/outliers.parquet`
* **Normalización Min-Max:** Creación de la columna `monto_escalado` con valores en el rango [0, 1].
* **Capa Curated:** Guardado final del dataset limpio (4,999,900 filas) en `./datalake/curated/transacciones_model_ready.parquet` (134.39 MB).

## Resultados Estadísticos (Monto)
* **RAW (Sucio):** 4,797,490 registros | Promedio: 1,463.00 | Desviación Estándar: 45,882.62 | Mín: 5.00 | Máx: 9,999,999.99
* **CURATED (Limpio):** 4,999,900 registros | Promedio: 1,252.50 | Desviación Estándar: 702.03 | Mín: 5.00 | Máx: 2,500.00
* **Monto Escalado:** Promedio: 0.50 | Desviación Estándar: 0.28 | Mín: 0.00 | Máx: 1.00
