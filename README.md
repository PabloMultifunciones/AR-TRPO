# AR-TRPO
Optimización de políticas de región de confianza
### Introduccion ### 

Para garantizar que la política no avance demasiado, agregamos una restricción a nuestro problema de optimización en términos de asegurarnos de que la política actualizada se encuentre dentro de una región de confianza. Las regiones de confianza se definen como la región en la que las aproximaciones locales de la función son precisas. Está bien, pero ¿qué significa eso? En las regiones de confianza, determinamos el tamaño de paso máximo y luego encontramos el máximo local de la política dentro de la región. Al continuar el mismo proceso iterativamente, encontramos el máximo global. También podemos ampliar o reducir la región en función de lo buena que sea la nueva aproximación. De esa manera, estamos seguros de que las nuevas políticas pueden ser confiables y no conducirán a una degradación de políticas dramáticamente mala. Podemos expresar matemáticamente la restricción anterior utilizando la divergencia KL (que puede considerar como una distancia entre dos distribuciones de probabilidades):

La divergencia KL entre la política nueva y la antigua debe ser menor que el delta (δ), donde delta es el tamaño de la región. Podría entrar en algo de matemáticas, pero creo que esto solo complicará las cosas en lugar de aclararlas. Entonces, esencialmente, solo tenemos un problema de optimización con restricciones.

<p align="center">
  <img src="https://user-images.githubusercontent.com/95035101/198902072-4e34859c-6a90-4e9e-9628-4f0e0bffc8de.png">
</p>

La pregunta ahora es ¿cómo resolvemos un problema de optimización con restricciones? Usando el método del Gradiente Conjugado. Por supuesto, podemos resolver el problema analíticamente (descenso de gradiente natural), pero es ineficaz desde el punto de vista computacional. Si desempolvas tus conocimientos sobre matemáticas numéricas, quizás recuerdes que el método del gradiente conjugado proporciona una solución numérica a un sistema de ecuaciones. Eso es mucho mejor desde una perspectiva computacional. Así que todo lo que tenemos que hacer es aproximar linealmente la función objetivo y cuadráticamente la restricción y dejar que el gradiente conjugado haga su trabajo.

Para resumir todo, el algoritmo tiene los siguientes pasos:
  
* Ejecutamos un conjunto de trayectorias y recopilamos las políticas.
* Estime las ventajas utilizando el algoritmo de estimación de ventajas.
* Resolver el problema de optimización con restricciones usando gradiente conjugado.
* Repetir.
  
En términos generales, las regiones de confianza se consideran métodos bastante estándar para abordar los problemas de optimización. La parte difícil es aplicarlos en un contexto de aprendizaje por refuerzo de una manera que brinde una ventaja sobre los simples gradientes de políticas.

Aunque TRPO es un algoritmo muy poderoso, adolece de un problema importante: esa maldita restricción, que agrega una sobrecarga adicional a nuestro problema de optimización. Quiero decir que nos obliga a usar el método del gradiente conjugado y nos desconcierta con aproximaciones lineales y cuadráticas. ¿No sería bueno si de alguna manera pudiera incluir la restricción directamente en nuestro objetivo de optimización? Como habrás adivinado, eso es exactamente lo que hace la optimización de políticas proximales.
