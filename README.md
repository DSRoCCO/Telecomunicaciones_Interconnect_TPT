# Telecomunicaciones_Interconnect_TPT

# Informe de Solución

### Proyecto Desarrollado por: Roger Christian Cansaya Olazabal  
### Proyecto: Pronóstico de Cancelación de Servicios de InterConnect  

---

## 1. ¿Qué pasos del plan se realizaron y qué pasos se omitieron?

### **Pasos realizados:**

#### **Definición del problema y objetivos:**  
El operador de telecomunicaciones **InterConnect** busca pronosticar la tasa de cancelación de clientes con el fin de identificar a los usuarios que planean abandonar el servicio y ofrecerles códigos promocionales o planes especiales para retenerlos.  

Para lograr este propósito, contamos con información proveniente de cuatro fuentes principales:  
- **Contratos:** Incluyen detalles del tipo de contrato y fechas clave.  
- **Datos personales:** Contienen información demográfica de los clientes.  
- **Servicios de internet:** Describen los servicios contratados relacionados con internet.  
- **Servicios telefónicos:** Incluyen detalles sobre los planes de telefonía contratados.  

El objetivo principal (**target**) de nuestro análisis es la variable **`EndDate`**, que indica si un cliente canceló o continúa con el servicio.

#### **Exploración y limpieza de datos:**  
Durante esta etapa, evaluamos cada fuente de datos por separado para garantizar su calidad:  

- **Datos de Contrato:**  
  - No presenta valores duplicados ni faltantes.  
  - Fue necesario actualizar los tipos de datos y convertir la columna **`Type`** a valores numéricos, ya que representa una variable ordinal.  

- **Datos de Internet:**  
  - No presenta valores duplicados ni faltantes.  
  - Solo se requirió actualizar los tipos de datos para un manejo eficiente.  

- **Datos Personales:**  
  - No presenta valores duplicados ni faltantes.  
  - También fue necesario actualizar los tipos de datos.  

- **Datos Telefónicos:**  
  - Sin valores duplicados ni faltantes.  
  - Solo se ajustaron los tipos de datos.  

Para el propósito del proyecto, unimos todas las fuentes de datos usando el **código único de usuario** como identificador, consolidando así un conjunto completo. Posteriormente, aplicamos técnicas de **imputación de valores** donde fue necesario, asegurando que el conjunto estuviera listo para su procesamiento y análisis.

#### **Análisis exploratorio de datos (EDA):**  
En esta etapa, llevamos a cabo un análisis de **ingeniería de características** para identificar las variables más relevantes en la predicción de cancelación de clientes:  
- Se inició con una **matriz de correlación** para observar relaciones lineales entre las variables.  
- Posteriormente, utilizamos el método de **Boruta** para la selección automática de características. Este método destacó las variables relevantes para nuestro objetivo (marcadas en verde).  

A continuación, seleccionamos las **siete características principales** basándonos en su ranking, incluyendo variables como **`gender_Male`**, las cuales demostraron un aporte significativo en la predicción del modelo.

#### **División de los datos:**  
Después de consolidar y procesar los datos, se realizó la división en dos conjuntos:  
- **Entrenamiento (train):** 80% de los datos, usado para entrenar los modelos.  
- **Prueba (test):** 20% de los datos, reservado para la validación del desempeño del modelo.  

Durante esta etapa, detectamos un **desbalance de clases significativo** en el conjunto de entrenamiento:  
- **Clase '0':** 3892 registros (clientes que no cancelaron).  
- **Clase '1':** 1390 registros (clientes que cancelaron).  

Para abordar este desbalance, aplicamos la técnica de **Edited Nearest Neighbours (ENN)**, una estrategia de submuestreo que reduce los datos de la clase mayoritaria eliminando registros ambiguos cercanos a la clase minoritaria. Como resultado, el conjunto de entrenamiento quedó con un desbalance reducido:  
- **Clase '0':** 3838 registros.  
- **Clase '1':** 1496 registros.  

Posteriormente, realizamos un **escalado de los datos** utilizando **StandardScaler**, que normalizó las características numéricas a una escala estándar (media = 0, desviación estándar = 1), mejorando la estabilidad del entrenamiento para modelos sensibles a las magnitudes de las variables.

#### **Selección y entrenamiento de modelos:**  
Para la selección y entrenamiento de modelos, probamos distintos algoritmos de clasificación:  

- Logistic Regression  
- Random Forest  
- Gradient Boosting  
- SVM  
- XGBoost  
- LightGBM  
- MLP Classifier  
- K-Nearest Neighbors (KNN)  
- Naive Bayes  
- SGD Classifier  

Los modelos fueron evaluados usando **validación cruzada** y seleccionamos el modelo con el mejor **AUC-ROC**.

#### **Optimización de hiperparámetros:**  
Utilizamos **BayesSearchCV** para optimizar los hiperparámetros del modelo final (**Gradient Boosting Classifier**) y evaluamos los resultados:  

- **Mejores parámetros encontrados:**  
  - `learning_rate`: 0.0321 
  - `max_depth`: 3  
  - `max_features`: 0.2457 
  - `min_samples_leaf`: 7  
  - `min_samples_split`: 4  
  - `n_estimators`: 415  
  - `subsample`: 0.5544

- **Métricas:**  
  - Train AUC-ROC: 0.8971  
  - Test AUC-ROC: 0.8641  
  - Train Accuracy: 0.8453  
  - Test Accuracy: 0.8105  
  - Cross-Validation AUC-ROC (Entrenamiento): 0.88
  - Cross-Validation AUC-ROC (Prueba): 0.85

---

### **Pasos omitidos:**
- **Recolección de datos adicionales:** Se decidió trabajar exclusivamente con el dataset proporcionado, ya que la inclusión de datos externos habría excedido el alcance del proyecto y los tiempos establecidos.

---

## 2. ¿Qué dificultades encontraste y cómo lograste resolverlas?

1. **Desbalance de clases:**  
   - **Problema:** La variable objetivo presentaba un desbalance significativo, afectando la capacidad de los modelos para identificar correctamente la clase minoritaria.  
   - **Solución:** Utilizamos la técnica **ENN (Edited Nearest Neighbours)** con `n_neighbours=12` para reducir el desbalance, mejorando las métricas de clasificación.  

2. **Características altamente correlacionadas:**  
   - **Problema:** Algunas variables numéricas presentaban alta multicolinealidad, mientras que las categóricas tenían relaciones difíciles de interpretar.  
   - **Solución:** Empleamos **Boruta**, seleccionando todas las características relevantes excepto las de menor ranking, optimizando así el conjunto de datos.  

3. **Tiempo de entrenamiento del modelo:**  
   - **Problema:** La optimización de hiperparámetros generó tiempos prolongados debido al amplio rango de búsqueda.  
   - **Solución:** Ajustamos los rangos clave, como la profundidad máxima y la tasa de aprendizaje, mejorando la eficiencia sin sacrificar rendimiento.

---

## 3. ¿Cuáles fueron algunos de los pasos clave para resolver la tarea?

- **Ingeniería de características:** La selección de variables mediante Boruta fue crucial para determinar las más relevantes para el objetivo.  
- **Equilibrado de clases:** Aplicar ENN mejoró significativamente el aprendizaje de los modelos.  
- **Optimización del modelo:** Usar **BayesSearchCV** para ajustar hiperparámetros aumentó el rendimiento, especialmente en **Gradient Boosting Classifier**.

---

## 4. ¿Cuál es tu modelo final y qué nivel de calidad tiene?

### **Modelo final seleccionado:**  
**Gradient Boosting Classifier**

### **Métricas finales:**  
- Test AUC-ROC: 0.8641  
- Test Accuracy: 0.8105  
- Cross-Validation AUC-ROC: 0.85  

El modelo final cumple con los requisitos del proyecto y ofrece un balance óptimo entre **AUC-ROC** y **exactitud**, siendo una herramienta confiable para la toma de decisiones del operador **InterConnect**.

---
