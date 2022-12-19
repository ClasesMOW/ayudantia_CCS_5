# Ayudantia CCS 5: Modelos Nulos, Mataching e intro a análisis de texto




# Tema 1: Modelos nulos

El primero no es un modelo nulo, pero una apliacción de un concepto similar. El índice de Balassa.

# Ejercicio 1: (pendiente de antes) Carreras más matriculadas por región.

Para este segundo ejercicio, trabajaremos los datos de postulación a carreras universitarias en chile. Para esto contamos con la sigiguiente [carpeta de datos](https://www.dropbox.com/sh/uw9u1wlwi189lbw/AAD9rRWQsBLskP3hexaOk08Ba?dl=0)

Cuando queremos hacer rankings, en general no basta con solo contar la frecuencia de eventos. Tenemos que considerar efectos de tamaños. Por ejemplo, para hacer comparables todas las carreras y todas las regiones, necesitamos considerar la cantidad total de postulantes de cada región y la cantidad de postulaciones a cada carrera. Una medida que controla por estos dos factores es el Balassa Index o RCA.

Por lo general, $M_{cp}$ se define como $M_{cp} = 1$ cuando la cantidad de postulaciones a una carrera en una comuna es mayor que lo esperado para una comuna del mismo tamaño y una carrera con la misma cantidad de postulciones.

$$ M_{cp} = 1 \quad \text{if} \quad R_{cp} \geq 1$$

Donde
$$ R_{cp}= \fraq{X_{cp}}{X_c X_o / X}$$

- $X_{cp}$ es la matriz que asocia comunas y carreras y cada entrada corresponde al número de postulaciones en una comuna c de una carrera p. 
- $X_{p} = \sum_p X_{cp}$ es el vector que contiene el número de postulaciones en todas las carreras para cada comuna c.
- $X_{c} = \sum_{p} X_{cp}$ es el vector que contiene el número de postulaciones en todas las comunas para cada carrera p. 
- $X = \sum_{cp} X_{cp}$ es el número total de postulaciones. 

Por tanto, el denominador de la ecuación $R_{cp}$ corresponde a las postulaciones esperadas de la comuna c para la carrera p. En otras palabras, el denominador es igual a la cantidad de postulaciones de una comuna ($X_c = \sum_p X_{cp}$) multiplicado por la cantidad de postulaciones totales a esa carrera (X = [eXçp) dividido por el número total de postulaciones utilizadas en todo el país (L Xp).


1) Para cada región, construya el ranking de las top-10 carreras más postuladas en primera opción. (Ordene las columnas usando el Balassa index no-discreto; balassa_index(incidence_matrix_1st,discrete=F))

2) Construya el ranking top-10 para las siguientes comunas: Santiago, Las Condes, Vitacura, Providencia, Puente Alto, Maipú, Concepción, Valparaíso, Viña del Mar, La Serena, Antofagasta, Iquique, Arica, Rancagua, Talca, Linares, Los Ángeles, Chillán, Temuco, Osorno, Puerto Montt, Castro, Chiloé, Aysén, Punta Arenas.

3) Grafique la red de carreras usando balassa_index(incidence_matrix_1st, discrete = T) y avg_links = 8.

4) Repita el punto 2 con una red a nivel a nivel región y comuna (ojo con los valores nan de la matriz de proximidad).


## Ejercicio 2: Phi Correlation


## Ejercicio 3: Degree preserving randomization


# Tema 2: Intro a analítica de textos



# Tema 3: Matching en la práctica (ahora si!!!)
