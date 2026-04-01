# EJD-UMA-003 -- Clasificador Naive Bayes Federado con Mezcla de Distribuciones

**Ejercicio doctoral | Programa de Doctorado en Tecnologias Informaticas | Universidad de Malaga**

| Campo | Detalle |
|-------|---------|
| Codigo | EJD-UMA-003 |
| Version actual | 6.0 |
| Autor | Edgar O. Herrera Logrono, M.Sc. Inteligencia Artificial, VIU Espana |
| Directores propuestos | Prof. Ezequiel Lopez Rubio y Prof. Juan Miguel Ortiz de Lazcano, UMA |
| Dataset | NSL-KDD (KDDTrain+.txt) |
| Repositorio anterior | EJD-UMA-002: Tree Edit Distance + MDS sobre arboles federados |

---

## Origen del ejercicio

Este ejercicio fue propuesto por el Prof. Lopez Rubio como continuacion directa de EJD-UMA-002, tras confirmar que no existe correlacion util entre las distancias Tree Edit Distance y el rendimiento de los arboles de decision federados.

La propuesta fue construir un clasificador Naive Bayes en cada nodo federado, agregar las distribuciones de probabilidad en un servidor central como mezcla, y comparar esa mezcla contra el baseline de promedio de parametros. La pregunta central: la mezcla de distribuciones, que preserva la especificidad de cada nodo, supera al promedio que asume homogeneidad?

---

## Hipotesis de trabajo

Tratar todos los nodos como equivalentes no es razonable cuando cada uno aprende desde escenarios propios, marcos regulatorios distintos e historiales de incidentes diferentes. Esa especificidad no es ruido, es informacion. Una mezcla de distribuciones condicionales P(X|clase) ponderada por la diversidad local de cada nodo deberia capturar esa riqueza mejor que el promedio, especialmente cuando se usan priors globales que eliminan el sesgo de composicion de cada nodo.

Esta hipotesis solo puede verificarse en condiciones de heterogeneidad real entre nodos. El proceso de desarrollo consistio precisamente en construir y verificar esas condiciones de forma rigurosa.

---

## Evolucion del ejercicio

La siguiente tabla resume las versiones de desarrollo. Cada version surgio de un hallazgo concreto, no de ajustes arbitrarios.

| Version | Cambio principal | Hallazgo que lo motivo |
|---------|-----------------|------------------------|
| v4.0 | Particion aleatoria, pesos por F1-Score | JS medio = 0.0000. Los tres nodos recibieron distribuciones identicas (IID). Sin heterogeneidad real, la hipotesis no puede verificarse. |
| v5.0 | Particion Dirichlet (alpha=0.1) | JS medio = 0.65, heterogeneidad real verificada. Sin embargo el baseline supero a la mezcla. Tres problemas de diseno identificados. |
| v6.0 | Priors globales + pesos por entropia de Shannon + analisis multi-alpha (0.1, 0.3, 1.0) | La hipotesis se confirma en los tres niveles de heterogeneidad. La mezcla con entropia supera al baseline de forma consistente. |

---

## Hallazgos por version

### v4.0 -- El problema de la homogeneidad oculta

Con particion aleatoria simple, los tres nodos recibieron distribuciones de clases practicamente identicas:

```
Nodo Financiero: DoS 36.3%, Probe 9.2%, normal 53.6%
Nodo Salud:      DoS 36.4%, Probe 9.4%, normal 53.4%
Nodo Gobierno:   DoS 36.7%, Probe 9.2%, normal 53.4%
JS medio entre nodos: 0.0000
```

Cuando los nodos ven la misma distribucion, la mezcla no tiene ventaja sobre el promedio. La hipotesis no fue refutada — simplemente no tenia condiciones para ser evaluada.

**Conclusion metodologica:** la particion de datos en experimentos federados no es un detalle tecnico menor. Es una decision de diseno que determina si el experimento tiene validez interna.

### v5.0 -- Heterogeneidad real, nuevos problemas de diseno

Con particion Dirichlet (alpha=0.1), la heterogeneidad quedo verificada:

```
Nodo Financiero (46,770 muestras): normal 99.7%
Nodo Salud       (8,923 muestras): Probe  89.3%
Nodo Gobierno   (32,488 muestras): DoS    98.1%
JS medio: 0.6500
```

El baseline supero a la mezcla. Ese resultado activo tres preguntas de diseno:

**Problema 1 -- Prior dominante:** el nodo Financiero tenia P(normal)=0.997. Cuando la mezcla incorpora ese prior, el modelo tiende a clasificar todo como normal. Era necesario usar priors globales que reflejen la distribucion real del problema.

**Problema 2 -- Criterio de peso circular:** el F1-Score local del nodo Financiero era alto porque el conjunto de prueba tiene 53% de trafico normal y ese nodo clasifica casi todo como normal. No aprende bien — tiene sesgo extremo. Darle mas peso amplificaba ese sesgo. La entropia de Shannon de la distribucion local es mas rigurosa: el nodo mas diverso aporta mas informacion al modelo global.

**Problema 3 -- Alpha extremo:** con alpha=0.1 cada nodo se especializo en una sola clase. Era necesario evaluar si la ventaja de la mezcla aparece en rangos de heterogeneidad mas moderados.

**Hallazgo adicional:** el modelo centralizado (0.3307) obtuvo menor F1-macro que el baseline federado (0.3740). Cuando la federacion supera a la centralizacion en GaussianNB, algo matematicamente relevante esta ocurriendo y merece analisis propio.

### v6.0 -- Las tres correcciones incorporadas y la hipotesis confirmada

**Resultados finales:**

| Alpha | JS medio | Mezcla Entropia | Mezcla Uniforme | Baseline | Centralizado |
|-------|---------|----------------|----------------|---------|-------------|
| 0.1 | 0.6500 | **0.3938** | 0.3511 | 0.3740 | 0.3307 |
| 0.3 | 0.2409 | **0.3562** | 0.3440 | 0.3393 | 0.3307 |
| 1.0 | 0.0369 | **0.3415** | 0.3322 | 0.3312 | 0.3307 |

La mezcla con priors globales y pesos por entropia supera al baseline en los tres niveles de heterogeneidad. La ventaja disminuye al reducirse la heterogeneidad, lo cual es coherente con la hipotesis: a mayor especificidad entre nodos, mayor ganancia de preservarla.

**Entropias locales con alpha=0.1:**
```
Financiero: 0.021  (especialista en normal -- baja diversidad)
Salud:      0.407  (ve Probe y R2L -- diversidad media)
Gobierno:   0.106  (especialista en DoS -- baja diversidad)
```

El nodo Salud, con mayor entropia, recibe el mayor peso en la mezcla adaptativa. Eso es lo que el criterio de entropia busca: el nodo que ve mas clases distintas aporta mas al modelo global.

**Hallazgo sobre el modelo centralizado:** en todos los niveles de alpha, el modelo centralizado (0.3307) es superado por la mezcla federada. Esto sugiere que cuando los datos de entrenamiento son heterogeneos, la centralizacion introduce ruido que la federacion evita naturalmente al mantener las distribuciones locales separadas.

---

## Estructura del notebook v6.0

| Seccion | Contenido |
|---------|-----------|
| Cabecera | Tabla de identificacion, literatura revisada, hipotesis, tabla de evolucion de versiones |
| Parametros configurables | SEMILLA, TEST_SIZE, ALPHAS_DIRICHLET con guia de valores sugeridos |
| Seccion 1 | Preparacion del entorno |
| Seccion 2 | Carga y preprocesamiento NSL-KDD; calculo del prior global |
| Seccion 3 | Funciones de particion Dirichlet, entropia local, agregacion y prediccion |
| Seccion 4 | Experimento multi-alpha: ciclo completo para los tres niveles |
| Seccion 5 | Figura 1: comparativa multi-alpha y ventaja de la mezcla sobre el baseline |
| Seccion 6 | Figura 2: ganancia de la federacion por nodo para cada alpha |
| Conclusiones | Generadas automaticamente desde los resultados; incluyen hallazgo sobre centralizado y pregunta abierta doctoral |

---

## Literatura revisada

| Referencia | Aporte |
|------------|--------|
| Torrijos et al. (2025), arXiv:2502.01532 | NB federado discriminativo; usa la variante generativa como baseline |
| ACM Computing Surveys (2023), doi:10.1145/3625558 | La mezcla implicita supera al promedio en entornos non-IID |
| Li et al. (2022), NeurIPS | Particion Dirichlet como metodo estandar para simular heterogeneidad non-IID |
| ScienceDirect (2025) | Ponderacion hibrida por tamano y accuracy para agregacion federada |
| Springer Nature Cybersecurity (2025), doi:10.1186/s42400-026-00567-6 | Survey sobre FL para deteccion de anomalias en infraestructura critica |

---

## Pregunta abierta para investigacion doctoral

Los resultados miden rasgos observables del trafico de red. Sin embargo, cada nodo porta variables contextuales que no aparecen en el dataset: entorno regulatorio, historial de incidentes, cultura de seguridad organizacional, tolerancia al riesgo. Esas variables condicionan como cada nodo aprende y, por tanto, como deberia ponderarse en la mezcla federada.

Como incorporarlas en el mecanismo de agregacion sin centralizar datos es la linea que queda abierta. Esta pregunta abre direcciones de investigacion en trust management y deteccion de drift aplicados a sistemas federados, que se identifican como posibles ejes de continuidad doctoral.

---

## Reproducibilidad

Para reproducir exactamente los resultados de v6.0:

```python
SEMILLA          = 42
TEST_SIZE        = 0.3
ALPHAS_DIRICHLET = [0.1, 0.3, 1.0]
```

Para explorar otros niveles de heterogeneidad, modificar unicamente ALPHAS_DIRICHLET.

---

*Edgar O. Herrera Logrono, M.Sc. Inteligencia Artificial, VIU Espana*
*EJD-UMA-003 v6.0 -- Universidad de Malaga -- Marzo 2026*
