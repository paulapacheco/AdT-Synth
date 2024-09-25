# Generación de datos sintéticos a partir de documentos del Archivo del Terror

## Resumen
A partir de archivos que tienen datos protegidos generar nuevos archivos que preserven las propiedades de los documentos originales sin poner en riesgo a las personas afectadas, para que aquellos que no tienen acceso a los datos originales puedan realizar pruebas y desarrollar herramientas relacionadas con este tipo de documentos sin comprometer la seguridad de la información.

## Hipótesis de trabajo
1. Desarrollar estos datos sintéticos basados en el Archivo del Terror de Uruguay, con la hipótesis de que este corpus es comparable al archivo de la memoria de Argentina, y por lo tanto, las herramientas que sean desarrolladas pueden aplicarse a este dataset.
2. Es posible generar nuevos documentos manteniendo la distribución de los documentos originales. Estas propiedades son por ejemplo, la distribución de palabras, la distribución de entidades nombradas, la distribución sobre la estructura del documento, etc.
3. Es posible generar datos sintéticos resistentes a ataques maliciosos para identificar a las víctimas.

## Objetivos preliminares



## Técnicas relevantes para aplicar
- Post-procesado de OCR posiblemente con [OCRonos](https://huggingface.co/PleIAs/OCRonos)
- Procesado del texto (splitting, spellchecking)
- Identificacion de informacion privada (PII) con [piiranha](https://huggingface.co/iiiorg/piiranha-v1-detect-personal-information?text=27+de+Abril+al+2000)
- Finetune (no privado) a algun dataset 'cercano' al de nustro interes. 
- [aug-pe](https://github.com/AI-secure/aug-pe) adaptado para algun modelo de lenguaje (abierto) en español
- 

## Referencia
- [Differentially Private Synthetic Data via Foundation Model APIs 2: Text](https://alphapav.github.io/augpe-dpapitext/)
- [Notebook de Text Spliting](https://github.com/FullStackRetrieval-com/RetrievalTutorials/blob/main/tutorials/LevelsOfTextSplitting/5_Levels_Of_Text_Splitting.ipynb)
-


## Planificación

- Semana del 23 al 29 de septiembre: Preparación del entorno de trabajo y adaptación al mismo. Continuación en la revisión de documentos académicos y repositorios útiles.

- Semana del 30 de septiembre al 6 de octubre: Adaptación de repositorios relevantes para nuestro problema de interés. Modificación del conjunto de datos para optimizar su adecuación a los fines del proyecto.

- Semana del 7 al 13 de octubre: Inicio de pruebas preliminares utilizando el modelo GPT-2. Estas pruebas serán breves y de corta duración, además de investigar métodos para la evaluación de los resultados obtenidos.

- Semana del 14 al 20 de octubre: Continuación de las pruebas, con la posibilidad de explorar otros modelos de lenguaje abiertos, preferentemente aquellos más grandes o recientes, y comparación de los resultados obtenidos.

- Semana del 21 al 27 de octubre: Continuación de las pruebas y realización de una evaluación exhaustiva, así como la preparación del informe correspondiente.

- Semana del 28 de octubre al 4 de noviembre: Finalización del repositorio, asegurando su presentación de manera adecuada, y preparación de la presentación final del proyecto.
