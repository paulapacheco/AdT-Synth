# Generación de datos sintéticos a partir de documentos del Archivo del Terror

## Resumen
A partir de archivos que tienen datos protegidos generar nuevos archivos que preserven las propiedades de los documentos originales sin poner en riesgo a las personas afectadas, para que aquellos que no tienen acceso a los datos originales puedan realizar pruebas y desarrollar herramientas relacionadas con este tipo de documentos sin comprometer la seguridad de la información.

En general, los archivos de la última dictadura militar están protegidos debido a que contienen inofrmación sensible de las víctimas. Nosotros elegimos trabajar con datos del Archivo del Terror de Uruguay, dicho dataset actualmente es público.

## Objetivos preliminares y revisión:
- Generación de Datos Sintéticos: Crear archivos sintéticos que preserven las propiedades de los documentos originales del Archivo del Terror de Uruguay. Evaluando en primer lugar la comparabilidad entre los datos sintéticos y el archivo de memoria de Argentina. Y la preservacion de distintas distribuciones subyacentes en los datos originales ya sea a nivel caracteres, palabras, entidades, entre otros.

  La generación de un un nuevo archivo sintetico ha sido abandonada, pero hay minusculas tareas de inspección que aun podemos llevar a cabo:
  - Explorar si hay buenos resultados con la libreria aug-pe, detallado en la seccion metodologia, para algun corpus en ingles y español con [mejor porcesamiento] NO MG que el Archivo del Terror. Generar textos sinteticos a partir de un solo texto privado suficientemente [lindo para ver de cerca] la generación.
  - Ver el impacto de aumentar la cantidad de samples generados para la calidad de los datos siteticos.
  - Identificar entidades o cadenas de palabras en los datos prohibidos consideradas como prohibidas para el presunto conjunto de datos sintetico.
     
- Resistencia a ataques maliciosos: Diseñar datos sintéticos que sean resistentes a ataques maliciosos.
- Validación de Resultados: Establecer un protocolo para evaluar la efectividad de los datos sintéticos en la investigación.

  Los ultimos objetivos resultan imposibles dado que el aterior resulto abandonado. 

## Metodologia y Problemas

El primer problema que nos encontramos fue el hecho de conseguir los datos. Al tratarse de datasets con datos protegidos, claramente es difícil acceder a los mismos. Finalmente, elegimos usar el Archivo del Terror del Uruguay ya que es un dataset público. 


Los dos caminos a seguir fueron:
- Fine-Tune de un modelo de lenguaje con Privacida Diferencial (Differential Privacy): Guiandonos por el paper...
- Generación mediante 'sampleos privado' de documentos con Evolución Privada (PE): Guiandono por el paper ... split clean




La generación de datos sintéticos no resutlto fructífera con ninguno de los metodos empleados.

## Hipótesis de trabajo
1. El corpus del Archivo del Terro es comparable al archivo de la memoria de Argentina, y por lo tanto, las herramientas que sean desarrolladas pueden aplicarse a este dataset.
2. Es posible generar nuevos documentos manteniendo la distribución de los documentos originales.
3.
4. Estas propiedades son por ejemplo, la distribución de palabras, la distribución de entidades nombradas, la distribución sobre la estructura del documento, etc.
5. Es posible generar datos sintéticos resistentes a ataques maliciosos para identificar a las víctimas. 

## Referencia
- [Synthetic Text Generation with Differential Privacy - A simple and Practical Recipe](https://arxiv.org/pdf/2210.14348)
- [dp-transformers](https://github.com/microsoft/dp-transformers)
- [Differentially Private Synthetic Data via Foundation Model APIs 2: Text](https://alphapav.github.io/augpe-dpapitext/)
- [aug-pe](https://github.com/AI-secure/aug-pe)
- [Synthetically generated text for supervised text analysis](https://drive.google.com/open?id=105fOMEQiZ9p0oq6BsIP1eHgIkv3aSV5h&usp=drive_fs)
- [Notebook de Text Spliting](https://github.com/FullStackRetrieval-com/RetrievalTutorials/blob/main/tutorials/LevelsOfTextSplitting/5_Levels_Of_Text_Splitting.ipynb)

## Planificación

- Semana del 23 al 29 de septiembre: Preparación del entorno de trabajo y adaptación al mismo. Continuación en la revisión de documentos académicos y repositorios útiles.

- Semana del 30 de septiembre al 6 de octubre: Adaptación de repositorios relevantes para nuestro problema de interés. Modificación del conjunto de datos para optimizar su adecuación a los fines del proyecto.

- Semana del 7 al 13 de octubre: Inicio de pruebas preliminares utilizando el modelo GPT-2. Estas pruebas serán breves y de corta duración, además de investigar métodos para la evaluación de los resultados obtenidos.

- Semana del 14 al 20 de octubre: Continuación de las pruebas, con la posibilidad de explorar otros modelos de lenguaje abiertos, preferentemente aquellos más grandes o recientes, y comparación de los resultados obtenidos.

- Semana del 21 al 27 de octubre: Continuación de las pruebas y realización de una evaluación exhaustiva, así como la preparación del informe correspondiente.

- Semana del 28 de octubre al 4 de noviembre: Finalización del repositorio, asegurando su presentación de manera adecuada, y preparación de la presentación final del proyecto.

