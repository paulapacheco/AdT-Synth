# Adaptación de aug-pe
En la notebook hicimos una adaptación de la libreria [aug-pe](https://github.com/AI-secure/aug-pe) guiándonos en mayor parte por el diseño para los datos de pubmed, los más parecidos a los del Archivo del Terror.
En primer lugar está la carpeta [apis](https://github.com/AI-secure/aug-pe/tree/main/apis) de donde tomamos 
- api.py - Donde se define la clase API
- hf_api.py - Donde redefine la clase API para la utilización de modelos abiertos de hugging face. Encargada de definir la generación y variación de textos.
- utils.py - Donde se definen los prompts de variación.

Luego seguimos con la carpeta  [dpsda](https://github.com/AI-secure/aug-pe/tree/main/dpsda) de donde tomamos:
- feature_extractor.py - Donde se define el embedding con el SentenceTransformer.
- dp_counter.py - Donde se define el histograma privado.
- data_loader.py - Donde se define la carga y sample de datos.
- metrics.py - Donde se definen varias métricas. 
- logging.py - Porsicaso.

En Noise Level Calculation, adaptamos la [notebook](https://github.com/AI-secure/aug-pe/blob/main/notebook/dp_budget.ipynb?short_path=ab58a17) encargada de darnos el ruido necesitado dado un $\epsilon$ elegido.

Y por último el [main.py](https://github.com/AI-secure/aug-pe/blob/main/main.py) donde se define el algoritmo de generación.
