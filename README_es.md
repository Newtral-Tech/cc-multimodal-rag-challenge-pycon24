# Verificación automática

Una ClaimReview es un estándar de metadatos para capturar información relevante sobre una verificación de datos (por ejemplo, título, imagen, calificación, afirmación…). Debido a que el documento de revisión combina texto y recursos multimedia, un buscador o chatbot de asistencia debe tener en cuenta no sólo la información textual, sino también toda la información que se puede extraer de los recursos multimedia (en nuestro caso, restringida a recursos de imagen).

En este contexto, el objetivo del desafío es construir un sistema de verificación automática capaz de razonar sobre la veracidad de una afirmación a partir de una base de datos de verificaciones.

En lo técnico, el reto propone la construcción de un sistema MultiModal RAG (Retrieval-Augmented Generation) que, ante una pregunta formulada en texto o imagen:
- Recupere los candidatos más relevantes y
- genere una respuesta, considerando, en ambas fases, que las imágenes adjuntas al documento de claim review también pueden proporcionar información relevante.

El espacio de búsqueda será un extracto de nuestra base de datos de verificación, con registros que incluyen el claim review en sí mismo, datos textuales derivados como resumen o palabras clave, e imágenes que contextualizan o complementan el artículo (ver más abajo la descripción del conjunto de datos).

NOTA: Si necesitas más información sobre el esquema ClaimReview, por favor accede a [Fact Check (ClaimReview) Markup for Search](https://developers.google.com/search/docs/appearance/structured-data/factcheck?hl=es)..

## Ideas
- Uso de frameworks como LangChain o LlamaIndex
- Para la multimodalidad, uso de embeddings multimodales o embeddings de texto incluyendo la descripción de la imagen
- La fase de recuperación podría evaluarse en función de la coincidencia entre los términos de búsqueda (columna de afirmación no verificada) y la afirmación (similitud = 1, coincidencia; similitud = 0, sin coincidencia)

## Criterios de evaluación
Se asignarán puntos en base a los siguientes criterios de evaluación.

- 30 - Soporte multimodal en la recuperación de información: busca documentos candidatos atendiendo a la información del texto y de la imagen
- 20 - Desempeño: se lanzan 5 preguntas y se asignan los puntos en la medida que estas respuestas incluyen información de alguno de los contenidos filtrados
- 15 - Soporte multimodal en los elementos de búsqueda: permite incluir como entrada una imagen
- 10 - Evaluación: se implementa alguna métrica de evaluación válida para la recuperación y/o la generación
- 10 - Estrategia de reranking de documentos candidatos: incorporar algún modelo o estrategia para rerankear los candidatos
-  5 - Combinación de documentos en la respuesta: usar (si procede) más de un documento candidato para generar la respuesta
-  5 - Trazabilidad: se incluye algún método o herramienta de trazabilidad para depurar, monitorizar o auditar el uso de LLMs controlar el gasto de la solución en términos de uso de servicios externos o de tiempo 
-  5 - Control de la respuesta: el sistema responde que no tiene suficiente información para responder si no encuentra documentos relevantes en el espacio de búsqueda

En caso de empate, se definirá una batería de preguntas adicional para evaluar el "desempeño avanzado" que podrá aumentar el puntaje en un máximo de 30 puntos.

## Los datos
El conjunto de datos adjunto contiene afirmaciones verificadas/revisadas disponibles en nuestra base de datos de revisiones de afirmaciones.
Incluye información contextual sobre estas verificaciones/revisiones, como texto completo, resumen, URL, descripción, palabras clave, fechas
o recursos multimedia adjuntos (imagen, video). Además, cada registro incluye una relación de similitud (establecida por humanos) que indica
la coincidencia (“similitud” = 1) o no coincidencia (“similitud” = 0) entre la revisión de la afirmación y un término de búsqueda (o afirmación
no verificada) proporcionado en el campo de "afirmación no verificada".

### Descripción de campos
#### Basic info
* **reviewed claim** *(str, multilingual)*: la "afirmación" a revisar
* **title** *(str, multilingual)*: título del artículo
* **text** *(str, multilingual)*: text original. Puede contener "ruido" en el sentido de incluir párrafos irrelevantes por errores de scrapeo (texto de menús/sitemaps, espacios en blanco, etc.)
* **summary** *(str, multilingual)*: resumen generado desde el texto original

#### Multimedia resources
* **cr_image** *(str, url)*: (si existe) imagen adjunta al claim review
* **meta_image** *(list[str], url)*: (si existen) imágenes enlazadas extraídas desde el objeto meta y/o el json+ld
* **movies** *(list[str], url)*: (si existen) vídeos enlazados desde el objeto meta y/o el json+ld

#### Other metadata
* **meta_description** *(str, multilingual)*: descripción extraída del objeto meta y/o el json+ld
* **kb_keywords** *(list[str], multilingual)*: keywords generadas desde el texto original (ngrams range 1-3) using KeyBert
* **meta_keywords** *(list[str], multilingual)*: keywords extraídas desde el objeto meta y/o el json+ld
* **url** *(string, url)*: url del claim review
* **domain** *(string, url)*: dominio (del publisher)
* **published** *(datetime)*: fecha de publicación
* **cm_authors** *(list[str], multilingual)*: una lista de autores (extraída desde el objeto meta y/o el json+ld)
* **cr_author_name** *(str): nombre del autor del calim review (el fact-checker)
* **cr_country** *(string)*: país de la publicación
* **meta_lang** *(string, ISO Code)* idioma del claim nativo extraído del objeto meta y/o el json+ld

#### Similarity relation 
* **unverified claim** *(str, multilingual (en, es))*: claim no verificado / término de búsqueda
* **similarity** *(int)*: etiqueta indicando una relación positiva (1) o negativa (0) entre un término de búsqueda (o claim no verificado) y el claim verificado. match between a search term
(or unverified claim) and the verified claim

## Recursos
[An Easy Introduction to Multimodal Retrieval-Augmented Generation](https://developer.nvidia.com/blog/an-easy-introduction-to-multimodal-retrieval-augmented-generation/)

[Multi-modal applications - LlamaIndex](https://docs.llamaindex.ai/en/stable/use_cases/multimodal/)

