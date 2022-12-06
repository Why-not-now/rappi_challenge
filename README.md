# CHALLENGE RAPPIfunciones

## Descripción
El challenge consiste en las siguientes tareas:
- [x] 1. Cargar el siguiente set de datos en una base de datos en snowflake (abrir una cuenta de prueba) https://www.kaggle.com/megelon/meetup 


- [x] 2. Realizar creación de tablas físicas auxiliares, el archivo esta adjunto


- [x] 3. Crear un DAG usando Apache Airflow (puede ser usando un servicio administrado google composer, aws mwaa, astronomer, local, otro)

El objetivo del DAG es automatizar las tareas en el punto 2. Es decir crear nuevas tablas con otros nombres para realizar el proceso de manera automática cada 15 minutos.

Usa tu creatividad para generar nuevos datos y que tenga más sentido la automatización, usar MERGES, CREATES AND REPLACES y otras funciones de snowflake.


### ✨Bonos✨:

- [x] Tomar las tablas procesadas y usando Apache NiFi mandarlas a un bucket de s3. (El envió a S3 fue realizado sin embargo con otro método)

- [x] Crear alertas para slack usando airflow.

- [x] Empezar el curso de airflow de Marc Lamberti y exponer conceptos clave

- [ ] Hacer un ejercicio con un dataset diferente

## Herramientas utilizadas
- Snowflake
- Google Cloud Composer
- Google Cloud Storage
- Apache Airflow
- Apache NiFi
- Slack
- Dbeaver
- Docker
- AWS S3

## Configuraciones iniciales

1. Crear un warehouse en Snowflake
2. Crear una base de datos en Snowflake
3. Levantar una instancia de Airflow en Google Cloud Composer
4. Realizar la conexión de DBeaver con snowflake para la carga de la data

# Screenshots de la realización del challenge

## Instancia de snowflake 
![Tablas subidas y auxiliares en la instancia de snowflake en el database de RAPPI](screenshots/screenshot_142.png)
## Instancia de Airflow en Google Cloud Composer
![Instancia de airflow levantada en Composer](/screenshots/screenshot_143.png)

## Ejecución del DAG que creó las tablas auxiliares a partir de los data entries en Kaggle

![Instancia de airflow levantada en Composer](screenshots/screenshot_144.png)

## Bucket en Google Cloud Storage donde se encuentran los archivos a utilizar en Airflow

![Bucket de airflow donde se encuentran las DML y el DAG que automatiza la carga de las tablas](screenshots/screenshot_145.png)

## Integración de Slack con Apache Airflow
Como se puede ver se las ejecuciones son cada 15 minutos y de igual manera se ha implementado alertas para cuando hayan errores así cómo para cuando el DAG inicia y finaliza.
![Integración de Airflow con Slack](screenshots/screenshot_147.png)

## Envío de tablas como archivos a AWS S3 utilizando operadores en airflow, primero enviando de snowflake a Google Cloud Storage y luego de GCS a AWS S3

Se utilizaron 2 operadores principales:
- Primero para mandar de snowflake a GCS el cual corre una DML que replica las tablas en el Storage
```python
        pythonsnowflake_op_copy_snowflake_into_gcs = SnowflakeOperator(
            task_id="snowflake_op_copy_snowflake_into_gcs",
            sql= path_sql+"copy_snowflake_into_gcs.sql",
            split_statements=True,
        )
```
- Segundo se envia de GCS a S3 a través de otro operador "GCSToS3Operator"
```python
        gcs_to_s3 = GCSToS3Operator(
            task_id="gcs_to_s3",
            bucket='rappi_test_storage',
            dest_s3_key='s3://rappi-bucket-aws/',
            replace=True,
            dest_aws_conn_id = S3_CONN_ID
        )
```
### Acá se muestra el bucket en GCS con los archivos de las tablas en Snowflake
![Bucket de GCS con archivos que hacen referencia a las tablas de snowflake](screenshots/screenshot_149.png)
### Y finalmente este es replicado en S3 con el operador mostrado previamente
![Bucket en S3 con archivos replicados de GCS](screenshots/screenshot_150.png)

# Eso sería todo muchas gracias.
