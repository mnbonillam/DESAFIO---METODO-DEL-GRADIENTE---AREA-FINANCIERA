# Nexo — Planificación financiera de sistemas

Desafío individual de Métodos Numéricos (UMSA), enfoque Ingeniería de Sistemas.

## Ejecución local

No requiere instalar dependencias. Abra `dist/index.html` en un navegador moderno o ejecute:

```sh
python3 -m http.server 8080 --directory dist
```

Visite http://localhost:8080. Para las pruebas numéricas use Node.js:

```sh
node test.cjs
```

## Arquitectura

- `dist/index.html`: caso de estudio, formulación, procesos, referencias y contenedores.
- `dist/math.js`: operaciones matriciales puras, Cholesky, GC/GCP, condición y escenarios.
- `dist/app.js`: eventos, validaciones, tablas, gráficos SVG y QA de interfaz.
- `dist/style.css`: diseño adaptable a móviles e impresión.
- `test.cjs`: comprobaciones reproducibles de precisión, convergencia y validación.

Sin backend, base de datos, framework ni API financiera. Los datos se recalculan en el navegador. Solo el nombre introducido se conserva localmente en localStorage; no se transmite. Google Fonts es opcional: el diseño tiene fuentes de respaldo.

## Modelo

Variables: presupuestos trimestrales en miles de Bs para infraestructura, desarrollo, ciberseguridad y soporte. Se minimiza J(x) = 1/2 sum(w_i (x_i-t_i)^2) + c/2 sum((x_i-x_{i+1})^2), c=0.5. La estacionariedad produce A=diag(w)+cL y b_i=w_i*t_i, con L laplaciano de la cadena de dependencias. Para todo v no nulo, v^TAv=sum(w_i v_i^2)+c sum((v_i-v_{i+1})^2)>0; por tanto A es SPD en los tres casos.

- Ideal: w=[2,3,2,3], t=[40,55,30,25].
- Estrés: mismo w, t=[35,42,30,20].
- Mal condicionado: w=[1,100,10000,1000000], t ideal. Rigidez contractual heterogénea exagerada para estudiar escalamiento.

Datos simulados; no representan datos observados ni estimaciones reales. El fondo indicativo es 160 mil Bs. No se imponen restricciones duras de caja ni no negatividad; se señalan para revisión. En el modelo original la estructura M-matriz y b positivo producen presupuestos positivos. Si se requieren restricciones vinculantes debe usarse optimización restringida.

## Algoritmos y precisión

Referencia: Cholesky A=LL^T y dos sustituciones triangulares. GCP: precondicionador M=diag(A), resolución Mz=r, direcciones conjugadas y coeficientes alpha/beta. GC usa M=I. x0=0, tolerancia 1e-6, Nmax=100. El residuo verdadero se vuelve a calcular para verificar la parada. Para b=0 se usa la norma absoluta y se indica en la interfaz.

Se reporta kappa_infinito(B)=||B||_infinito ||B^-1||_infinito para A y M^-1 A; la inversa se obtiene por Gauss-Jordan con pivoteo parcial. No se usa cociente de autovalores para una matriz no simétrica. Errores por componente: absoluto, relativo y porcentual. Denominador cero: no definido.

Resultados por defecto: ideal 4/4 iteraciones GCP/GC; estrés 4/4; mal condicionado 2/5, condición original 671075.2176 y precondicionada 1.7829203. La tolerancia global no garantiza precisión monetaria por componente: el caso extremo exhibe aproximadamente 0.16% de error máximo; use 1e-10 o menor para estudiar este efecto.

## Pruebas

La página ejecuta 10 pruebas: 3 cargas, matriz asimétrica, matriz indefinida, recálculo del motor, comparación de Jacobi, límite de iteraciones, b nulo y edición real por evento de interfaz. La prueba UI restaura el valor después de comprobar el recálculo. `node test.cjs` añade verificación a 1e-12, menores positivos y valores no finitos. La matriz es fija de orden 4 en la interfaz (cumple n>=4).

## Publicación en GitHub/GitLab

Este proyecto contiene todo el código y pruebas, sin secretos ni dependencias de compilación. Para un repositorio académico público, cree un repositorio vacío en su cuenta y suba estos archivos. Con Git:

```sh
git init
git add .
git commit -m "Implementar modelo financiero y simulador GCP"
git branch -M main
git remote add origin URL_DEL_REPOSITORIO
git push -u origin main
```

La URL del repositorio debe ser real y pública; el ZIP descargable no sustituye este requisito de la consigna. El alojamiento de la página es independiente del repositorio académico.

## Referencias

Suñagua S., P. (2020). Método de Gradientes Conjugados Precondicionado. Revista Boliviana de Matemática – UMSA 04, 2–7. https://cmat.umsa.bo/revmat/rbm4/psunagua_gcp.pdf

Golub y Van Loan (2013), Matrix Computations, 4.ª ed.; Saad (2003), Iterative Methods for Sparse Linear Systems, 2.ª ed. (bibliografía complementaria de la consigna).

## Limitaciones

No se implementan restricciones de optimización, estimación con datos reales ni garantías de rendimiento para sistemas grandes. Se rechazan entradas inválidas; la precisión sigue limitada por IEEE-754. La ganancia medida es de iteraciones, no de tiempo. La interpretación financiera de una matriz editada debe revisarse manualmente. Complete el nombre de estudiante antes de presentar; queda guardado solo en su navegador.
