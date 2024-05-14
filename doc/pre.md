# Pre-analysis
--------------
# Introducción:

* Hipótesis: Particionar el cerebro en regiones anatómicas permite predecir, con una performance superior a la chance estimada empíricamente, fenómenos de decodificación visual con distinto grado de complejidad: Desde la detección facial a la percepción de una emoción.

* En particular, buscamos replicar alguno de los resultados relacionados unicamente a la percepción de emociones provistos en este paper: *Localizing Brain Function Based on Full Multivariate Activity Patterns: The Case of Visual Perception and Emotion Decoding* https://www.biorxiv.org/content/10.1101/2021.04.04.438425v3


---

# Métodos:
 
## Dataset:

El dataset planteado en el paper, el cual tiene derivatives ya computadas de fmriprep, puede encontrarse en https://openneuro.org/datasets/ds003548/versions/1.0.1. A continuación se describe el diseño del experimento principal planteado por los autores

<p align="center">
  <img src="https://hackmd.io/_uploads/SJvSRzbQ0.png?raw=true" alt="Sublime's custom image"/>
</p>

* Se trata de 16 sujetos, cada uno de los cuales realiza 5 runs/sequences donde se les presentan rostros con distintas emociones (happy, sad, anger, neutral), imágenes de pseudo-faces y low stimulation (fixation cross) mientras hacen una tarea ortogonal de detección del sexo de la persona. Los runs se dividen en 12 bloques que duran 30 segundos cada uno y durante los cuales se presentan 10 caras. Se muestran dos bloques por clases.

    * Cada run debería contener 180 volúmenes, con una duración total de 360 segundos.
    * Cada clase tiene 30 volúmenes por cada run.

## Features y modelos:

* Se entrenaría un problema binario dado por cada combinación de clases (happy vs sad, happy vs neutral, etc) y sujeto. Para cada clase se toman las 5 runs, dando un total de 150 volúmenes (120 para train y 30 para test).

* El modelo SVM entrenado por los autores, según lo que entendemos, recibe "señales/features" del cerebro BOLD-segmentado por ROIs que luego de entrenarse darían los pesos para armar la clusterización. 
    * Suponemos que estas features pueden obtenerse a partir de los datos de fmriprep y una mascara de selección de voxels siguiendo lo sugerido en el repo de los autores https://github.com/isacdaavid/np-mvpa
    * **Importante**: Tuvimos un inconveniente al seguir los pasos de ese repositorio, puntualmente en el primer paso el script debería descargar imágenes DICOM, pero estas están en XNAT y no nos deja bajarlas (y por ende continuar con el resto de los pasos).

* Modelos propuestos: SVM lineal para comparar con los autores (aunque podría probarse con otros kernels). Luego pensamos que podrían considerarse comparar con modelos basados en decision trees.

* Evaluación: 
    * Crossvalidation por sujeto, accuracy.
    * Reportar test de hipotesis: p-values para accuracy de clasificación
        * El accuracy anterior, según el paper, es comparado *"against an empirical null model in a non-parametric rank-based test, by estimating the probability distribution of average classification accuracy given H0 via Monte-Carlo simulations...Then, a p-value for that particular subject and class combination can be calculated as the proportion of random results equal to or greater than the original classification accuracy"*
* El resultado esperado sería  algo similar a la figura 5

<p align="center">
  <img src="https://hackmd.io/_uploads/rJn4-E-XC.png?raw=true" alt="Sublime's custom image"/>
</p>

---
Dudas:

* Se puede hacer test de hipótesis con julearn facilmente?
* Nos gustaría conseguir algo como la figura 6/7 pero no nos queda claro que tan incómodo sería.

<p align="center">
  <img src="https://hackmd.io/_uploads/SyKFzNZm0.png?raw=true" alt="Sublime's custom image"/>
</p>


