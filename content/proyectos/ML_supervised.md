---
title: "Machine Learning Supervisado"
description: "Una serie de proyectos de clasificación con técnicas de Machine Learning Supervisado"
date: "2024-05-30"
cluster: "Python"
slug: "ml_supervised"
draft: false
github: "https://github.com/aliciagilmatute/Machine-Learning-supervised-"
kaggle: "https://www.kaggle.com/code/arrobarsandias/"
---
<br><br>

## Árbol de Decisión y Bosques Aleatorios
<br>

### Introducción

La base de datos que emplearemos es una base de datos que recoge información de 200 personas, en concreto, se recoge información sobre su Edad, Sexo, Presión en Sangre, Niveles de Colesterol, Niveles de Sodio y Potasio y droga/medicación que se le receta. Queremos realizar un árbol aleatorio que tenga en cuenta todas estas variables y sea capaz de predecir qué droga/medicación debemos recetar a cada paciente.

En primer lugar vamos a realizar una reestructuración de los datos y después un breve Análisis Exploratorio, para ver qué tipo de datos tenemos, cuantas categorías, valores perdidos, etc. Aunque ya adelantamos que no hay valores perdidos y que las variables cuantitativas son dad y Sodio-Potasio y el resto son categóricas. 

<br>

### Árbol de Decisión
<br>
<div style="display:flex; justify-content:center; gap:30px; align-items:center;">
  <figure style="margin:0; width:360px; text-align:center;">
    <img src="/accuracy_tree.png" alt="Métricas de Evaluación del Árbol de Decisión" width="360">
    <figcaption style="font-style:italic; margin-top:8px;">
      Figura 1. Métricas de Evaluación del Árbol de Decisión.
    </figcaption>
  </figure>
</div>
<br>

Como podemos ver el *accuracy* del Árbol de Decisión es de 0.98 y su matriz de confusión es ideal (solo 1 caso no es clasificado correctamente). Con él, podríamos utilizarlo para predecir un caso nuevo que no existiera ni en el conjunto de entrenamiento ni el conjunto de test como se hace en el siguiente bloque de código:
<br><br>

```python
X = np.array(['20', 'Male', 'LOW', 'LOW', '10'])
label = LabelEncoder()
X = label.fit_transform(X)

y_pred = clf.predict(X.reshape(1, -1))
y_pred
```

Pero el Árbol de Decisión adolece de un problema que los Bosques Aleatorios no tienen: **son poco generalizables**. Los Bosques Aleatorios al implementar múltiples árboles, permiten una clasificación más robusta y confiable.
<br><br>

### Bosques Aleatorios



### Anexo 1: *Script Árboles y Bosques Aleatorios*


---



<br><br>
