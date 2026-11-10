# Nexo | Planificación financiera de sistemas

### Aplicación de Gradientes Conjugados Precondicionados a la distribución de recursos tecnológicos

**Universidad Mayor de San Andrés · Carrera de Informática**  
**Mención: Ingeniería de Sistemas · Métodos Numéricos**

## Presentación

Nexo es una página web interactiva que permite analizar la planificación presupuestaria de una empresa de servicios digitales mediante métodos numéricos.

El proyecto estudia cómo distribuir recursos entre cuatro áreas relacionadas: **infraestructura, desarrollo, ciberseguridad y soporte**. A través de tres escenarios financieros, permite observar cómo cambian los presupuestos y cómo responde el método de Gradientes Conjugados Precondicionados (GCP).

La página integra el caso de estudio, la formulación matemática, un simulador editable, gráficos de convergencia, análisis de errores y pruebas de calidad.

> Los datos son simulados con fines académicos. No representan información financiera de una empresa real.

## Problema que aborda

La planificación de recursos tecnológicos requiere considerar las necesidades de cada departamento y sus relaciones con el resto de la organización.

Una decisión sobre infraestructura puede afectar al desarrollo; las decisiones de desarrollo requieren coordinación con seguridad; y soporte necesita recursos para mantener la continuidad del servicio.

Nexo plantea la siguiente pregunta:

**¿Cómo calcular una asignación presupuestaria que equilibre las metas de cada área y la coordinación entre ellas, y qué tan confiable es la solución numérica obtenida?**

## Enfoque en Ingeniería de Sistemas

El proyecto analiza la organización como un conjunto de procesos interdependientes.

Su aporte se concentra en:

- **Modelado de procesos:** relaciona planificación, evaluación y aprobación presupuestaria.
- **Dependencias organizacionales:** incorpora coordinación entre las cuatro unidades.
- **Reglas de negocio:** verifica presupuestos no negativos y su relación con un fondo indicativo.
- **Apoyo a decisiones:** permite comparar alternativas antes de aprobar recursos.
- **Trazabilidad:** muestra los supuestos, procedimientos y errores de cada cálculo.

La solución es una propuesta de planificación. La aprobación de los presupuestos corresponde a los responsables de la organización.

## Variables del modelo

Los presupuestos se expresan en **miles de bolivianos por trimestre**.

| Variable | Área | Destino de los recursos |
|---|---|---|
| x₁ | Infraestructura | Nube, redes y continuidad de plataforma |
| x₂ | Desarrollo | Personal, integración y entregas de software |
| x₃ | Ciberseguridad | Prevención, monitoreo y protección |
| x₄ | Soporte | Atención, operación y continuidad del servicio |

## Fundamento matemático

El modelo equilibra dos objetivos: aproximar cada presupuesto a su meta y reducir diferencias entre áreas conectadas.

La función de planificación es:

$$
J(x)=\frac{1}{2}\sum_{i=1}^{4}w_i(x_i-t_i)^2
+\frac{c}{2}\sum_{i=1}^{3}(x_i-x_{i+1})^2
$$

Donde:

- **tᵢ:** meta presupuestaria del área.
- **wᵢ:** ponderación de la desviación respecto a esa meta.
- **c:** ponderación de la coordinación entre áreas.

Al establecer la condición de mínimo se obtiene:

$$
Ax=b
$$

La matriz se construye como:

$$
A=\operatorname{diag}(w)+cL
$$

Aquí, **L** es el laplaciano de la cadena de dependencias entre las cuatro áreas.

En los escenarios originales, las ponderaciones positivas garantizan que **A sea simétrica y definida positiva**, condición necesaria para aplicar los métodos utilizados.

## Escenarios financieros

| Escenario | Situación representada | Propósito del análisis |
|---|---|---|
| Ideal | Metas y ponderaciones equilibradas | Evaluar precisión y convergencia en condiciones estables |
| Bajo estrés | Reducción de metas en algunas áreas, manteniendo la meta de seguridad | Analizar el efecto financiero de modificar el vector b |
| Mal condicionado | Ponderaciones contractuales de escalas muy diferentes | Examinar la estabilidad y el aporte del precondicionamiento |

Los tres escenarios conservan las mismas variables y la estructura general del modelo.

## Métodos implementados

### Cholesky: solución numérica de referencia

Calcula una solución mediante factorización y sustituciones triangulares. Sirve como referencia para evaluar las aproximaciones iterativas.

### Gradientes Conjugados — GC

Resuelve el sistema sin precondicionamiento y permite establecer una comparación con GCP.

### Gradientes Conjugados Precondicionados — GCP

Utiliza el precondicionador de Jacobi:

$$
M=\operatorname{diag}(A)
$$

Su finalidad es compensar diferencias de escala y favorecer la convergencia.

La configuración inicial utiliza un vector nulo, un máximo de 100 iteraciones y el criterio:

$$
\frac{\|b-Ax_k\|_2}{\|b\|_2}<10^{-6}
$$

## Funcionalidades de la página

- Carga rápida de los tres escenarios.
- Edición de la matriz A y el vector b.
- Vinculación opcional de entradas simétricas.
- Configuración del vector inicial, tolerancia e iteraciones máximas.
- Selección de Jacobi o identidad como precondicionador.
- Recálculo automático de soluciones y errores.
- Gráfico de convergencia de GCP y GC.
- Visualización del presupuesto por área.
- Tabla comparativa de escenarios.
- Consulta del factor de Cholesky y los menores principales.
- Bitácora de iteraciones con aproximaciones, α, β y residuos.
- Ejecución de pruebas de calidad desde la interfaz.

## Análisis de precisión

La página presenta:

- Error absoluto por componente.
- Error relativo por componente.
- Error porcentual por componente.
- Residuo global.
- Número de condición de A y de M⁻¹A, calculados con la norma infinito.

**Un residuo global pequeño no garantiza que todos los presupuestos tengan un error individual pequeño.** Por ello, la interpretación debe considerar también los errores por área.

## Resultados de los escenarios originales

Configuración: Jacobi, vector inicial nulo y tolerancia de 10⁻⁶.

| Indicador | Ideal | Bajo estrés | Mal condicionado |
|---|---:|---:|---:|
| Presupuesto total de referencia, en miles de Bs | 151,64 | 127,39 | 154,77 |
| Iteraciones GC | 4 | 4 | 5 |
| Iteraciones GCP | 4 | 4 | 2 |
| κ∞(A) | 2,365729 | 2,365729 | 671.075,217605 |
| κ∞(M⁻¹A) | 1,897698 | 1,897698 | 1,782920 |
| Convergencia GCP | Sí | Sí | Sí |

En el escenario mal condicionado, Jacobi reduce las iteraciones necesarias para alcanzar la tolerancia establecida. Esta comparación mide **iteraciones**, no tiempo de ejecución.

## Control de calidad

La página incorpora diez pruebas ejecutables:

1. Carga del escenario ideal.
2. Carga del escenario bajo estrés.
3. Carga del escenario mal condicionado.
4. Rechazo de una matriz no simétrica.
5. Rechazo de una matriz no definida positiva.
6. Recálculo del motor al modificar los datos.
7. Comparación de iteraciones entre GCP y GC.
8. Detección del límite de iteraciones.
9. Tratamiento del vector b nulo.
10. Actualización mediante un evento real de edición en la interfaz.

Cada prueba muestra su entrada, resultado esperado, resultado obtenido y conclusión.

## Cómo explorar la página

1. Revisa el caso de estudio y las variables.
2. Selecciona un escenario en el simulador.
3. Observa sus presupuestos, errores y convergencia.
4. Modifica los datos para explorar otras condiciones.
5. Consulta la comparación entre escenarios.
6. Ejecuta las pruebas de calidad.
7. Revisa las conclusiones antes de interpretar los resultados.

## Alcance y limitaciones

El modelo utiliza datos académicos y cuatro variables presupuestarias.

El fondo de 160 mil Bs funciona como referencia de revisión: **no constituye una restricción impuesta al cálculo**. Tampoco se incorporan mínimos contractuales obligatorios.

Si se requieren límites financieros estrictos, el problema debe ampliarse mediante optimización restringida.

Las matrices editadas pueden ser válidas matemáticamente y, aun así, requerir una nueva justificación financiera.

## Conclusión

Nexo muestra cómo los métodos numéricos pueden apoyar la planificación de una organización tecnológica.

El enfoque de Ingeniería de Sistemas conecta presupuestos, dependencias y reglas de negocio con un modelo verificable. La comparación entre GC y GCP permite comprender el papel del precondicionamiento, mientras que el análisis de errores ayuda a interpretar los resultados con mayor criterio.

## Referencias

- Suñagua S., P. (2020). **Método de Gradientes Conjugados Precondicionado**. Revista Boliviana de Matemática – UMSA, 04, 2–7. [Consultar artículo](https://cmat.umsa.bo/revmat/rbm4/psunagua_gcp.pdf).
- Golub, G. H. y Van Loan, C. F. (2013). **Matrix Computations**. 4.ª edición. Johns Hopkins University Press.
- Saad, Y. (2003). **Iterative Methods for Sparse Linear Systems**. 2.ª edición. SIAM.