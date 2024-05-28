# Pre-analysis
--------------

# Introducción:

* Hipótesis: A partir del refinamiento de señales BOLD mediante ALFF(*) sobre tareas activas, se puede predecir, con una performance superior a la chance estimada empíricamente, fenómenos de decodificación visual con distinto grado de complejidad: Desde la detección facial a la percepción de una emoción. Nos centramos en esta última.

* Nos insipiramos en un trabajo que también analiza la percepción emociones a partir de señales fMRI: *Localizing Brain Function Based on Full Multivariate Activity Patterns: The Case of Visual Perception and Emotion Decoding* https://www.biorxiv.org/content/10.1101/2021.04.04.438425v3


---

# Métodos:
 
## Dataset:

El dataset planteado en el paper, el cual tiene derivatives ya computadas de fmriprep, puede encontrarse en https://openneuro.org/datasets/ds003548/versions/1.0.1. A continuación se describe el diseño del experimento principal planteado por los autores

![image](https://hackmd.io/_uploads/SJvSRzbQ0.png)

* Se trata de 16 sujetos, cada uno de los cuales realiza 5 runs/sequences donde se les presentan rostros con distintas emociones (happy, sad, anger, neutral), imágenes de pseudo-faces y low stimulation (fixation cross) mientras hacen una tarea ortogonal de detección del sexo de la persona. Los runs se dividen en 12 bloques que duran 30 segundos cada uno y durante los cuales se presentan 10 caras. Se muestran dos bloques por clases.

    * Cada run debería contener 180 volúmenes, con una duración total de 360 segundos.
    * Cada clase tiene 30 volúmenes por cada run.


---

## Features:


* Por cada sujeto, consideramos la señal BOLD de cada run y la agrupamos por bloque. Obtenemos así, dos bloques por emoción con su respectiva señal BOLD la cual contiene 15 volumenes.
* Dado un ROI/parcellation a definir aplicamos ALFF(*) a cada señal y se obtiene un feature vector para cada una.
* Tendríamos 10 muestras por emoción y por sujeto, un total de 160 por clase.

---

## Modelos

Vamos a entrenar dos familias de modelos (SVM y RandomForest) para implementar un clasificador para el problema multiclase de clasificación de 4 emociones a partir de las features obtenidas previamente. 

* Modelos a seleccionar mediante Grid Search: 
    * SVM, variando principalmente los hiperparámetros:
        *  kernel (dependiendo del kernel, el gamma también)
        *  C 
    * Random Forest, variando principalmente los hiperparámetros:
        * n_estimators
        * max_depth
        * random_state


* Evaluación: 
    * Para hacer model selection y estimar el error de generalización conjuntamente, haremos Nested Stratified 5-fold Crossvalidation:
        * Reportamos principalmente accuracy porque estratificamos por clase, y además podemos agrupar por sujeto para evitar correlaciones espúreas.
        * Además, similar a los autores, nos gustaría poder reportar test de hipotesis: p-values para accuracy de clasificación
            * El accuracy anterior, según el paper, es comparado *"against an empirical null model in a non-parametric rank-based test, by estimating the probability distribution of average classification accuracy given H0 via Monte-Carlo simulations...Then, a p-value for that particular subject and class combination can be calculated as the proportion of random results equal to or greater than the original classification accuracy"*



---
Dudas (*):
* No nos queda claro cual podría ser la feature más conveniente para este contexto. Proponemos ALLF, pero a priori conectividad funcional sería interesante también.
* Tampoco cual parcellation tomar.



