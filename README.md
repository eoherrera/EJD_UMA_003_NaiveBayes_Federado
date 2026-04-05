# EJD-UMA-003 v6.0 · Clasificador Naive Bayes Federado con Mezcla de Distribuciones

Ejercicio doctoral · Programa de Doctorado en Tecnologias Informaticas · Universidad de Malaga

| Campo | Detalle |
|-------|---------|
| Codigo | EJD-UMA-003 |
| Version | 6.0 |
| Autor | Ing. Edgar O. Herrera Logrono, M.Sc. en Inteligencia Artificial, VIU Espana |
| Directores propuestos | Prof. Ezequiel Lopez Rubio · Prof. Juan Miguel Ortiz de Lazcano, UMA |
| Dataset | NSL-KDD (Network Security Lab, Dalhousie University) |
| Fecha | Marzo 2026 |

---

## Objetivo

El Prof. Lopez Rubio propuso construir un clasificador Naive Bayes en cada nodo federado, agregar las distribuciones de probabilidad en un servidor central como mezcla, y comparar esa mezcla contra el promedio de parametros como linea base. La pregunta central es si la mezcla, que preserva la especificidad de cada nodo, supera al promedio que asume homogeneidad.

Este ejercicio responde esa pregunta bajo tres niveles de heterogeneidad real (alpha=0.1, 0.3 y 1.0), usando particion Dirichlet non-IID como metodo estandar en la literatura federada.

Adicionalmente, se incorpora una dimension que el dataset no captura pero que en infraestructura critica si existe: las variables contextuales que condicionan como cada nodo aprende. La madurez en gestion de riesgos (CMM), la cobertura de controles implementados (KCI), la efectividad de esos controles (FCI) y el Indice de Coherencia Contextual (ICC) que los combina son las variables que diferencian un nodo bancario de uno gubernamental. Incorporarlas como criterio de ponderacion en la mezcla federada, sin centralizar datos, es la linea abierta que este ejercicio deja planteada.

---

## Linea evolutiva

| Version | Cambio principal | Hallazgo |
|---------|-----------------|----------|
| v4.0 | Particion aleatoria, pesos por F1-Score | JS=0.0000, nodos IID: la mezcla no puede mostrar ventaja |
| v5.0 | Particion Dirichlet alpha=0.1 | JS=0.65, heterogeneidad real. El baseline superaba a la mezcla por sesgo del prior local |
| v6.0 | Priors globales + pesos por entropia + multi-alpha | Corrige el sesgo e identifica el rango optimo de heterogeneidad |

---

## Modelos comparados

| Modelo | Descripcion |
|--------|-------------|
| NB Mezcla Entropia | Mezcla de distribuciones ponderada por entropia local de cada nodo. Priors globales. |
| NB Mezcla Uniforme | Mezcla ponderada por tamano de nodo. Priors globales. |
| NB Baseline | Promedio de parametros. Asume distribucion homogenea entre nodos. |
| NB Centralizado | Entrenado con todos los datos. Cota superior de rendimiento. |

---

## Resultados obtenidos

| Alpha | JS medio | Mezcla Entropia | Baseline | Delta |
|-------|----------|-----------------|----------|-------|
| 0.1 | 0.6500 | 0.3938 | 0.3740 | +0.0199 |
| 0.3 | 0.2409 | 0.3562 | 0.3393 | +0.0169 |
| 1.0 | 0.0369 | 0.3415 | 0.3312 | +0.0103 |

La mezcla supera al baseline en los tres niveles de heterogeneidad evaluados. La ventaja es mayor cuando la heterogeneidad es alta (JS=0.65) y se reduce progresivamente cuando los nodos son mas homogeneos (JS=0.04), lo cual es consistente con la hipotesis central del ejercicio.

El criterio de peso por entropia supera al criterio por tamano en los tres niveles, confirmando que la diversidad interna de cada nodo, no su volumen de datos, es el criterio mas informativo para la agregacion federada.

---

## Limitaciones declaradas

- Naive Bayes asume independencia condicional entre variables, supuesto que no se cumple en trafico de red real.
- NSL-KDD data de 1998 y no representa patrones de ataque contemporaneos en infraestructura critica OT/ICS.
- Los tres nodos se simulan en un unico entorno de ejecucion.
- Los valores CMM, KCI, FCI e ICC son la linea abierta de investigacion: este ejercicio los identifica como necesarios pero no los incorpora aun al mecanismo de ponderacion.

---

## Pregunta abierta

La mezcla con pesos por entropia supera al promedio en todos los niveles evaluados. La siguiente pregunta es si esos pesos podrian calcularse desde variables contextuales que no aparecen en el dataset pero si condicionan como cada nodo aprende: CMM, KCI, FCI e ICC. Es posible aprender esos pesos sin centralizar datos de ningun nodo?

---

## Como ejecutarlo

1. Abrir Google Colab (colab.research.google.com)
2. Ir a File, Open notebook, GitHub y pegar la URL de este repositorio
3. Ejecutar con Runtime, Run all

El notebook descarga NSL-KDD automaticamente. Si falla la descarga genera un dataset sintetico equivalente.

---

## Stack tecnico

Python 3.10 o superior, scikit-learn 1.3 o superior, NumPy, Pandas, Matplotlib, Seaborn, SciPy.

---

## Control de cambios

| Version | Fecha | Descripcion |
|---------|-------|-------------|
| v4.0 | Feb 2026 | Particion aleatoria. Nodos IID: mezcla sin ventaja demostrable. |
| v5.0 | Mar 2026 | Particion Dirichlet. Heterogeneidad real pero sesgo por prior local. |
| v6.0 | Mar 2026 | Priors globales, pesos por entropia, evaluacion multi-alpha. Mezcla supera al baseline en los tres niveles. |

---

## Repositorios del proceso doctoral

| Codigo | Repositorio | Contenido |
|--------|-------------|-----------|
| EJD-UMA-001 | [RF_Federado_Ejercicio_Doctoral_UMA](https://github.com/eoherrera/RF_Federado_Ejercicio_Doctoral_UMA) | Random Forest Federado, comparativa de configuraciones |
| EJD-UMA-001 v8.0 | [RF_Federado_Ejercicio_Doctoral_UMA_v8](https://github.com/eoherrera/RF_Federado_Ejercicio_Doctoral_UMA_v8) | Fed-TRUST: Coeficiente de Veracidad V_i y agregacion ponderada |
| EJD-UMA-002 | [TED_MDS_Ejercicio_Doctoral_UMA](https://github.com/eoherrera/TED_MDS_Ejercicio_Doctoral_UMA) | Tree Edit Distance y proyeccion MDS |
| EJD-UMA-003 | Este repositorio | Naive Bayes Federado con mezcla de distribuciones |

---

Ing. Edgar O. Herrera Logrono, M.Sc. en Inteligencia Artificial, VIU Espana

Analista Senior de Seguridad de la Informacion, IESS Ecuador

Candidato CRISC, ISACA

Candidato doctoral, Programa de Doctorado en Tecnologias Informaticas, Universidad de Malaga
