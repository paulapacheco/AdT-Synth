# Generación de datos sintéticos a partir de documentos del Archivo del Terror

## Resumen
El objetivo inicial de este proyecto fue: a partir de archivos que tienen datos protegidos explorar la generación de nuevos archivos que preserven las propiedades de los documentos originales sin poner en riesgo a las personas afectadas, para que aquellos que no tienen acceso a los datos originales puedan realizar pruebas y desarrollar herramientas relacionadas con este tipo de documentos sin comprometer la seguridad de la información.

En general, los archivos de la última dictadura militar están protegidos debido a que contienen inofrmación sensible de las víctimas. Nosotros elegimos trabajar con datos del Archivo del Terror de Uruguay, dicho dataset actualmente es público.

El proyecto finalmente consistió en una prueba de factibilidad para considerar la generación de datos sintéticos con los recursos que tenemos disponibles.

## Hipótesis de trabajo
1. Es posible generar nuevos documentos sinteticos manteniendo la distribución de los documentos originales sin dejar de vulnerar su privacidad, es decir, no poseen ninguna entidad reconocida ni hay posibilidad de reidentificarlas.  

Y una hipótesis a posteriori, suponiendo una prudente generación de datos sintéticos.

2. La metodología de generación es extensible a otros datos del mismo domino. Por ejemplo el Archivo de la Memoria de Argentina.

## Objetivos preliminares y revisión:
- **Generación de Datos Sintéticos**: Crear archivos sintéticos que preserven las propiedades de los documentos originales del Archivo del Terror de Uruguay. Evaluando en primer lugar la comparabilidad entre los datos sintéticos y el archivo de memoria de Argentina. Y la preservacion de distintas distribuciones subyacentes en los datos originales ya sea a nivel caracteres, palabras, entidades, entre otros.

  La generación de un un nuevo archivo sintético ha sido abandonada debido a los pobres resultados obtenidos, no obstante, realizamos minúsculas tareas de inspección para evaluar los métodos de generación de texto y recursos a disposición:
  - Explorar si hay buenos resultados con la libreria aug-pe, detallado en la seccion metodologia, para algun dataset en ingles y/o español con textos mas 'limpios' que el Archivo del Terror. 
  - Generar textos sintéticos y sucesivas variaiciones de un solo texto corto en el Archivo del Terror escogido y pulido a mano, es decir, un texto sin ruido.
     
- **Resistencia a ataques maliciosos**: Diseñar datos sintéticos que sean resistentes a ataques maliciosos.
- **Validación de Resultados**: Establecer un protocolo para evaluar la efectividad de los datos sintéticos en la investigación.

  Los ultimos objetivos resultaron imposibles dado que el anterior resulto abandonado. 


## Metodologia y Problemas

El primer problema que nos encontramos fue el hecho de conseguir los datos. Al tratarse de datos protegidos, claramente es difícil acceder a los mismos. Finalmente, elegimos usar el Archivo del Terror del Uruguay ya que es un dataset público ubicado en [Archivo del Terror](https://archive.org/search?query=creator%3A%22Servicio+de+Informaci%C3%B3n+de+Defensa+%28SID%29%22).  

En las ultimas semanas, cuando finalmente logramos hacer aveces en la producción, la página estuvo caída debido a ciberataques, por ende perdimos el acceso a los pdf correspondientes a cada texto del OCR. Los pdf nos servian de referencia para entender el procesamiento de los textos crudos, que en partes pueden resultar ininteligibles, por suerte la idea general la obtuvimos antes de perder el acceso.

### Seleccion de Textos
Dentro del Archivo del Terror hay múltiples rollos de documentos de la dicatadura en Uruguay. Entre ellos hay distintos tipos de contenido como:
- Listas de personas investigadas,
- Archivos de grafica (noticias/revistas),
- Transcripciones de informe privados sobre vecinos o conocidos sospechosos.

Decidimos enfocarnos solo en los 19 rollos dedicados a informes privados para la generación de textos. Descartamos las listas de nombres por ser pobres para una generacion de textos. Dejamos de lado también otro tipo de rollos por ser muy diversos no solo en estrucutura sino también en contenido. Los rollos de informes solo poseen la trascripción junto a un membrete al inicio de cada agente infomante.

### Privacidad Diferencial
Para asegura que ciertos métodos cumplan con alguna garantía de privacidad es nos guiamos por un concepto fundamental, ya aceptado como un estandard, la Privacidad Diferencial (**DP**). La formulación matemática es la siguiente:
Que preferis??

![image](https://github.com/user-attachments/assets/100faa2f-e389-4b91-9086-4b9985441954)



Un algorito randomizado $M:\mathcal D\to \mathcal S$ es $(\epsilon,\delta)$ ***- diferencialmente privado*** si dados dos datasets $d, \tilde{d}\in \mathcal{D}$ adjacentes (se diferencian en una muestra) para todo $s\in \mathcal S$ se cumple que 

$$P[M(d)\in s]\leq e^\epsilon\ P[M(\tilde{d})\in s] + \delta$$

Donde $\epsilon$ cuantifica el máximo impacto permitdo por un dato indivudual en la salida. $\delta$ especifica la máxima probabilidad de que la garantía de privacidad pueda fallar.

Los metodos utilizados tienen en cuenta esta definición y demuestran que cada uno sus algoritmos la cumplen.

### "Generación"
Los dos caminos a seguir fueron:
- **Fine-Tune de un modelo de lenguaje con Privacidad Diferencial** : Guiándonos por el articulo[^1], la idea era adaptar la libreróa asociada[^2] para hacer un el finetune con QLoRa + DP de un modelo gpt2 en epañol sobre los textos seleccionados del Archivo de Terror, para posteriormente generar informes sintéticos semejantes. La adaptación resulta imposible, en primer lugar por errores fuera de nuestra comprensión (PyTorch), y en segundo lugar por insuficiencia de recursos disponibles (contando CCAD). En el repositorrio[^2] reportan que sus implementaciones fueron sobre 8 GPUs de minimo 40GB, en el articulo[^3] reportan también que finetunear un GPT2 con DP tarda 456.71hs en una GPU de 32GB.
Indagando en [Opacus](https://opacus.ai/), librería encargada de dotar la privacidad en el pipeline, no logramos ni siquiera hacer un setup simple de finetuninig DP. El motivo de la exigencia de recursos de este método, es la utilización del algoritmo **DP-SGD** un descenso estocastico pero con privacidad diferencial, es el cálculo gradientes por muestra (per-sample gradient) para hacer un recorte (clipping) que garanztiza el DP. 
- **Generación mediante 'sampleos privado' de documentos con Evolución Privada (PE)**: Guiándonos por el paper[^3] del método, buscamos adpatar su respectiva libreria[^4] desarrollada para los datos de Yelp, OpenReview y PubMed. Consideramos PubMed la mas cercana a nuestro tipo de datos, no por el domino, sino por contar con textos medianos (abstracts de medicina) sin ningun tipo de característica adicional como Yelp y OpenReview que tienen, por ejemplo, calificaciones. Por lo tanto utilizamos el desarrollo para PubMed como guía en la adaptación.

La idea del método de evolución privada (**PE**) desarrollada en el articulo, se basa de tres componentes fundamentales:

	- *RANDOM_API*: Genera $n$ muestras sintéticas con un modelo de lenguaje.
	- *DP_NN_HISTOGRAM*: Cada muestra privada vota por los documentos sintéticos más cercanos en un espacio donde son embebidos. Añade luego ruido Gaussiano a los votos para asegurar DP.
	- *VARIATION_API*: Hace variaciones de los documentos sintéticos generados inicialmente.

	Una vez generados los datos sintéticos inicales RANDOM_API luego se itera sobre DP_NN_HISTOGRAM y VARIATION_API la cantidad de iteraciones deseada. 
	
	Cabe destacar que, al ser la privacidad asegurada 'por fuera' de la generación, este algoritmo sólo solicita los pasos de inferencia de los modelos de lenguaje. Es por ello que, si tenemos acceso, podremos utilizar aun modelos de lenguaje cerrados. También resulta más eficiente, si la cantidad $n$ de muestras generadas no es gigante, este metodo para modelos abiertos con muchos parámetros en comparación al finetune con DP. 

	Debemos elegir entonces un modelo de lenguaje para generación, un modelo de sentence tranformer para relizar los embeddings y  un adecuado diseño de prompts para la generación y variación.  

	Para el modelo de generación de texto buscamos uno que soporte instrucciones para desarrollar mejor la tarea. Primero elegimos Lama3.2 1B Instruct, pero notamos que por mas de ser miltiligual, la generación terminaba siendo puramente en inglés. Para lograr la generación en español encontramos un Llama 7B Instruct finetuneado para español. Para el sentence tranformer elegimos primero [Sentence Transformer Paraphrase Multilingual](https://huggingface.co/sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2) y luego [Sentence Similarity Spanish](https://huggingface.co/hiiamsid/sentence_similarity_spanish_es). 
	
	Un objetivo inicial era poder crear documentos parecidos en formato al original, es decir, un membrete y la transcripción del informe junto al ruido provocado por el OCR. Para intentar eso utilizábamos los 19 documentos sin procesar. Para el prompt de genereacion dictamos: 
	>"Escribi un informe policial de un informante anonimo: " 

	Para la variacion:
	> "de forma casual",  "de forma creativa",  "de forma concisa"
	
	Notamos en este primer intento mucho apego al ruido terminando con documentos completamente inentendibles o en ruso.
	
	Abandonamos entonces el ambicioso objetivo de crear documentos completos. Para intentar mejorar la generación en una tarea mas específica de generar meramente informes, dividimos cada uno de los documentos en algunos más pequeños de 500 palabras. Primero identificamos dos palabras claves, que parecen ser generalmente bien leídas por el OCR, como divisores principales:
	
	- 'Agente' que aparece al inicio de cada membrete
	
	- 'Texto'  que aparece justo antes de los informes

	Como dijimos, el ruido ya lo consideramos fuera de nuestro alcance, por lo tanto, antes de dividir, porcedimos a eliminar las stopwords y minuscular. Una vez hecha esa división[^5] según las palabras 'clave' con el  [CharacterTextSplitter](	https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.CharacterTextSplitter.html#) (de langachain) seguimos subdividiendo cada una de ellas con el [RecursiveCharacterTextSplitter](https://api.python.langchain.com/en/latest/character/langchain_text_splitters.character.RecursiveCharacterTextSplitter.html#) hasta que todos los mini chunks tuvieran una longitud menor a 500. Resultando en $\sim 35000$ documentos distintos.


	El prompt inicial de generación estaba mal hecho, no es un informante el que escribe, sino más bien una transcripción por ello los cambiamos por:
	> 'Sos un policia espia. Por sospechas de subversivos fuentes anonimas te informan sobre intinerarios o movimientos de vecinos o conocidos. Escribi una transcripción un informe que te entrego un informante privado.'
	
	Dados los recursos disponibles hasta la fecha, nos acotamos a la generación de a lo sumo $n=4$ muestras iniciales (en el articulo hacen mínimo $n=2000$), los resultados con esta cantidad son poco prometedores, y como es esperable, a medida que más iteraciones hacíamos, peor eran los datos sintéticos generados.
	Para intentar mejorar la cantidad de samples $n$ y la cantidad iteraciones de variación intentamos usar los nuevos recursos obtenidos con Jupyter del CCAD pero por problemas de compatibilidad no pudimos lograrlo.

Un metodo descartdo por su demanda de recursos fue el post-procesado de OCR, que podrían resultar eficiente para la tarea de generar informes individuales.

## Evaluación (digamos)
La evaluación de las tareas finales, dado su corto alcance, fue predominantemente anecdótica.

Para comparar la similaridad de los datos sintéticos con los originales se utilizan las metricas sobre el espacio donde las embebemos, como el **FID** (Fréchet Inception Distance), la ***'kNN' F1-Score*** (F1 claculado sobre kNN datos originales de los datos generados y viceversa) ver A.5 en [^1]. Dada la pobre performance de los datos hasta ahora obtenidos los resultados son consecuentemente malos. Otra posible métrica es la **MAUVE** pero no la calculamos dada la poca cantidad de muestras generadas. 

Planteando un hipotético futuro donde consiguiéramos efectivamente datos sinteticos, la seguiriamos el camino del articulo .... , finetuneando algun modelo BERT sobre los datos sinteticos y evaluar la accuracy en la predicción de la próxima palabra. 

## Sugerencias
> ¿Se utilizarán otros documentos históricos para evaluar la herramienta? ¿Quizás la comparabilidad con el Archivo de Argentina sea un objetivo para una etapa posterior? ¿Quizás un objetivo intermedio sería consolidar una metodología que se pudiera aplicar al Archivo de Argentina para poder generar datos sintéticos a partir de esos datos?

Si terminásemos de desarrollar propiamente la generación de textos y contáramos con acceso a otros datos sería óptimo. Dado que nos restringimos solo a informes, podriamos ver como se comporta el mismo pipeline con un rollo cualquiera del Archivo del Terror.
Efectivamente es un objetivo ulterior poder generalizar a cualquier dataset del dominio.

## Trabajo a futuro

El principal plan a futuro es la generación de datos sintéticos, que fue la meta inicial de este proyecto. Para lograrlo, necesitaremos un mayor poder computacional y exploraremos otros modelos disponibles. 

Para esto, planteamos otros posibles trabajos que nos podrían ayudar a llegar a ese objetivo.
- **Extender el rango** de rollos explorados: No acotarnos solo a los archivos de informe, sino también a toda la variedad de documentos, con nuevas dificultades para el procesamiento, aunque ganando más datos.
- Investigar métodos más efectivos para la **separación de datos**: Para realizar tareas mas especificas, como la generación de informes individuales que intentamos performar, resulta porvechoso encontrar mejor formas de separar los textos según la composición de los rollos (membrete, informes, observaciones, etc) y así poder segmentar la generación de texto. 
- Explorar la herramienta **Opacus**: Como dijimos, esta librería permite convertir el proceso de finetunig en diferencialmente privado. Uno de los objetivos del primer articulo[^1]  es justamente integrarlo al porceso de finetuning, consecuentemente resultaría provechoso estudiar mejor el funcionamiento de esta librería y otras importantes como PyTorch.
- CCAD
- Realizar **finetuning a un modelo más reciente** para instrucciones en español: Finalmente intentamos generar textos con aug-pe[^2] en base a un finetune de intrucciones en español para un Llama 2, podriamos realizar un fintune sobre intrucciones en español para modelos mas actuales como algun Llama 3.
- Probar la generación de datos con **modelos cerrados**: Solo adaptamos la parte de modelos abiertos de aug-pe[^2] , podríamos probar también adaptar el mecanismo de generación en base a modelos cerrdos, como GPT 3.5. capaz de tomar mejores intrucciones por ejemplo. 
- Variar y mejorar los **prompts** utilizados: La exploración del prompting no fue profusa y es altamente no trivial, habróa que investigar de manera mas detallada la ingeniería de los prompts pasados a cada modelo en particular. Si se segmenta la generación de datos sintéticos, también habría que explorar para cada una de las tareas propuestas. Se pueden investigar recursos como el Chain of Thought para mejorar la geración, lo que requeriria una modificación notoria del algoritmo adaptado.
- **Identificación de Identidades y Combinaciones Porhibidas**: Una vez generados los datos sintéticos de manera 'correcta', identificar entidades que puedan estar pesentes los nuevos datos para evlauar la correcta privacidad de generación. De la misma manera se pueden buscar ciertas combinaciones o secuencias de palbaras encontradas en los textos originales que, por más que no parezcan una amenaza, puedan resultar útiles para performar tareas de reidentificación,
- **Evaluación downstream intermedia en BERT** : Como dijimos en la evaluación, basándonos en los papers[^1] [^2], ......
- Performar **ataques maliciosos de reidentificación** para evaluar la efectividad del modelo.
- Investigar la aplicabilidad del modelo a otros **datasets del mismo dominio**: Como una tarea final luego de garantizar una calidad prudente en la genración de datos sinteticos para el Archivo del Terror, buscar acceso a datos del archivo de la memoria de Argentina para analizar si los procesos llevados a cabo pueden ser transladables.
## Referencias
[^1]: [Synthetic Text Generation with Differential Privacy - A simple and Practical Recipe](https://arxiv.org/pdf/2210.14348)
[^2]: [dp-transformers](https://github.com/microsoft/dp-transformers)
[^3]: [Differentially Private Synthetic Data via Foundation Model APIs 2: Text](https://arxiv.org/pdf/2403.01749)
[^4]: [aug-pe](https://github.com/AI-secure/aug-pe)
[^5]: [Notebook de Text Spliting](https://github.com/FullStackRetrieval-com/RetrievalTutorials/blob/main/tutorials/LevelsOfTextSplitting/5_Levels_Of_Text_Splitting.ipynb)

