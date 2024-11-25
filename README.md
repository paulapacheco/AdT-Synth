
# Generación de datos sintéticos a partir de documentos del Archivo del Terror

## Resumen
A partir de archivos que tienen datos protegidos explorar la generación(generar) nuevos archivos que preserven las propiedades de los documentos originales sin poner en riesgo a las personas afectadas, para que aquellos que no tienen acceso a los datos originales puedan realizar pruebas y desarrollar herramientas relacionadas con este tipo de documentos sin comprometer la seguridad de la información.

En general, los archivos de la última dictadura militar están protegidos debido a que contienen inofrmación sensible de las víctimas. Nosotros elegimos trabajar con datos del Archivo del Terror de Uruguay, dicho dataset actualmente es público.

## Hipótesis de trabajo 2.0
1. Es posible generar nuevos documentos sinteticos manteniendo la distribución de los documentos originales sin dejar de vulnerar su privacidad, es decir, no poseen ninguna entidad reconocida ni hay posibilidad de reidentificarlas.  
2. La metodología de generación es extensible a otros datos del mismo domino. Por ejemplo el Archivo de la Memoria de Argentina.

## Objetivos preliminares y revisión:
- Generación de Datos Sintéticos: Crear archivos sintéticos que preserven las propiedades de los documentos originales del Archivo del Terror de Uruguay. Evaluando en primer lugar la comparabilidad entre los datos sintéticos y el archivo de memoria de Argentina. Y la preservacion de distintas distribuciones subyacentes en los datos originales ya sea a nivel caracteres, palabras, entidades, entre otros.

  La generación de un un nuevo archivo sintetico ha sido abandonada, pero hay minusculas tareas de inspección que aun podemos llevar a cabo:
  - Explorar si hay buenos resultados con la libreria aug-pe, detallado en la seccion metodologia, para algun dataset en ingles y/o español con textos mas limpios que el Archivo del Terror. 
  - Generar textos sinteticos a partir de un solo texto privado suficientemente 'bueno', en sentido de no tener el menor ruido posible.
  - Ver-+ el impacto de aumentar la cantidad de samples generados para la calidad de los datos siteticos.
  - Identificar entidades o cadenas de palabras en los datos prohibidos consideradas como prohibidas para el presunto conjunto de datos sintetico.
     
- Resistencia a ataques maliciosos: Diseñar datos sintéticos que sean resistentes a ataques maliciosos.
- Validación de Resultados: Establecer un protocolo para evaluar la efectividad de los datos sintéticos en la investigación.

  Los ultimos objetivos resultan imposibles dado que el aterior resulto abandonado. 


## Metodologia y Problemas

El primer problema que nos encontramos fue el hecho de conseguir los datos. Al tratarse de datos protegidos, claramente es difícil acceder a los mismos. Finalmente, elegimos usar el Archivo del Terror del Uruguay ya que es un dataset público ubicado en  Archive.org.  

En las ultimas semanas, cuando finalmente logramos hacer aveces en la prooducción, la pagina estuvo caída proyecto debido a ciberataques, por ende perdimos el acceso a los pdf correspondientes a cada texto del OCR. Los pdf nos servian de referencia para entender el procesamiento de los textos crudos, que en partes pueden resultar ininteligibles, por suerte la idea general ya la obtuvimos antes de que perdamos el acceso.

### Seleccion de Textos
Dentrro del Archivo del Terror hay multiples rollos a documentos de la dicatadura en Uruguay. Entre ellos hay distintos tipos de contenido como:
- Listas de personas ionvestigadas
- Archivos de grafica (noticias/revistas)
- Transcripciones de informe privados sobre vecinos o conocidos sospechosos.

Decidimos enfocarnos solo en los rollos (19) dedicados a informes privados para la generación de textos. Descartamos las listas de nombres por ser pobres para una generacion de textos. Dejamos de lado también otro tipo de rollos por ser muy diversos no solo en estrucutura sino también en contenido. Los rollos de informes solo poseen la trascripcion junto a un membrete al inicio de cada agente infomante.

### Privacidad Diferencial
Para asegura que ciertos métodos cumplan con alguna garantía de privacidad es nos guiamos por un concepto fundamental, ya aceptado como un estandard, la Privacidad Diferencial (**DP**). La formulación matemática es la siguiente:
![image](https://github.com/user-attachments/assets/c2cd1fd5-1c6a-43b6-9ca4-af5cd1852a7e)

Un algorito randomizado $M:\mathcal D\to \mathcal S$ es $(\epsilon,\delta)$ ***- diferencialmente privado*** si dados dos datasets $d, \tilde{d}\in \mathcal{D}$ adjacentes (se diferencian en una muestra) para todo $s\in \mathcal S$ se cumple que 

$$P[M(d)\in s]\leq e^\epsilon\ P[M(\tilde{d})\in s] + \delta$$

Donde $\epsilon$ cuantifica el máximo impacto permitdo por un dato indivudual en la salida. $\delta$ especifica la máxima probabilidad de que la garantía de privacidad pueda fallar.

Los metodos utilizados tienen en cuenta esta definición y demuestran que cada uno sus algoritmos la cumplen.

### "Generación"
Los dos caminos a seguir fueron:
- Fine-Tune de un modelo de lenguaje con Privacida Diferencial : Guiandonos por el articulo[^1], la idea era adaptar la libreria asociada[^2] para hacer un el finetune con QLoRa + DP de un modelo gpt2 en epañol sobre los textos seleccionados del Archivo de Terror, para posteriormente generar informes sintéticos semejantes. La adaptación resulta imposible, en primer lugar por errores fuera de nuestra comprensión (PyTorch), y en segundo lugar por insuficiencia de recursos disponibles (contando CCAD). En el repositorrio[^2] reportan que sus implementaciones fueron sobre 8 GPUs de minimo 40GB, en el articulo[^3] reportan también que finetunear un GPT2 con DP tarda 456.71hs en una GPU de 32GB.
Indagando en [Opacus](https://opacus.ai/), libreria encargada de dotar la privacidad en el pipeline, no logramos ni siquiera hacer un setup simple de finetuninig DP. El motivo de la exigencia de recursos de este metodo, es la utilización del algoritmo **DP-SGD** un descenso estocastico pero con privacidad diferencial, es el calculo gradientes por muestra (per-sample gradient) para hacer un recorte (clipping) que garanztiza el DP. 
- Generación mediante 'sampleos privado' de documentos con Evolución Privada (PE): Guiandono por el paper[^3] el metodo buscamos adpatar su respectiva libreria[^4] desarrollada para los datos de Yelp, OpenReview y PubMed. Consideramos PubMed la mas cercana a nuestro tipo de datos, no por el domino, sino por contar con textos medianos (abstracts de medicina) sin ningun tipo de caracteristica adicional como Yelp y OpenReview que tienen, por ejemplo, calificaciones. Por lo tanto utilizamosel desarrollo para PubMed como guía en la adaptación.
La idea del metodo de evolución privada (**PE**) desarrollada en el articulo, se basa de tres componentes fundaentales:

	- RANDOM_API: Genera $n$ muestras sinteticas con un modelo de lenguaje.
	- DP_NN_HISTOGRAM: Cada muestra privada vota por los documentos sinteticos mas cercanos en un espacio donde son embebidos. Añade luego ruido Gausiano a los votos para asegurar DP.
	- VARIATION_API: Hace variaciones de los documentos sinteticos generados inicialmente.

	Una vez generados los datos sinteticos inicales RANDOM_API luego se itera sobre DP_NN_HISTOGRAM y VARIATION_API las epocas deseadas. 
	
	Cabe destacar que, al ser la privacidad asegurada 'por fuera' de la generación, este algoritmo solo solicita los pasos de inferencia de los modelos de lenguaje. Es por ello que, si tenemos acceso, podremos utilizar aun modelos de lenguaje cerrados. También resulta más eficiente, si la cantidad $n$ de muestras generadas no es gigante, este metodo para modelos abiertos con muchos parámetros en comparación al finetune con DP. 

	Debemos elegir entonces un modelo de lenguaje para generación, un modelo de sentence tranformer para relizar los embeddings y  un adecuado diseño de prompts para la generación y variación.  

	Para el modelo de generacion de texto buscamos uno que soporte instrucciones para desarrollar mejor la tarea. Primero elegimos Lama3.? 1B Instruct, pero notamos que por mas de ser miltiligual, la generación terminaba siendo puramente en ingles. Para lograr la generación en espaól encontramos un Llama 7B Instruct finetuneado para español. para el sentence tranformer elegimos ... 
	
	Un objetivo inicial era poder crear documentos parecidos en formato al original, es decir, un membrete y la transcricion del informe junto al ruido provocado por el OCR. Para intentar eso utilizabamos los 19 documentos sin procesar. Para el prompt de genereacion dictamos: 
	>"Escribi un informe policial de un informante anonimo: " 

	Para la variacion:
	> "de forma casual",  "de forma creativa",  "de forma concisa"
	
	Notamos en este primer intento mucho apego al ruido terminando con documentos completasmente ininetelibles o en ruso!
	
	Abandonamos entonces el ambicioso objetivo de crear documentos completos. Para intentar mejorar la generación en una tarea mas específica de genera meramente informes, dividimos cada uno de los documentos en algunos mas pequeños de 500 palabras. Primero identificamos dos palabras clave, que parecen ser generalmente bien leidas por el OCR, como divisores principales:
	
	- 'Agente' que aparece al inicio de cada membrete
	
	- 'Texto'  que aparece justo antes de los informes

	Como dijimos, el ruido ya lo consideramos fuera de nuestro alcance, por lo tanto, antes de dividir, porcedimos a eliminar las stopwords y minuscular. Una vez hecha esa division[^5] segun las palabras 'clave' con el  [CharacterTextSplitter](	https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.CharacterTextSplitter.html#) (de langachain) seguimos subdividiendo cada una de ellas con el [RecursiveCharacterTextSplitter](https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.RecursiveCharacterTextSplitter.html#) hasta que todos los mini chunks tengan largo menor a 500. Resultando en $\sim 35000$ documentos distintos.


	El prompt inicial de generación estaba mal hecho, no es un iformante el que escribe, sino mas bien una transcripción por ello los cambiamos por:
	> 'Sos un policia espia. Por sospechas de subversivos fuentes anonimas te informan sobre intinerarios o movimientos de vecinos o conocidos. Escribi una transcripción un informe que te entrego un informante privado.'
	
	Dados los recursos disponibles hasta la fecha, nos acotamos a la generación de a lo sumo $n=4$ muestras iniciales (en el articulo hacen minimo $n=2000$), los resultados con esta cantidad son poco prometedores, y peor aun es que a medida que mas epocas se itera peor son los datos sinteticos.

Un metodo descartdo por su demanda de recursos fue 
el post-procesado de OCR, que podrían resultar eficiente para la tarea de generar informes.

## Evaluación (digamos)
La evaluación de las tareas finales, dado su corto alcance, sera predominantemente anecdótica.

Para comparar la similaridad de los datos sintéticos con los originales se utilizan las metricas sobre el espacio donde las embebemos, como el **FID** (Fréchet Inception Distance), la ***'kNN' F1-Score*** (F1 claculado sobre kNN datos originales de los datos generados y viceversa) dada la pobre performance de los datos hasta ahora obtenidos los resultados son consecuentemente malos. Otra posible métrica es la **MAUVE** pero no la calculamos dada la poca cantidad de muestras generadas. 

Planteando un hipotético futuro donde consiguieramos efectivamente datos sinteticos, la seguiriamos el camino del articulo .... , finetuneando algun modelo BERT sobre los datos sinteticos y evaluar la accuracy en la prección de la proxima palabra. 

## Sugerencias
> ¿Se utilizarán otros documentos históricos para evaluar la herramienta? ¿Quizás la comparabilidad con el Archivo de Argentina sea un objetivo para una etapa posterior? ¿Quizás un objetivo intermedio sería consolidar una metodología que se pudiera aplicar al Archivo de Argentina para poder generar datos sintéticos a partir de esos datos?

Si terminasemos de desarrollar propieamente la generación de textos y contanmos con el acceso a otros datos sería optimo. Dado que nos restringimos solo a informes, podriamos ver como se comporta el mismo pipeline con un rollo cualquiera del Archivo del Terror
Efectivamente es un objetivo ulterior poder generalizar a cualquier dataset del dominio.

## Planificación

- Semana del 23 al 29 de septiembre: Preparación del entorno de trabajo y adaptación al mismo. Continuación en la revisión de documentos académicos y repositorios útiles. ***[Completada]***

- Semana del 30 de septiembre al 6 de octubre: Adaptación de repositorios relevantes para nuestro problema de interés. Modificación del conjunto de datos para optimizar su adecuación a los fines del proyecto. **_[Completada]_**

- Semana del 7 al 13 de octubre: Inicio de pruebas preliminares utilizando el modelo GPT-2. Estas pruebas serán breves y de corta duración, además de investigar métodos para la evaluación de los resultados obtenidos. **_[Completada sin éxito]_**. Si bien realizamos estas pruebas preliminares, GPT-2 no resultó útil para nuestro propósito. 

- Semana del 14 al 20 de octubre: Continuación de las pruebas, con la posibilidad de explorar otros modelos de lenguaje abiertos, preferentemente aquellos más grandes o recientes, y comparación de los resultados obtenidos. **_[Completada sin éxito]_**. Adapatamos los dos modelos mencionados anteriormente en la metodología. Si bien exploramos diversos modelos de lenguajes, antes mencionados, no obtuvimos resultados favorables.

- Semana del 21 al 27 de octubre: Continuación de las pruebas y realización de una evaluación exhaustiva, así como la preparación del informe correspondiente. **_[No pudimos realizar la evaluación debido a que no obtuvimos resultados favorables]_.**

- Semana del 28 de octubre al 4 de noviembre: Finalización del repositorio, asegurando su presentación de manera adecuada, y preparación de la presentación final del proyecto. **_[En curso]_**.

### Planificación actualizada 

Durante las próximas semanas, además de preparar el informe final y la presentación como está planificado, realizaremos algunas modificaciones a lo planeado, como:

- Investigar la efectividad de aug-pe, detallado en la sección de metodología, en un corpus en inglés.
- Generar textos sinteticos a partir de un único texto privado que sea lo suficientemente bueno para examinar de cerca el proceso de generación.
- Analizar cómo afecta el incremento en la cantidad de muestras generadas a la calidad de los datos sintéticos, utilizando el CCAD.

## Trabajo a futuro

El principal plan a futuro es la generación de datos sintéticos, que fue la meta inicial de este proyecto. Para lograrlo, necesitaremos un mayor poder computacional y exploraremos otros modelos disponibles. 

Para esto, planteamos otros posibles trabajos que nos podrían ayudarnos a llegar a ese objetivo.
- Extender el rango de rollos: No acotarnos solo a los archivos de informe, sino también toda la variedad de documentos, con nuevas difulatades para el procesamiento, aun que ganando más datos.
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

## Referencias
[^1]: [Synthetic Text Generation with Differential Privacy - A simple and Practical Recipe](https://arxiv.org/pdf/2210.14348)
[^2]: [dp-transformers](https://github.com/microsoft/dp-transformers)
[^3]: [Differentially Private Synthetic Data via Foundation Model APIs 2: Text](https://alphapav.github.io/augpe-dpapitext/)
[^4]: [aug-pe](https://github.com/AI-secure/aug-pe)
[^5]: [Notebook de Text Spliting](https://github.com/FullStackRetrieval-com/RetrievalTutorials/blob/main/tutorials/LevelsOfTextSplitting/5_Levels_Of_Text_Splitting.ipynb)
