# Ayudantia CCS 5: Modelos Nulos e intro a análisis de texto

# Tema 1: Modelos nulos

El primer tema que revisaremos se refiere a modelos nulos.

Para poder atribuir causalidad a un fenómeno, generalmente necesitamos con un contrafactual válido, lo cual sabemos en muchos casos es imposible.

En ese sentido un modelo nulo es una manera de construir artificialmente un contrafactual que conserva ciertas características del fenómeno y aleatoriza la que creemos es la causao algun elemento en el que nos estamos centrando.

Un modelo nulo es construido para evaluar la hipótesis de que la existencia de cierto patrón observado es puede ser explicado puramente por azar.

En el caso de las redes, construimos modelos nulos como redes que coinciden con la red empírica/observada en ciertas caracterósticas o cumple un conjunto de restricciones, pero que fuera de eso, siguen una estructura aleatoria en su construcción.

En palabras simples, queremos tener algun punto de comporación, para poder atribuir el fenómeno observado a la causa o variable de interés.

Vamos a revisar 3 ejemplos aplicados:

1- Degree preserving radomization aplicado en la red de colegios 

2- Aplicación del índice de Balassa 

3- Phi correlation randomization

## Ejercicio 1: Degree preserving randomizaction

En esta primera aplicación, volveremos a los datos de los colegios para ejemplificar la implementación de una randomización sencilla.

1.  Cargue los datos de intercambios diádicos de un curso y construlla la red direccionada en la cual al menos uno de los dos integrantes envio 10 tokens.

2.  Obtenga la matriz de incidencia de cada red.

3.  Genere un modelo nulo,manteniendo el total de envios (out degree) de cada nodo.

4.  Genere un modelo nulo manteniendo el total de recepciones (in degree) de cada nodo.

## Ejercicio 2: Red de carreras y phi-correlation.

En este segundo ejercicio pasaremos a trabajar con los datos de postulación a carreras universitarias en Chile.

![](images/image-1114643002.png)

Para esto contamos con la sigiguiente [carpeta de datos](https://www.dropbox.com/sh/uw9u1wlwi189lbw/AAD9rRWQsBLskP3hexaOk08Ba?dl=0)

En esta red, bipartita, consideraremos dos carreras conectadas si son co-postuladas por un individuo.

Cada individuo, anonimizado con un MRUN único, puede postular a una o más carreras. Nosotros, vamos a crear una red de carreras en función de  las postulaciones de los individuos. Posteriormente analizaremos esta red.

Como se muestra en la figura, cada individuo puede postular a una o mas carreras con el mismo nombre. Para estos efectos, fitraremos los enlaces con carreras repetidas en ambos extremos (bucles).

**Nota: Para construir la red de carreras usamos una agregación propuesta y operacionalizada por el Ministerio de Educación de Chile, llamada carrear genérica. De esta forma, podemos agrupar todas las carreras que son equivalentes pero tienen distintos nombres en distintas instituciones.**

![](images/image-237919864.png)
 
 1. Cosntruyamos la matriz de incidencia y su proyección como red de carreras.
 
 
1. 1) Lea el archivo `POSTULACIONES.csv`. Use `read.csv("./Data/POSTULACIONES.csv", header=T, sep = ';')`

1.2) Filtre las entradas para `AÑO_PROCESO >=2012`, `SITUACION_POSTULANTE=="P"` (P: postulaciones válidas) y `EDAD<=21`. Use de la libreria `tidyverse` el comando `filter`.

1.3) Crear la matriz de incidencia. Las filas representan personas (`MRUN`) y las columnas carreras (`CODIGO_CARRERA_GENERICA`)

1.4) Use `graph_from_incidence_matrix` para crear el objeto igraph. Luego, proyecte la red sobre la dimensión de carreras.


 
 2. Esta red cuenta con mucho ruido, vamos a filtrarla mediante phi correlation es decir, su significancia estratégica. 
En general las redes bipartitas capturan señal y ruido. 

Ahora, vamos a repetir el ejercicio anterior pero filtrando la red en función de la significancia de sus enlaces.


![](images/image-299266272.png)


Para esto usaremos la siguiente función:


```
#### Filtra red bipartita usando correlación phi y t-statistic ####

phi_correlation<-function(net){
    require(stats)
    
    d_free=t_bin=phi=t=phi_filterd=matrix(ncol = length(net[,1]),nrow = length(net[,1]))
    N=sum(net)#Suma en toda la matriz
    k=0

    for(i in 1:length(net[,1])){
      Mi<-sum(net[,i])
      for(j in 1:length(net[,1])){
        k=k+1
        Mj<-sum(net[,j])
        phi[i,j]<-((net[i,j]*N)-(Mi*Mj))/(sqrt(Mi*Mj*(N-Mi)*(N-Mj)))
        D=max(Mi,Mj)
        d_free[i,j]=D
        phi_cut=ifelse(phi[i,j]>=0,phi[i,j],0) # solo correlaciones postivias
        #https://www.statology.org/p-value-from-t-score-python/
        #https://courses.lumenlearning.com/introstats1/chapter/testing-the-significance-of-the-correlation-coefficient/
        t[i,j]<-phi_cut*(sqrt(D-2))/sqrt(1-phi_cut^2)#T-value for correlations
#         c_val=qt(0.05, D-2, lower.tail = FALSE)#Critical value in the distribution de la acumulada
        pval<-pt(q=t[i,j], df=D-2, lower.tail=FALSE)
#         t[i,j]<-ifelse(t[i,j]>=c_val,t[i,j],0) #usa la acumulada
        t[i,j]<-ifelse(pval<=0.05,t[i,j],0) 
        t_bin[i,j]<-ifelse(t[i,j]>=c_val,1,0)# matriz binaria 
        phi_filterd[i,j]<-ifelse(t[i,j]>=c_val,phi[i,j],0)
      }
    }

    colnames(t)=colnames(net)
    rownames(t)=rownames(net)
    colnames(t_bin)=colnames(net)
    rownames(t_bin)=rownames(net)
    colnames(phi_filterd)=colnames(net)
    rownames(phi_filterd)=rownames(net)
#     return(gg<-list(t_bin,phi_filterd))
    return(t_bin)
}
```

## Ejercicio 3: Aplicando el índice de Balassa para identificar las carreras más matriculadas por región.

Para este tercer ejercicio, seguiremos trabajaremos los datos de postulación a carreras universitarias en chile. Para esto contamos con la sigiguiente [carpeta de datos](https://www.dropbox.com/sh/uw9u1wlwi189lbw/AAD9rRWQsBLskP3hexaOk08Ba?dl=0)

En este ejercicio no usaremos un modelo nulo, pero la aplicación de un concepto similar: El índice de Balassa.

Cuando queremos hacer rankings, en general no basta con solo contar la frecuencia de eventos. Tenemos que considerar efectos de tamaños. Por ejemplo, para hacer comparables todas las carreras y todas las regiones, necesitamos considerar la cantidad total de postulantes de cada región y la cantidad de postulaciones a cada carrera. Una medida que controla por estos dos factores es el Balassa Index o RCA.

Por lo general, $M_{cp}$ se define como $M_{cp} = 1$ cuando la cantidad de postulaciones a una carrera en una comuna es mayor que lo esperado para una comuna del mismo tamaño y una carrera con la misma cantidad de postulciones.

$$ M_{cp} = 1 \quad \text{if} \quad R_{cp} \geq 1$$

Donde $$ R_{cp}= \fraq{X_{cp}}{X_c X_o / X}$$

-   $X_{cp}$ es la matriz que asocia comunas y carreras y cada entrada corresponde al número de postulaciones en una comuna c de una carrera p.
-   $X_{p} = \sum_p X_{cp}$ es el vector que contiene el número de postulaciones en todas las carreras para cada comuna c.
-   $X_{c} = \sum_{p} X_{cp}$ es el vector que contiene el número de postulaciones en todas las comunas para cada carrera p.
-   $X = \sum_{cp} X_{cp}$ es el número total de postulaciones.

Por tanto, el denominador de la ecuación $R_{cp}$ corresponde a las postulaciones esperadas de la comuna c para la carrera p. En otras palabras, el denominador es igual a la cantidad de postulaciones de una comuna ($X_c = \sum_p X_{cp}$) multiplicado por la cantidad de postulaciones totales a esa carrera (X = [eXçp) dividido por el número total de postulaciones utilizadas en todo el país (L Xp).

1)  Para cada región, construya el ranking de las top-10 carreras más postuladas en primera opción. (Ordene las columnas usando el Balassa index no-discreto; balassa_index(incidence_matrix_1st,discrete=F))

2)  Construya el ranking top-10 para las siguientes comunas: Santiago, Las Condes, Vitacura, Providencia, Puente Alto, Maipú, Concepción, Valparaíso, Viña del Mar, La Serena, Antofagasta, Iquique, Arica, Rancagua, Talca, Linares, Los Ángeles, Chillán, Temuco, Osorno, Puerto Montt, Castro, Chiloé, Aysén, Punta Arenas.

3)  Grafique la red de carreras usando balassa_index(incidence_matrix_1st, discrete = T) y avg_links = 8.

(Tarea: 4) Repita el punto 2 con una red a nivel a nivel comuna en lugar de regiones y grafique la red de comunas (ojo con los valores nan de la matriz de proximidad)).

# Tema 2: Intro a analítica de textos
