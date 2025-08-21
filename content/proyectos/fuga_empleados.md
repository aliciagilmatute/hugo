---
title: "Análisis Fuga de Empleados"
description: "Proyecto de análisis y machine learning para predecir la fuga de empleados, medir su impacto económico y proponer estrategias de retención."
date: "2025-08-21"
cluster: "Python"
slug: "fuga_empleados"
draft: false
github: "https://github.com/tu_usuario/tu_repositorio"
kaggle: "https://www.kaggle.com/code/arrobarsandias/an-lisis-fuga-empleados"
tableau: "https://public.tableau.com/views/DashboardAbandonoEmpleados_17549150031180/Dashboard1?:showVizHome=no&:embed=true"
---

<!-- # Emulación de una semana de trabajo en DataScientist -->
<br><br>
El gran objetivo del proyecto en el que vas a trabajar es reducir la fuga de empleados de la empresa. 
El principal problema es la rotación de empleados. Deberás trabajar en un sistema que consiga reducir la fuga de empleados.
<br><br>

## Objetivos<br>
1. Entender y cuantificar el problema desde el punto de vista de negocio
2. Desarrollar un sistema automatizado de machine learning que identifique a los empleados que están en mayor riesgo de fuga
3. Comunicar los resultados de forma exitosa a la dirección
<br><br>

## Fases<br>
- **Business Analytics**: entender y cuantificar el problema
- **Machine Learning**: desarrollar un modelo predictivo
- **Productivización**: generar un cuadro de mando
<br><br>
## Carga de librerías y dataset

```python
import numpy as np
import pandas as pd 
import matplotlib.pyplot as plt
import os

ruta = os.path.join(os.getcwd())
df = pd.read_csv(ruta + r'/data/AbandonoEmpleados.csv', sep=';', na_values='#N/D')
df.head()
df.info()
```

## Análisis de nulos
```python
df.isnull().sum().sort_values(ascending=False)
```

Conclusiones:
- Eliminamos columnas con muchos nulos: `años_en_puesto` y `conciliacion`.
- Imputamos valores en sexo, educación, satisfacción e implicación.

```python
df.drop(columns = ['anos_en_puesto','conciliacion'], inplace = True)
df['educacion'] = df['educacion'].fillna('Universitaria')
df['implicacion'] = df['implicacion'].fillna('Alta')
df['satisfaccion_trabajo'] = df['satisfaccion_trabajo'].fillna('Alta')
```

## Variables numéricas
Conclusiones:
- Eliminamos `sexo`, `empleados`, `horas_quincena`.

```python
df.drop(columns=['sexo','empleados','horas_quincena'], inplace=True)
```

## Generación de insights
**Tasa de abandono**
```python
df['abandono'].value_counts(normalize=True)*100
```

**Impacto económico**
```python
df['salario_ano'] = df['salario_mes'] * 12

condiciones=[
    (df['salario_ano']<=30000),
    (df['salario_ano']>30000) & (df['salario_ano']<=50000),
    (df['salario_ano']>50000) & (df['salario_ano']<=75000),
    (df['salario_ano']>75000)
]
ponderaciones=[df['salario_ano']*0.161, df['salario_ano']*0.197, df['salario_ano']*0.204, df['salario_ano']*0.21]

df['impacto_abandono'] = np.select(condiciones, ponderaciones, default=-999)
coste_total = df.loc[df['abandono']=='Yes'].impacto_abandono.sum()
```

## Machine Learning

**Preparación de datos**
```python
df['abandono'] = df.abandono.map({'No':0, 'Yes':1})
from sklearn.preprocessing import OneHotEncoder

df_ml = df.copy()
cat = df_ml.select_dtypes('O')
ohe = OneHotEncoder(sparse_output=False)
ohe.fit(cat)
cat_ohe = pd.DataFrame(ohe.transform(cat), columns=ohe.get_feature_names_out(cat.columns)).reset_index(drop=True)

num = df_ml.select_dtypes('number').reset_index(drop=True)
df_ml = pd.concat([cat_ohe, num], axis=1)
```

**Modelos candidatos**
```python
from sklearn.model_selection import train_test_split, StratifiedKFold, cross_val_score
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier
from sklearn.neighbors import KNeighborsClassifier
from sklearn.svm import SVC

X = df_ml.drop(columns='abandono')
y = df_ml['abandono']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=42)

models = [
    ('LR', LogisticRegression(solver='liblinear', multi_class='ovr')),
    ('KNN', KNeighborsClassifier()),
    ('RF', RandomForestClassifier()),
    ('SVM', SVC(gamma='auto'))
]

for name, model in models:
    kfold = StratifiedKFold(n_splits=10, random_state=1, shuffle=True)
    cv_results = cross_val_score(model, X_train, y_train, cv=kfold, scoring='accuracy')
    print(f"{name}: {cv_results.mean():.4f} ({cv_results.std():.4f})")
```

**Random Forest optimizado**
```python
from sklearn.model_selection import RandomizedSearchCV

param_dist = {
    'n_estimators': [50, 100, 200, 300],
    'max_depth': [None, 10, 20, 30],
    'min_samples_split': [2, 5, 10],
    'min_samples_leaf': [1, 2, 4],
    'max_features': ['sqrt', 'log2']
}

random_search = RandomizedSearchCV(
    estimator=RandomForestClassifier(), 
    param_distributions=param_dist,
    n_iter=50, cv=5, scoring='f1_macro', verbose=2, n_jobs=-1, random_state=42
)

random_search.fit(X_train, y_train)
print(random_search.best_params_)
```

**Evaluación**
```python
RF = RandomForestClassifier(**random_search.best_params_)
RF.fit(X_train, y_train)
RF_pred = RF.predict(X_test)

from sklearn.metrics import accuracy_score, confusion_matrix, classification_report

print(accuracy_score(y_test, RF_pred))
print(confusion_matrix(y_test, RF_pred))
print(classification_report(y_test, RF_pred))
```

**Scoring de abandono**
```python
df['scoring_abandono'] = RF.predict_proba(df_ml.drop(columns='abandono'))[:,1]
df.sort_values(by='scoring_abandono', ascending=False).head(10)
```
<br><br>

## Visualización y Comunicación
<div style="text-align: center; margin: 2rem 0;">
  <!-- Enlace al dashboard con estilo de botón -->
  <p>
    <a href="https://public.tableau.com/app/profile/alicia.gil.matute/viz/DashboardAbandonoEmpleados_17549150031180/Dashboard1" 
       target="_blank" class="btn-contact">
       Ver Dashboard
    </a>
  </p>


  

<!-- No es necesario porque se gestiona el tamaño en single.html -->


<!-- Dashboard centrado manualmente
<div style="text-align: center; margin: 2rem 0;">
<div style="width: 100%; max-width: 1200px; margin: 0 auto;">
  <iframe 
      src="https://public.tableau.com/views/DashboardAbandonoEmpleados_17549150031180/Dashboard1?:showVizHome=no&:embed=true"
      width="100%" height="900" frameborder="0" scrolling="no" allowfullscreen
      style="margin: 0 auto; display: block;">
  </iframe>
</div> -->