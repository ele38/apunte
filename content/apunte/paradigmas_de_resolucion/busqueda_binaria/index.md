---
title: "Búsqueda binaria"
type: docs
menu:
    apunte:
        identifier: "apunte-paradigmas_de_resolucion-busqueda_binaria"
        parent: "apunte-paradigmas_de_resolucion"
weight: 10 # El menú lateral ordena artículos por su peso
---
## Introducción y búsqueda lineal

A veces necesitamos buscar algún elemento en algún espacio. Un ejemplo típico es buscar un número en un arreglo o vector, lo cual se puede hacer con una búsqueda lineal:

```c++
bool en_arreglo(vector <int> &vec, int x){
  for(int y : vec)
    if(y == x) return true;
  return false;
}
```

Si nuestros datos no tienen ninguna propiedad que podamos aprovechar (por ejemplo son aleatorios), probablemente esta sea la mejor forma, a menos que insertemos nuestros elementos en una estructura diferente.
Como itera por todos los elementos del vector, tiene complejidad $O(n)$, con $n$ el tamaño del vector.

Sin embargo, si nuestro espacio de búsqueda presenta propiedades especiales, podemos buscar mucho más rápido usando búsqueda binaria. Específicamente para búsqueda binaria, necesitamos encontrar una función que cumpla la propiedad de monotonía.

## Monotonía

La búsqueda binaria no sirve sólo para buscar en un arreglo ordenado. Para generalizarla un poco, necesitamos un par de definiciones:

- Función binaria: Una función binaria es una función $f$ que toma elementos de nuestro espacio y retorna $\texttt{true}$ o $\texttt{false}$.

- Monotonía: Una función binaria $f$ es monótona si cumple las siguientes propiedades:

  - $f(x) = \texttt{true} \implies f(y) = \texttt{true}$, $\forall y > x$
  - $f(x) = \texttt{false} \implies f(y) = \texttt{false}$, $\forall y < x$

  Intuitivamente, esto nos dice que una función monótona es igual a $\texttt{false}$, y desde cierto punto en adelante es siempre igual a $\texttt{true}$.

La búsqueda binaria nos permitirá encontrar de forma eficiente el punto en el que nuestra función binaria monótona cambia de $\texttt{false}$ a $\texttt{true}$ (o viceversa, sin pérdida de generalidad).

Para simplificar, nos referiremos a una "función binaria monótona" como una "condición".

## Búsqueda binaria

Una vez identifiquemos la propiedad de monotinicidad en nuestro problema, podemos hacer búsqueda binaria.

La búsqueda binaria es una aplicación de la estrategia _divide and conquer_. En cada iteración iremos reduciendo nuestro espacio de búsqueda a la mitad, logrando así una complejidad $O(\log n)$ donde $n$ es el tamaño del espacio de búsqueda, dado que dividiendo $n$ a la mitad $\log n$ veces llegamos a un espacio de tamaño $1$. (_Nota: en computación, logaritmo es por defecto con base $2$, aunque igual no importa para la complejidad porque es una constante_)

Imaginemos que tenemos una condición. En el siguiente diagrama, una $F$ significa que la condición es falsa y una $V$ que es verdadera, y nuestro objetivo es encontrar la primera $V$. El rectángulo indica nuestro espacio de búsqueda actual (inicialmente todo) y la flecha el elemento central del espacio de búsqueda:

<center> <img class="invertible" src="img/bbin_1.png" width="450"/> </center>

Nuestra flecha divide al espacio en dos mitades. Nos aprovecharemos de la monoticidad para descartar una de ellas: como actualmente estamos mirando una $F$, sabemos que todo lo de la izquierda son $F$, por lo que nuestra respuesta debe estar hacia la derecha. Descartamos la mitad izquierda:

<center> <img class="invertible" src="img/bbin_2.png" width="450"/> </center>

Ahora, la flecha apunta a una $V$. Análogamente a antes, todo lo de la derecha es $V$, así que nos quedamos con la mitad izquierda:

<center> <img class="invertible" src="img/bbin_3.png" width="450"/> </center>

Repitiendo el algoritmo, eventualmente llegaremos a un solo elemento:

<center> <img class="invertible" src="img/bbin_4.png" width="450"/> </center>
<center> <img class="invertible" src="img/bbin_5.png" width="450"/> </center>

## Implementación

### Encontrar primer valor donde la condición es verdadera

Aunque la búsqueda binaria tiene una naturaleza recursiva (y se puede programar así), lo implementaremos iterativamente. Para ello tendremos dos números enteros $l$ (de _left_) y $r$ (de _right_), los cuales apuntarán a los extremos de nuestro espacio de búsqueda actual.

Mientras $l$ sea estrictamente menor a $r$, el algoritmo iterará. En cada iteración, calcularemos $mid$ como $(l+r)/2$, y tenemos dos casos:

- La condición en $mid$ es verdadera: asignamos $r=mid$, descartando la mitad derecha.
- La condición en $mid$ es falsa: asignamos $l=mid+1$, descartando la mitad izquierda incluyendo a $mid$.

Así, encontraremos el primer elemento tal que la condición es verdadera.

{{% details "Código" %}}

```c++
/*  Búsqueda Binaria
 *  l: inicio del espacio de búsqueda (int o long long)
 *  r: fin del espacio de búsqueda (int o long long)
 *  mid: centro del espacio de búsqueda (int o long long)
 *  Requiere que la condición sea "monótona": es falsa hasta cierto punto,
 *  después es siempre verdadera.
 *  Busca el primer valor entre l y r que hace la propiedad verdadera.
 */
while(l < r){
  mid = (l+r)/2; // se puede usar l+(r-l)/2 para evitar overflow por la suma
  if(/* condición es verdadera en mid */) r = mid;
  else l = mid+1;
}
if(/* condición es falsa en l */)
  /* quejarse! la condición es falsa para todos los valores
  del espacio de búsqueda inicial */
else
  /* l (que en este punto es igual a r) es el primer valor
  en el espacio donde se cumple la condición */
```

{{% /details %}}

### Encontrar último valor donde la condición es falsa

Para esto, se puede buscar el primero donde es verdadera y restar uno. O también podemos modificar un
poco el ciclo del código anterior:

{{% details "Código" %}}

```c++
while(l < r){
  mid = (l+r+1)/2; // ahora hacemos la división con techo en vez de piso
  if(/* condición es verdadera en mid */) r = mid-1; // ahora aquí descartamos mid
  else l = mid; // acá ahora no descartamos mid porque puede ser el último falso
}
```

{{% /details %}}

### Búsqueda binaria en $\mathbb{R}$

Hay veces en la que nuestro espacio de búsqueda no es un subconjunto de los números enteros, si no que la respuesta también puede ser un número decimal. En este caso, debemos hacer dos cambios:

- Ya no podemos hacer $\texttt{l=mid+1}$, pues la respuesta puede estar en el intervalo $(l, mid+1)$.
- La condición $\texttt{while(l < r)}$ ya no nos sirve, porque $l$ siempre será menor a $r$.

Para esto, debemos fijar un número de iteraciones, y la precisión de nuestra respuesta dependerá de este número. Usualmente $\texttt{50}$ es suficiente, porque la precisión aumenta muy rápidamente con las iteraciones (cada $4$ iteraciones nos acercamos en un factor de $16$ a la respuesta).

{{% details "Código" %}}

```c++
// IMPORTANTE: acá l, r y mid son doubles (o long doubles para más precisión)
int NUMERO_ITERACIONES = 50;
while(NUMERO_ITERACIONES--){
  mid = (l+r)/2;
  if(/* condicion es verdadera en mid */) r = mid;
  else l = mid;
}
```

Otra forma de hacer el ciclo es esperar hasta que la diferencia entre $l$ y $r$ sea menor a un epsilon que definamos:

```c++
double EPS = 1e-6;
while(r-l > EPS){
  //... lo mismo de antes
}
```

{{% /details %}}

## Búsqueda Ternaria

Pensando en números enteros, uno usualmente puede hacer búsqueda binaria sobre funciones que son crecientes o decrecientes usando una condición tipo desigualdad. Por ejemplo, si tenemos un arreglo que es creciente y queremos buscar un número $c$, buscamos el primer número $x$ que cumpla la condición $x \geq c$ y al encontrarlo verificamos si $x=c$ o no.

A veces pasa que podemos tener funciones que no sean crecientes ni decrecientes. Es decir, funciones que

- Son estrictamente crecientes, alcanzan su máximo y luego son estrictamente decrecientes, o
- Son estrictamente decrecientes, alcanzan su mínimo y luego son estrictamente crecientes.

Resulta que en este caso también podemos encontrar el mínimo/máximo usando una variante de la búsqueda binaria.

Sin pérdida de generalidad (el otro caso es simétrico), digamos que tenemos una función $f$ que primero decrece, alcanza su mínimo y luego crece.

### El caso discreto

Si tenemos una cantidad discreta de datos sobre los que buscar, podemos convertir el problema a uno de búsqueda binaria.

Podemos definir una condición como "f es creciente". Obviamente, la condición es falsa en el tramo donde $f$ decrece, y desde que alcanza su mínimo en adelante es verdadera. Así, tenemos la monotonicidad sobre la cual podemos hacer busqueda binaria.

{{% details "Código" %}}
Nota que en el código $f$ es una función, no un arreglo/vector.
Para el caso donde la función alcanza un máximo, deberíamos hacer la condición como "f es decreciente".

```c++
while(l < r){
  mid = (l+r)/2;
  if(f(mid+1)-f(mid) > 0) r = mid; // La función es creciente: la condición se cumple
  else l = mid+1;
}
```

{{% /details %}}

### El caso continuo

Para el caso continuo, estamos obligados a hacer búsqueda ternaria. La búsqueda ternaria consiste en tener dos índices entre $l$ y $r$, $mid_1$ y $mid_2$ en vez de solo uno, dividiendo el espacio de búsqueda en tres segmentos de igual largo. Para esto, $mid_1$ debe estar al final del primer tercio y $mid_2$ al inicio del último tercio.

Comparando la diferencia $f(mid_2)-f(mid_1)$ podemos determinar qué segmentos eliminar:

- Si la diferencia es mayor o igual a cero, descartamos el primer segmento.
- Si es menor a cero, descartamos el tercer segmento.

Los siguientes diagramas pueden ayudar a entender la correctitud del algoritmo. En ninguno de los dos casos podemos descartar el segmento del medio, porque $mid_1$ y $mid_2$ podrían estar ambos en la parte creciente o en la parte decreciente al mismo tiempo.

<center> <img src="img/ternary.png" width="850"/> </center>

{{% details "Código" %}}

```c++
// l y r son doubles
double EPS = 1e-6; // Cambiar para más o menos precisión
while(r-l > EPS){
  double mid1 = l+(r-l)/3;
  double mid2 = l+2*(r-l)/3;
  if(f(mid2)-f(mid1) >= 0) r = mid2;
  else l = mid1;
}
// La respuesta queda en l
```

Otra forma es hacer lo mismo que el caso discreto, sumando un epsilon en vez de 1. La desventaja de esto es que puede dar problemas por la precisión de double.
{{% /details %}}

## Problemas para ejercitar

- [BBIN - SPOJ](https://www.spoj.com/problems/BBIN/)
- [BBIN2 - SPOJ](https://www.spoj.com/problems/BBIN2/)
- [Find First and Last Position of Element in Sorted Array - LeetCode](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)
- [Sum of Square Numbers - LeetCode](https://leetcode.com/problems/sum-of-square-numbers/)
- [Median of Two Sorted Arrays - LeetCode](https://leetcode.com/problems/median-of-two-sorted-arrays/)
- [Particle Collision - Kattis](https://open.kattis.com/problems/particlecollision) (Búsqueda Binaria + Geometría)
- [Hobbits - Codeforces](https://codeforces.com/problemset/problem/1468/G 'https://codeforces.com/problemset/problem/1468/G')
- [Three Parts of the Array - Codeforces](https://codeforces.com/problemset/problem/1006/C)
- [Multiplication Table - Codeforces](https://codeforces.com/problemset/problem/448/D)
- [Blurred Pictures - Codeforces (SWERC 2018)](https://codeforces.com/gym/102465/problem/B)
