# Post-analysis
--------------


# Resultados y discusión

A partir de la señal BOLD preprocesada y separada por bloques con el script `chunks.ipynb`, se obtuvieron 4 features dados por su tipo (Functional Connectivity, ReHo) y la forma de segmentar el área de interés (Parcellation-Schaefer100x17 y Coordenadas CogAR-5mm). 
Para el caso de las matrices de correlación, nos quedamos con el promedio por columna para obtener features de tamaño 100 (teniendo así menos features que datos).
Todas las features obtenidas pueden accederse desde la carpeta `/Features` del repositorio.


## Problemas one vs one:

Usando Grid Search hicimos búsqueda de hiperparámetros para los algoritmos de SVM y Random Forest. 
Concretamente, para SVM tomamos los kernels RBF, Sigmoid y Linear. Consideramos `C=[.001,.01,0.1,1,10,100]` y, para los no lineales, gamma en `[1e-1, 1e1, 1e3]`.
Para Random Forest se tomaron `n_estimators=[10,20,30,40,50,100]` , `max_depth=[3,4,5,6]` y `random_state=[42, 1337, 95]`.
Finalmente consideramos un modelo Dummy para evaluar cuanto mejor que la chance es nuestro clasificador (0.5 pues las clases están balanceadas).
Mediante 5-fold stratified nested cross-validation hicimos el proceso de model selection y evaluamos la capacidad de generalización de los modelos. Habíamos considerado agrupar por sujeto o por run para evitar correlaciones espúreas, pero los resultados obtenidos no indicaron que esto fuera necesario.

El pickle de los resultados puede descargarse de los siguientes links:
* one vs all: https://drive.google.com/file/d/1f7p08Toyf2e4VB-CRlSTGinkoOb6tnIe/view?usp=sharing
* one vs one: https://drive.google.com/file/d/1PD3QMKJlLBFtWCqtl9DDoBwHl-VKYUpE/view?usp=sharing

Por cada subproblema de clasificación binaria tomado entre cada par de emociones, y para cada tipo de feature extraída, evaluamos los resultados observando la distribución de los resultados de validation y train sobre cada uno de los folds dados por el outer cv. Mostramos un ejemplo concreto en la Figura 1, donde particularmente se ve que Random Forest muestra signos más claros de overfitting comparado con SVM.

![bokeh_plot](https://hackmd.io/_uploads/S1G9YfnNR.png)

*Figura 1: Distribución Accuracy para SVM y Random Forest en cross validation para el sub-problema Happy vs Sad. Por cada partición del outer 5-fold se obtiene un `best accuracy score` (tanto para train como validation) a partir de la selección del mejor modelo del inner 5-fold. 

---


A partir de la observación de los resultados de los distintos subproblemas, reportamos 3 de ellos (combinaciones con Happy) habiendo notado pocas diferencias en los resultados con los resantes. Elegimos mostrar los resultados obtenidos para grouped K-fold por sujeto pero, como se dijo previamente, no encontramos diferencia significativa con respecto a no hacerlo. 


|         |             |  SVM     |          |          |      Random Forest    |           |
|:---------|:------------|:--------:|:--------:|:--------:|:---------------------|:----------|
|          |    Min      |  Med     |   Max    |   Min    |      Med             |    Max    |
| FC-Parcell  |   0.45    |   0.51  |   0.58   |   0.36   |     0.45             |    0.56   |
| FC-Coord   |    0.4     |   0.5   |   0.6    |   0.36   |     0.48             |    0.51   |
| Reho-Parcell   |  0.51    |   0.56  |   0.61   |   0.41   |     0.45             |    0.61   |
| Reho-Coord   |    0.48   |   0.51  |   0.58   |   0.43   |     0.48             |    0.7    |

*Tabla 1: Accuracy para SVM y Random Forest en cross validation para el sub-problema Happy vs Sad. Por cada partición del outer 5-fold se obtiene un Best Accuracy score (en validation) a partir de la selección del mejor modelo del inner 5-fold. Se reportan mínimo, mediana y máximo entre los 5 resultados del outer 5-fold.*


---


|         |             |  SVM     |          |          |      Random Forest    |           |
|:---------|:------------|:--------:|:--------:|:--------:|:---------------------|:----------|
|          |    Min      |  Med     |   Max    |   Min    |      Med             |    Max    |
| FC-Parcell  |   0.51    |   0.55  |   0.68  |   0.43  |     0.48             |    0.56  |
| FC-Coord   |    0.45     |   0.51   |   0.61   |   0.43   |     0.47             |    0.5  |
| Reho-Parcell   |  0.41    |   0.51  |   0.56   |   0.4   |     0.55             |    0.61   |
| Reho-Coord   |    0.46   |   0.51  |   0.53   |   0.43   |     0.5             |    0.53    |

*Tabla 2: Accuracy para SVM y Random Forest en cross validation para el sub-problema Happy vs Angry. Por cada partición del outer 5-fold se obtiene un Best Accuracy score a partir de la selección del mejor modelo del inner 5-fold. Se reportan mínimo, mediana y máximo entre los 5 resultados del outer 5-fold.*

---


---


|         |             |  SVM     |          |          |      Random Forest    |           |
|:---------|:------------|:--------:|:--------:|:--------:|:---------------------|:----------|
|          |    Min      |  Med     |   Max    |   Min    |      Med             |    Max    |
| FC-Parcell  |   0.5    |   0.55  |   0.58  |   0.45  |     0.48             |    0.58  |
| FC-Coord   |    0.5     |   0.58   |   0.62   |   0.53   |     0.55             |    0.56  |
| Reho-Parcell   |  0.51    |   0.53  |   0.68   |   0.45   |     0.55             |    0.61   |
| Reho-Coord   |    0.45   |   0.53  |   0.58   |   0.45   |     0.5             |    0.53    |

*Tabla 3: Accuracy para SVM y Random Forest en cross validation para el sub-problema Happy vs Neutral. Por cada partición del outer 5-fold se obtiene un Best Accuracy score a partir de la selección del mejor modelo del inner 5-fold. Se reportan mínimo, mediana y máximo entre los 5 resultados del outer 5-fold.*


---

En función de los resultados entendemos que:

* Para cada tipo de feature, la mediana de accuracy de los SVM, como se ve en las Tablas 1 a 3, es en general superior a la de Random Forest. Para las distintas pruebas, observamos que las medianas del accuracy en train para Random Forest suelen estar cerca de 0.9 mostrando signos de overfitting más pronunciados que los SVM, cuyas valores nunca sobrepasaron 0.75.

* Observando los resultados de las Tablas 1 a 3, la mediana del accuracy por fold de cada modelo en ningún caso sobrepasó 0.6. Comparado con un proceso aleatorio, realmente no pensamos que sea significativa la diferencia.

* En definitiva, para este tipo de problema no volveríamos a elegir estas mismas features, posiblemente el enfoque en sí no sea fructífero.


---

También intentamos abordar el problema one vs all siguiendo el mismo procedimiento que en one vs one, pero en este caso son 4 experimentos (1 por emoción) y tenemos más etiquetas de la clase negativa. Al estar desbalanceados los datasets usamos balanced accuracy como métrica principal. Lo que ocurrió fue que al haber más datos disminuyó la varianza y hubo mucho menos overfitting (salvo en con algún fold particular con algún feature particular), pero se hizo más evidente todavía que a partir de estas features en estos datos no es posible predecir qué emoción tenía la cara que estaban viendo los sujetos. Obtuvimos 0.5 de balanced accuracy para todos los folds (de validation), para todos los features, para todas las emociones.
