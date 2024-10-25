# Generación de datos sintéticos a partir de documentos del Archivo del Terror

## Resumen
A partir de archivos que tienen datos protegidos explorar la generación(generar) nuevos archivos que preserven las propiedades de los documentos originales sin poner en riesgo a las personas afectadas, para que aquellos que no tienen acceso a los datos originales puedan realizar pruebas y desarrollar herramientas relacionadas con este tipo de documentos sin comprometer la seguridad de la información.

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

El primer problema que nos encontramos fue el hecho de conseguir los datos. Al tratarse de datos protegidos, claramente es difícil acceder a los mismos. Finalmente, elegimos usar el Archivo del Terror del Uruguay ya que es un dataset público. 
El mismo se encuentra en Archive.org. Este proyecto estuvo caído durante gran parte de nuestro proyecto debido a ciberataques, lo que también nos generó dificultades en distintas etapas.


## Hipótesis de trabajo
1. Es posible generar nuevos documentos sinteticos que manteniendo la distribución de los documentos originales y tampoco vulneren su privacidad, es decir, no poseen ninguna entidad reconocida ni hay posibilidad de reidentificarlas.  
2. La metodología de generación es extensible a otros datos del mismo domino. Por ejemplo el Archivo de la Memoria de Argentina.

## Metodologia y Problemas

El primer problema que nos encontramos fue el hecho de conseguir los datos. Al tratarse de datos protegidos, claramente es difícil acceder a los mismos. Finalmente, elegimos usar el Archivo del Terror del Uruguay ya que es un dataset público. 
El mismo se encuentra en Archive.org. Este proyecto estuvo caído durante gran parte de nuestro proyecto debido a ciberataques, lo que también nos generó dificultades en distintas etapas.

### Seleccion de Textos
En el

### Privacidad Diferencial
Un concepto relevante para la privacidad es el de Privacidad Diferencial

### "Generación"
Los dos caminos a seguir fueron:
- Fine-Tune de un modelo de lenguaje con Privacida Diferencial (Differential Privacy): Guiandonos por el articulo [Synthetic Text Generation with Differential Privacy - A simple and Practical Recipe](https://arxiv.org/pdf/2210.14348) la idea era adaptar la libreria asociada [dp-transformers](https://github.com/microsoft/dp-transformers) para propociar el finetune con QLoRa y privacidad diferencial de un modelo gpt2 en epañol sobre los textos seleccionados del Archivo de Terror, para posteriormente generar informes sintèticos semejantes. Resulto imposible no solo imposible la adaptación por dificultades en la integración de la libreria [Opacus](https://opacus.ai/), encargada de dotar la privacidad en el pipeline, con QLora. Sino también, dado el caso de una correcta adaptación, los recursos disponibles (contando CCAD) son insuficientes para poder levarlo a cabo. En el repositorrio reportan que sus implementaciones fueron sobre 8 GPUs de minimo 40GB, en el articulo [Differentially Private Synthetic Data via Foundation Model APIs 2: Text](https://arxiv.org/pdf/2403.01749) reportan también que finetunear un GPT2 con DP tarda 456.71hs en una GPU de 32GB.
- Generación mediante 'sampleos privado' de documentos con Evolución Privada (PE): Guiandono por el paper el metodo [Differentially Private Synthetic Data via Foundation Model APIs 2: Text](https://arxiv.org/pdf/2403.01749) buscamos adpatar su respectiva libreria [aug-pe](https://github.com/AI-secure/aug-pe) desarrollada para los datos de Yelp, OpenReview y PubMed, consideramos PubMed la mas cercana a nuestro tipo de datos, no por el domino, sino por contar con textos mas bien largos sin ningun tipo de caracteristica adicional como Yelp y OpenReview que tienen por ejemplo calificaciones.
La idea del metodo de evolución privada desarrollada en el articulo, se basa de tres componentes fundaentales:
	- Samplin
	- Histrograma
	- Varaition

y a diferencia del finetune no requiere que accedamos directamente a los modelos de lenguaje, de igual manera el costo alto que puede conllevar realizar el finetune con DP de un modelo grande como Llama, resulta mas eficiente utilizar PE.  



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

- Semana del 23 al 29 de septiembre: Preparación del entorno de trabajo y adaptación al mismo. Continuación en la revisión de documentos académicos y repositorios útiles. [Completada]

- Semana del 30 de septiembre al 6 de octubre: Adaptación de repositorios relevantes para nuestro problema de interés. Modificación del conjunto de datos para optimizar su adecuación a los fines del proyecto. [Completada]

- Semana del 7 al 13 de octubre: Inicio de pruebas preliminares utilizando el modelo GPT-2. Estas pruebas serán breves y de corta duración, además de investigar métodos para la evaluación de los resultados obtenidos. [Completada sin éxito]. Si bien realizamos estas pruebas preliminares, GPT-2 no resultó útil para nuestro propósito. 

- Semana del 14 al 20 de octubre: Continuación de las pruebas, con la posibilidad de explorar otros modelos de lenguaje abiertos, preferentemente aquellos más grandes o recientes, y comparación de los resultados obtenidos. [Completada sin éxito] Adapatamos los dos modelos mencionados anteriormente en la metodología. Si bien exploramos diversos modelos de lenguajes, antes mencionados, no obtuvimos resultados favorables.

- Semana del 21 al 27 de octubre: Continuación de las pruebas y realización de una evaluación exhaustiva, así como la preparación del informe correspondiente. [No pudimos realizar la evaluación debido a que no obtuvimos resultados favorables]

- Semana del 28 de octubre al 4 de noviembre: Finalización del repositorio, asegurando su presentación de manera adecuada, y preparación de la presentación final del proyecto. []

### Planificación actualizada 

- Explorar si hay buenos resultados con la libreria aug-pe, detallado en la seccion metodologia, para algun corpus en ingles
- Generar textos sinteticos a partir de un solo texto privado suficientemente [lindo para ver de cerca] la generación
- Ver el impacto de aumentar la cantidad de samples generados para la calidad de los datos siteticos. Usando el CCAD.

## Trabajo a futuro

El principal plan a futuro es la generación de datos sintéticos, que fue la meta inicial de este proyecto. Para lograrlo, necesitaremos un mayor poder computacional y exploraremos otros modelos disponibles. 

Para esto, planteamos otros posibles trabajos que nos podrían ayudarnos a llegar a ese objetivo.
- Entender el rango de archivos.
- Investigar métodos más efectivos para la separación de datos.
- Explorar la herramienta Opacus.
- Realizar finetuning a un modelo más reciente para instrucciones en español.
- Probar la generación de datos con modelos cerrados como GPT 3.5.
- Variar y mejorar los prompts utilizados.
- Una vez generados los datos, identificar entidades.
- Diseñar correctamente un mecanismo de detección de combinaciones prohibidas.
- Llevar a cabo una evaluación downstream intermedia en BERT, basándonos en el paper.
- Performar ataques maliciosos de reidentificación para evaluar la efectividad del modelo.
- Investigar la aplicabilidad del modelo a otros datasets del mismo dominio.
