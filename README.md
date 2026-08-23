# Analizador Exploratorio de Entrevistas: NLP + Frecuencia + TF-IDF


[![Abrir en Google Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/drive/1MLS3ixyeURe-mJOYyiP7RYwuY5IC1lBf?authuser=3#scrollTo=_HFVCNkFz4gO)   **Abrir en Google Colab**


Herramienta breve y reproducible para realizar una **exploración asistida por computador de entrevistas cualitativas en formato `.txt`**. El proyecto combina procesamiento de lenguaje natural (NLP), análisis de frecuencia y TF-IDF para identificar conceptos recurrentes, observar su presencia entre entrevistas y generar una primera matriz de resultados para apoyar la interpretación cualitativa.

Está diseñado para ejecutarse directamente en **Google Colab**, sin requerir una infraestructura compleja. El usuario carga sus transcripciones, el script procesa los textos y genera un archivo Excel junto con una visualización de los conceptos más recurrentes.

> **Propósito metodológico:** el proyecto funciona como una herramienta de exploración y apoyo al análisis cualitativo. No reemplaza la lectura contextual, la codificación interpretativa ni la construcción de categorías teóricas por parte del investigador.

---

## 1. ¿Qué problema aborda?

Cuando se trabaja con varias entrevistas, una primera revisión puede requerir localizar rápidamente expresiones recurrentes y observar qué conceptos aparecen transversalmente en el corpus.

Este proyecto busca responder de forma sencilla preguntas exploratorias como:

- ¿Qué conceptos aparecen con mayor frecuencia?
- ¿En cuántas entrevistas aparece cada concepto?
- ¿Qué conceptos tienen mayor relevancia léxica según TF-IDF?
- ¿Qué expresiones pueden constituir posibles núcleos temáticos para una revisión cualitativa posterior?

El proyecto fue probado inicialmente con **seis entrevistas provenientes de una investigación cualitativa**, utilizadas como corpus de demostración.

---

## 2. Flujo general

```text
Archivos .txt
     ↓
spaCy (es_core_news_sm)
     ↓
Tokenización + lematización + POS tagging
     ↓
Filtrado de ruido lingüístico
     ↓
Extracción de pares conceptuales
     ↓
Frecuencia + cobertura entre entrevistas
     ↓
TF-IDF
     ↓
Excel + visualización
```

---

## 3. Funcionalidades principales

### Procesamiento lingüístico con spaCy

El script utiliza `es_core_news_sm` para procesar los textos en español y obtener, entre otros elementos:

- lemas;
- categorías gramaticales (POS);
- segmentación de oraciones;
- identificación de palabras funcionales y signos de puntuación.

Esto permite ir más allá de una simple extracción de palabras consecutivas.

### Extracción de conceptos mediante patrones POS

Uno de los principales ajustes metodológicos consiste en **restringir los pares de palabras a combinaciones lingüísticamente plausibles**.

Actualmente se consideran patrones como:

```text
NOUN + NOUN
NOUN + ADJ
ADJ + NOUN
VERB + NOUN
VERB + ADJ
```

Por ejemplo, pueden aparecer expresiones como:

```text
industria pesquera
identidad territorial
harina pescado
sentir orgulloso
```

En cambio, combinaciones generadas por el texto de forma incidental, como `ver él`, `decir él` o `dar él`, pueden excluirse mediante la combinación de reglas POS, stopwords y verbos de ruido.

### Lematización

Las palabras se convierten a su lema para reducir variaciones morfológicas. Esto permite agrupar, por ejemplo, diferentes formas verbales bajo una misma unidad léxica cuando spaCy las reconoce como pertenecientes al mismo lema.

### Control de ruido específico de entrevistas

El código incorpora dos mecanismos configurables:

- `STOP_LEMAS_EXTRA`: términos funcionales que pueden persistir después del filtrado general.
- `VERBOS_RUIDO`: verbos frecuentes en la oralidad que pueden producir combinaciones poco informativas para el objetivo exploratorio.

Además, existe una lista `ruido_conocido` para excluir expresiones específicas del corpus.

### Frecuencia de conceptos

Se calcula la cantidad total de apariciones de cada concepto dentro del corpus.

### Cobertura entre entrevistas

Además de la frecuencia absoluta, se calcula **en cuántas entrevistas aparece cada concepto**.

Esta medida es particularmente útil en un corpus pequeño porque permite diferenciar entre:

- un concepto muy repetido dentro de una sola entrevista;
- un concepto transversal a varias entrevistas.

Con el corpus inicial de seis entrevistas, `min_df=2` exige que un concepto aparezca en al menos **dos entrevistas distintas** para entrar al análisis.

### Relevancia mediante TF-IDF

Se calcula un puntaje TF-IDF para complementar la frecuencia. El resultado utilizado en la tabla final es el promedio del TF-IDF entre documentos.

Este indicador debe interpretarse como una medida de **relevancia/discriminación léxica**, no como una medida directa de importancia sociológica.

### Exportación a Excel

El archivo generado es:

```text
Matriz_Sociologica_Resultados.xlsx
```

Incluye dos hojas principales:

#### `Resultados`

Contiene los conceptos seleccionados y sus indicadores:

| Variable | Descripción |
|---|---|
| `Concepto` | Expresión o par conceptual extraído |
| `Frecuencia total` | Número total de apariciones en el corpus |
| `Entrevistas con presencia` | Número de entrevistas donde aparece |
| `TF-IDF promedio` | Relevancia léxica promedio |

#### `Por entrevista`

Matriz concepto × entrevista que permite observar la distribución de cada concepto entre los distintos casos.

### Visualización

Se genera un gráfico horizontal con los diez conceptos más frecuentes del corpus.

La visualización está pensada como una salida rápida para presentación y exploración inicial, no como una visualización estadística avanzada.

---

## 4. Tecnologías utilizadas

- **Python**
- **Google Colab**
- **spaCy** — procesamiento lingüístico en español
- **pandas** — manipulación de datos
- **scikit-learn** — `CountVectorizer` y `TfidfTransformer`
- **matplotlib** — visualización
- **openpyxl** — exportación de resultados a Excel

---

## 5. ¿Cómo utilizarlo?

### Paso 1. Google Colab

Abrir el notebook mediante el botón "Abrir en Google Colab" que aparece al inicio del README. 

No requiere una instalación local de Python

### Paso 2. Ejecutar el script

Ejecutar todas las celdas.

El script instala el modelo lingüístico de spaCy automáticamente:

```python
!python -m spacy download es_core_news_sm -q
```

### Paso 3. Seleccionar las entrevistas

Cargar los .txt:

```text
Selecciona tus archivos .txt:
```

### Paso 4. Revisar los resultados

Al finalizar, se descarga:

```text
Matriz_Sociologica_Resultados.xlsx
```

Además, Colab muestra el gráfico de conceptos recurrentes y una tabla textual con el Top 10.

---

## 6. Recomendaciones para utilizarlo con otros corpus

El comportamiento del análisis depende fuertemente de las características del conjunto documental. Antes de reutilizar el script conviene revisar estas configuraciones.

### Cantidad de entrevistas

El valor actual:

```python
min_df=2
```

funciona como una decisión razonable para un corpus pequeño de seis entrevistas: exige presencia en al menos dos documentos.

Para corpus mayores conviene revisar ese umbral. Por ejemplo, con decenas o cientos de entrevistas puede ser preferible elevar el número mínimo de documentos o adaptar la regla a una proporción del corpus.

### Stopwords y vocabulario específico

`STOP_LEMAS_EXTRA` debe adaptarse al lenguaje del corpus. Una palabra irrelevante en una investigación puede ser analíticamente importante en otra.

Por eso se recomienda **no ampliar esta lista de forma automática**. Cada exclusión debería responder a una razón analítica clara.

### Verbos de ruido

La lista `VERBOS_RUIDO` está pensada principalmente para entrevistas y transcripciones orales. En otros tipos de textos —por ejemplo, documentos institucionales, artículos de prensa o respuestas abiertas escritas— puede ser innecesaria o incluso perjudicial.

### Etiquetas de hablantes

Si las transcripciones incluyen estructuras repetitivas como:

```text
ENTREVISTADOR:
ENTREVISTADO:
```

conviene limpiar o revisar esas etiquetas antes del procesamiento, especialmente si contienen términos que podrían aparecer muchas veces.

### Muletillas y transcripción oral

En entrevistas reales pueden persistir expresiones como:

```text
tipo
cosa
como que
igual
onda
po
```

No todas deben eliminarse. Algunas pueden ser ruido transcripcional y otras pueden constituir características lingüísticas relevantes. Se recomienda revisar el corpus antes de decidir qué excluir.

### Textos muy extensos

El modelo `es_core_news_sm` permite mantener una solución liviana, pero los corpus muy grandes pueden requerir procesamiento por lotes y estrategias de optimización de memoria.

---

## 7. Recomendaciones metodológicas para interpretar los resultados

El resultado debe entenderse como **evidencia exploratoria**, no como una codificación automática definitiva.

Un concepto con alta frecuencia puede corresponder a:

- un tema realmente transversal;
- una característica particular del lenguaje del entrevistado;
- una pregunta repetida por el entrevistador;
- una expresión propia de la dinámica de entrevista;
- una palabra necesaria para hablar de determinado tema.

Por esta razón, la secuencia recomendada es:

```text
Resultado computacional
        ↓
Revisión del contexto original
        ↓
Comparación entre entrevistas
        ↓
Codificación / interpretación cualitativa
        ↓
Construcción de categorías analíticas
```

Una buena práctica consiste en volver desde el concepto detectado hacia los fragmentos originales donde aparece y revisar su **contexto discursivo**.

---

## 8. Limitaciones actuales

El proyecto mantiene deliberadamente un nivel de complejidad bajo para facilitar su comprensión, ejecución y presentación. Por ello, no incorpora actualmente:

- embeddings semánticos;
- clustering automático de documentos;
- topic modeling;
- modelos Transformer/BERT;
- detección automática de temas latentes;
- análisis de relaciones sintácticas complejas;
- codificación cualitativa automatizada.

Estas técnicas pueden complementar el proyecto en futuras versiones, pero no son necesarias para su objetivo principal: disponer de una herramienta **rápida, transparente y reproducible para exploración inicial de entrevistas**.

---

## 9. Posibles extensiones futuras

### Versión 2 — Análisis contextual

Incorporar una salida que muestre los fragmentos de entrevista donde aparece cada concepto, permitiendo pasar directamente de la frecuencia al contexto discursivo.

### Versión 3 — Comparación semántica

Agregar embeddings de frases o documentos para identificar conceptos similares que no compartan exactamente las mismas palabras.

### Versión 4 — Modelos de lenguaje

Comparar el enfoque léxico actual con un modelo Transformer en español, evaluando ventajas y limitaciones de cada estrategia.

### Versión 5 — Interfaz para investigadores

Convertir el notebook en una interfaz sencilla para que un investigador pueda cargar entrevistas, configurar parámetros y descargar resultados sin editar el código.

---

## 10. Valor del proyecto

El principal aporte del proyecto no está en automatizar completamente el análisis cualitativo, sino en mostrar cómo una metodología tradicional de investigación social puede complementarse con herramientas de ciencia de datos y NLP.

La propuesta combina tres niveles:

**Investigación cualitativa**  
Comprensión contextual e interpretación de discursos.

**Procesamiento de lenguaje natural**  
Lematización, POS tagging y extracción estructurada de conceptos.

**Análisis cuantitativo exploratorio**  
Frecuencia, cobertura entre entrevistas y TF-IDF.

Esto permite utilizar la tecnología como una herramienta de apoyo para **ordenar, explorar y contrastar grandes volúmenes de texto sin sustituir la interpretación del investigador**.

---

## 11. Estructura del repositorio

```text
analizador-entrevistas-nlp/
│
├── README.md
│
├── notebooks/
│   └── Analizador_Entrevistas_NLP.ipynb
│
├── src/
│   └── Analizador_Entrevistas_NLP_Optimizado_Colab.py
│
└── examples/
    └── Matriz_Sociologica_Resultados_ejemplo.xlsx
```

Se recomienda **no publicar transcripciones reales que contengan información identificable o sensible**. Para GitHub y el portafolio, es preferible utilizar textos anonimizados, sintéticos o un corpus público con licencia compatible.

---

## Licencia y uso

Este proyecto puede adaptarse para fines académicos, metodológicos y demostrativos. Al utilizar corpus externos, se deben respetar las condiciones de acceso, anonimización, privacidad y licencia correspondientes.

---

## Autor

**Cristian Riquelme Fernández**  
Sociólogo · Analista de Datos · Investigación Aplicada · NLP
