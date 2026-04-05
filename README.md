# EJD-UMA-003 v1.0 - Clasificador Naive Bayes Federado con Mezcla de Distribuciones

Ejercicio doctoral · Programa de Doctorado en Tecnologias Informaticas · Universidad de Malaga

| Campo | Detalle |
|-------|---------|
| Codigo | EJD-UMA-003 |
| Version | 1.0 |
| Referencia | Ejercicio propuesto por el Prof. E. Lopez Rubio como continuacion de EJD-UMA-002 |
| Autor | Ing. Edgar O. Herrera Logrono, M.Sc. en Inteligencia Artificial, VIU Espana |
| Directores propuestos | Prof. Ezequiel Lopez Rubio · Prof. Juan Miguel Ortiz de Lazcano, UMA |
| Dataset | NSL-KDD (Network Security Lab, Dalhousie University) |
| Fecha | Marzo 2026 |

---

## Objetivo

Este ejercicio surge de una observacion concreta del EJD-UMA-002: no existe correlacion util entre las distancias Tree Edit Distance y el rendimiento de los arboles de decision federados. Eso descarta la TED como criterio de agregacion y abre una pregunta diferente.

El Prof. Lopez Rubio propuso entonces construir un clasificador Naive Bayes en cada nodo federado, agregar las distribuciones de probabilidad en un servidor central como mezcla, y comparar esa mezcla contra la linea base de promedio de parametros. La pregunta central es si la mezcla de distribuciones, que preserva la especificidad de cada nodo, supera al promedio que asume homogeneidad entre nodos.

---

## Estructura del notebook

| Seccion | Contenido |
|---------|-----------|
| Parametros configurables | Ajustables antes de ejecutar sin modificar el pipeline |
| Seccion 1 | Preparacion del entorno |
| Seccion 2 | Carga y preparacion del dataset NSL-KDD |
| Seccion 3 | Entrenamiento de Naive Bayes local por nodo |
| Seccion 4 | Agregacion como mezcla de distribuciones en servidor central |
| Seccion 5 | Linea base: promedio de parametros |
| Seccion 6 | Comparativa de los tres modelos: mezcla, promedio y centralizado |
| Conclusiones | Generadas automaticamente segun resultados reales |
| Resumen final | Estado de ejecucion por modulo con porcentaje de completitud |

---

## Resultados obtenidos

| Metrica | Valor |
|---------|-------|
| Nodos federados | 3 (Financiero, Gubernamental, Salud) |
| Estado de ejecucion | 100% |

> Los valores numericos de F1-Score y precision se calculan en tiempo real segun la ejecucion. No hay valores fijos en el codigo.

---

## Limitaciones declaradas

- Naive Bayes asume independencia condicional entre variables, supuesto que no se cumple en trafico de red real.
- NSL-KDD data de 1998 y no representa patrones de ataque contemporaneos en infraestructura critica OT/ICS.
- Los tres nodos se simulan en un unico entorno de ejecucion.

---

## Como ejecutarlo

1. Abrir Google Colab (colab.research.google.com)
2. Ir a File, Open notebook, GitHub y pegar la URL de este repositorio
3. Ejecutar con Runtime, Run all

El notebook descarga NSL-KDD automaticamente. Si falla la descarga genera un dataset sintetico equivalente para que la ejecucion sea completa en cualquier entorno.

---

## Stack tecnico

Python 3.10 o superior, scikit-learn 1.3 o superior, NumPy, Pandas, Matplotlib, Seaborn, SciPy.

---

## Control de cambios

| Version | Fecha | Descripcion |
|---------|-------|-------------|
| 1.0 | Mar 2026 | Version inicial. Clasificador Naive Bayes federado con mezcla de distribuciones gaussianas y multinomiales. Comparativa contra promedio de parametros y modelo centralizado. |

---

## Repositorios del proceso doctoral

| Codigo | Repositorio | Contenido |
|--------|-------------|-----------|
| EJD-UMA-001 | [RF_Federado_Ejercicio_Doctoral_UMA](https://github.com/eoherrera/RF_Federado_Ejercicio_Doctoral_UMA) | Random Forest Federado, comparativa de configuraciones |
| EJD-UMA-001 v8.0 | [RF_Federado_Ejercicio_Doctoral_UMA_v8](https://github.com/eoherrera/RF_Federado_Ejercicio_Doctoral_UMA_v8) | Fed-TRUST: Coeficiente de Veracidad V_i y agregacion ponderada |
| EJD-UMA-002 | [TED_MDS_Ejercicio_Doctoral_UMA](https://github.com/eoherrera/TED_MDS_Ejercicio_Doctoral_UMA) | Tree Edit Distance y proyeccion MDS |
| EJD-UMA-003 | Este repositorio | Clasificador Naive Bayes Federado con mezcla de distribuciones |
| EJD-UMA-003 v7.1 | [EJD_UMA_003_Variables_Contextuales_CRISC](https://github.com/eoherrera/EJD_UMA_003_Variables_Contextuales_CRISC) | Variables contextuales CRISC en aprendizaje federado |

---

Ing. Edgar O. Herrera Logrono, M.Sc. en Inteligencia Artificial, VIU Espana
Analista Senior de Seguridad de la Informacion, IESS Ecuador
Candidato CRISC, ISACA
Candidato doctoral, Programa de Doctorado en Tecnologias Informaticas, Universidad de Malaga
