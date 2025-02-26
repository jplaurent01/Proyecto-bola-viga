# Proyecto-bola-viga

## Planta bola viga

Una bola se coloca sobre una viga, como se muestra en la figura a continuación, donde se le permite rodar con un grado de libertad a lo largo de la viga. Un brazo de palanca está unido a la viga en un extremo y a un engranaje servo en el otro. A medida que el engranaje servo gira un ángulo $\Theta$, el brazo de palanca cambia el ángulo de la viga en $\alpha$. Cuando el ángulo se desvía de la posición horizontal, la gravedad hace que la bola ruede a lo largo de la viga. Se diseñará un controlador para este sistema con el fin de manipular la posición de la bola.

![Diagrama del sistema bola-viga](./Imagenes/bola_viga.png)

### Parámetros del sistema 

+ (m) masa de la bola (PESAR BOLA kg)
+ (R) radio de la bola 0.02 m 
+ (d) desplazamiento del brazo de palanca (MEDIR en metros) (Rueda de 0.04 m de radio)
+ (g) aceleración gravitacional 9.8 m/s²
+ (L) longitud de la viga 0.528 m (Preguntar si es L/2 por la ubicación de $\alpha$ al medio de la barra)
+ (J) momento de inercia de la bola ($I=\frac{2}{5} m R^2$) kg·m²
+ (r) coordenada de posición de la bola
+ ($\alpha$) coordenada del ángulo de la viga
+ ($\theta$) coordenada del ángulo del engranaje servo

### Criterios de diseño 

+ Tiempo de establecimiento < 3 segundos
+ Sobrepaso < 5%

### Sistema de ecuaciones

La segunda derivada del ángulo de entrada $\alpha$ en realidad afecta la segunda derivada de $r$.  
Sin embargo, ignoraremos esta contribución. La ecuación de movimiento lagrangiana para la bola está dada por la siguiente expresión:

$$
0 = \left(\frac{J}{R^2}+m\right) \ddot{r} + m g \sin{\alpha} - m r \dot{\alpha}^2
$$

La linealización de esta ecuación sobre el ángulo de la viga, $\alpha = 0$, nos da la siguiente aproximación lineal del sistema:

$$
\left(\frac{J}{R^2}+m\right) \ddot{r} = - m g \alpha
$$

La ecuación que relaciona el ángulo de la viga con el ángulo del engranaje puede aproximarse como lineal mediante la siguiente ecuación:

$$
\alpha = \frac{d}{L}\theta
$$

Sustituyendo esto en la ecuación anterior, obtenemos:

$$
\left(\frac{J}{R^2}+m\right) \ddot{r} = - m g \frac{d}{L} \theta
$$

Tomando la transformada de Laplace de la ecuación anterior, se obtiene la siguiente ecuación:

$$
\left(\frac{J}{R^2}+m\right) R(s) s^2 = - m g \frac{d}{L} \Theta(s)
$$

Reordenando, encontramos la función de transferencia del ángulo del engranaje ($\Theta(s)$) a la posición de la bola ($R(s)$):

$$
P(s) = \frac{R(s)}{\Theta(s)} = -\frac{mgd}{L \left(\frac{J}{R^2}+m\right)} \frac{1}{s^2} \qquad \left[ \frac{m}{rad} \right]
$$

Cabe señalar que la función de transferencia de la planta anterior es un doble integrador. Como tal, es marginalmente estable y presentará un problema de control desafiante.

## 2. Ecuaciones de Estados  

El sistema linealizado también puede representarse en forma de espacio de estados. Para ello, seleccionamos la posición de la bola (\(r\)) y su velocidad (\(\dot{r}\)) como variables de estado, y el ángulo del engranaje (\(\theta\)) como entrada. La representación en espacio de estados se muestra a continuación:  

$$ \left[{\begin{array}{c} \dot{r} \\ \ddot{r} \end{array}}\right] =
\left[{\begin{array}{cc} 0 & 1 \\ 0 & 0 \end{array}}\right]
\left[{\begin{array}{c} r \\ \dot{r} \end{array}}\right] +
\left[{\begin{array}{c} 0 \\ -\frac{m g d}{L
\left(\frac{J}{R^2}+m\right)}\end{array}}\right] \theta$$

Sin embargo, para nuestro ejemplo en espacio de estados utilizaremos un modelo ligeramente diferente. La ecuación de la bola sigue siendo la misma, pero en lugar de controlar la posición a través del ángulo del engranaje (\(\theta\)), controlaremos directamente el torque aplicado al brazo. La representación de este sistema es la siguiente:  

$$ \left[{\begin{array}{c} \dot{r} \\ \ddot{r} \\ \dot{\alpha} \\ \ddot{\alpha} \end{array}}\right] =
\left[{\begin{array}{cccc} 0 & 1 & 0 & 0 \\ 0 & 0 & \frac{-m g}{\left(\frac{J}{R^2}+m\right)} & 0 \\ 0 & 0 & 0 & 1 \\ 0 & 0 & 0 & 0 \end{array}}\right]
\left[{\begin{array}{c} r \\ \dot{r} \\ \alpha \\ \dot{\alpha} \end{array}}\right] +
\left[{\begin{array}{c} 0 \\ 0 \\ 0 \\ 1 \end{array}}\right] u $$

$$ y = \left[{\begin{array}{cccc} 1 & 0 & 0 & 0 \end{array}}\right]
\left[{\begin{array}{c} r \\ \dot{r} \\ \alpha \\ \dot{\alpha}
\end{array}}\right] $$

> **Nota:** En este sistema, el engranaje y el brazo de palanca no se utilizarán. En su lugar, un motor en el centro del brazo aplicará torque para controlar la posición de la bola.
