---
title: "Trabajos prácticos: Introducción al Modelado Continuo"
date: 2022-12-04
draft: false
author: ["Noé Hsueh", "Juan Wisznia"]
---



## Trabajo Práctico 1: Simulación de Dinámica de Gases

El trabajo práctico se enfoca en el estudio de la dinámica
de gases en un sistema de partículas en movimiento dentro de una caja. 
El sistema modela partículas en una caja en el plano que se repelen entre sí y
colisionan con las paredes. 
 
Se utiliza un potencial repulsivo que varía según la distancia entre las 
partículas y una constante de interacción. Se describe el cálculo de las 
fuerzas entre las partículas y se establece la forma en que actúan en 
función del potencial propuesto. 

[Clickear aquí para abrir el notebook](/tp_imc/tp1.jl.html)

{{< rawhtml >}}
<iframe src="/tp_imc/tp1.jl.html" width="100%" height="300"></iframe>
{{< /rawhtml >}}


## Trabajo Práctico 2: Compresión de Imágenes (jpeg)

En este trabajo práctico, implementaremos una versión simplificada del algoritmo 
de compresión de archivos .jpg, basado en la observación de que las transformadas 
de señales reales generalmente están dominadas por frecuencias bajas y tienen 
una contribución limitada de frecuencias altas. 

La estrategia consiste en realizar la transformada de una señal, 
eliminar las frecuencias por encima de un umbral y almacenar solo un 
conjunto reducido de frecuencias, logrando así una compresión. 
La recuperación de la señal implica completar el vector transformado con 
ceros y aplicar la antitransformación. 
Aunque el algoritmo jpg sigue este principio, también emplea la 
transformada del coseno en lugar de la transformada de Fourier, 
debido a su capacidad para trabajar con números reales y a su mayor 
concentración de información en frecuencias bajas. 
La transformada del coseno se implementa mediante el 
comando dct en la librería FFTW.

[Clickear aquí para abrir el notebook](/tp_imc/tp2.jl.html)

{{< rawhtml >}}
<iframe src="/tp_imc/tp2.jl.html" width="100%" height="300"></iframe>
{{< /rawhtml >}}


## Trabajo Práctico 3: Simulación de Difusión en una taza

El trabajo trata sobre resolver una ecuación que modela la difusión de una 
sustancia en un líquido en un dominio circular. Se emplea el método de líneas y 
se usan coordenadas polares para discretizar y resolver el problema. 
Se imponen condiciones de borde de Neumann en los bordes del dominio,
considerando las particularidades de la geometría circular. 
El objetivo es encontrar la solución numérica de la ecuación 
en un rectángulo del dominio polar.

[Clickear aquí para abrir el notebook](/tp_imc/tp3.jl.html)

{{< rawhtml >}}
<iframe src="/tp_imc/tp3.jl.html" width="100%" height="300"></iframe>
{{< /rawhtml >}}