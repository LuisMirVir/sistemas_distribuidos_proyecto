# Impacto del Preprocesamiento de imagenes en el desempeño de una CNN para clasificación de flores

En este proyecto se propono una pipeline de preprocesamiento de un dataset de imagenes de flores, para evaluar como afecta el rendimiento en el entrenamiento de una CNN entrenada ara clasificar 5 tipos de flores del dataset [Flowers Recognition](https://www.kaggle.com/datasets/alxmamaev/flowers-recognition).
Para ello nos basamos en la proyeccion HCL para maximizar el contraste, realizamos una umbralización con Otsu y realizamos un refinamiento morfologico sobre el dataset original para posteriormente entrenar la CNN con este nuevo dataset y compara los resultados con los obtenidos sin el preprocesmaiento.

---

## Propuesta de Preprocesamiento

El objetivo de nuestra propuesta es  **aisalr la flor y eliminar el ruido de fondo (hierba, hojas, etc.)** y de esta forma permitir que la CNN se concentre exclusivamente en el aprendizaje de las caracteristicas de la flor y no en el fondo de las mismas.

El flujo del procesamiento aplicado a cada imagen consiste en lo siguiente:

1. **Filtro de mediana (3x3):** Se realiza un suavizado inicial para eliminar el ruido sin difuminar los bordes de la flor
2. **Proyeccion HCL a escala de grices de máximo contraste (HCM):**
   - proyecta la imagen **BGR** mediante una combinación lineal optimizada $I = w_1 B + w_2 G + w_3 R$
   - Los pesos optimos se encuantran con el algoritmo Nelder-Mead para maximizar la desviación estandar de la imagen en grises divididos por su rango
3. **Umbralización de Otsu con Offset ($p = -0.2$):** 
   - Se binariza la proyección HCM encontrando el umbral óptimo.
   - Se aplica un offset de $-0.2$ para asegurar que se conserven los bordes de los pétalos.
4. **Relleno de Huecos:** Se rellenan los espacios internos vacíos dentro de la máscara
5. **Limpieza Morfológica:**
   - Eliminamos los objetos aislados pequeños en el fondo con un umbral de área de 200 píxeles.
   - Rellenamos los huecos oscuros internos restantes con un umbral de área de 1000 píxeles.
6. **Enmascaramiento Bitwise:** Aplicamos la máscara binaria final sobre la imagen BGR original, resultando en la flor a color sobre un fondo negro absoluto.
7. **Redimensionamiento :** Ajustamos las imágenes procesadas al tamaño necesario para la CNN ($150 \times 150$ píxeles).

En el diagrama 
