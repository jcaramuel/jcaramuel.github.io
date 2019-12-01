---
layout: post
title: ¿Por qué el software es tan difícil de desarrollar?
---

*Traducción del artículo ["Why software is so hard to develop"](http://www.drdobbs.com/cpp/why-is-software-so-hard-to-develop/240168832), de Andrew Koenig, publicado en Dr.Dobb's Journal*

**El software es difícil de desarrollar por varias razones: debemos averiguar qué hacer, hacerlo y asegurarnos de que se ha hecho correctamente.**

[La semana pasada](http://www.drdobbs.com/cpp/a-second-try-at-refactoring-dijkstras-ex/240168792) prometí que hablaría de los invariantes. He decidido aparcar momentáneamente este asunto porque cuando comencé a pensar sobre él me di cuenta de que era simplemente una pequeña parte de un tema mucho más amplio. Los invariantes son una herramienta intelectual que podemos usar para incrementar nuestra confianza de que un programa está funcionando como esperamos. No obstante, no resuelve todos los problemas, ni es la única herramienta al respecto. Mientras pensaba acerca de la descripción de los invariantes -particularmente cómo y por qué usarlos- me di cuenta de que podría merecer la pena echarle un vistazo desde una perspectiva más amplia.

Una visión ingenua del desarrollo de software es que "debería ser fácil": todo lo que tienes que hacer es decirle al ordenador qué quieres que haga y él lo hace. No obstante, como cualquiera con una minúscula experiencia en el tema podrá contarte, esta visión da por sentadas demasiadas asunciones que generalmente no son correctas en la práctica:

1. Estás seguro de que sabes qué quieres que haga el ordenador.
2. Tu conocimiento sobre el tema es correcto y completo.
3. Puedes trasladar, exactamente, tu conocimiento a código.
4. El código hace lo que tu crees que hace.
5. El ordenador funciona de la forma que esperas que lo haga.
6. Además de hacer lo que quieres que haga, el programa no hace nada que no quieres que haga.
7. El programa funciona lo suficientemente bien para el uso al que se va a destinar.
8. Si la salida del programa será aproximada, esa aproximación es lo suficientemente precisa.
9. El programa se comporta razonablemente bien cuando encuentra una entrada absurda o maliciosa.

Los dos primeros puntos se refieren a nuestro entendimiento del problema. Puede sonar obvio que tengas que entender el problema antes de que lo puedas resolver, pero esta supuesta obviedad no suele sobrevivir a un encuentro con la realidad del día a día. Muchas veces no entendemos completamente los problemas que tenemos que resolver; ese entendimiento se va volviendo menos incompleto a medida que meditamos cuidadosamente sobre los problemas. Si lo que estamos resolviendo son los problemas de algún otro, en vez de los nuestros, esa meditación puede llevarnos a preguntar a quien tenga el problema que nos clarifique parte del mismo. Ocurra esta aclaración o no, uno de los aspectos más importantes -y más difíciles- del desarrollo de software es entender con claridad qué se supone que debe hacer el software.

Como un ejemplo sencillo, considera el problema de escribir un programa de ordenación. Tal programa generalmente tiene dos entradas: una secuencia de valores y una función de comparación definida en esos valores. Su propósito es reordenar los valores en orden, según la función de comparación. La descripción deja varias preguntas sin responder:

* ¿Qué asunciones podemos hacer sobre la función de comparación? Por ejemplo, ¿es seguro asumir que si la usamos para comparar dos valores, y comparamos esos mismos dos valores más tarde, obtendremos el mismo resultado?
* ¿Cómo devuelve la función de comparación su resultado? Por ejemplo, ¿se trata de un resultado que nos indique *"menor que"*, *"igual"* o *"mayor que"* o se trata por el contrario de un resultado tipo *"si/no"*?
* Si dos valores no son ni mayores ni menores entre si, ¿podemos considerar que son iguales? Si no ¿qué asunciones podemos hacer sobre esos valores?
* Si dos valores son iguales ¿necesitamos mantener su orden original? Es decir, ¿podría haber algo acerca de esos valores que no podemos ver, pero que el llamador sí podría ver?
* ¿Y qué hay sobre dos valores que no son ni mayores, ni menores, entre sí pero *tampoco* son iguales? ¿Debemos mantener el orden original para esos valores?

Nuestro propósito aquí no es responder a esas preguntas; es señalar que hay un buen número de aspectos, incluso para  un problema simple como este, que no tienen una respuesta obvia. Al menos no de entrada.

Una vez entendemos el problema, escribir código es generalmente la parte más fácil. Eso no quiere decir que hacerlo sea fácil. Para comprobarlo por ti mismo, intenta escribir una función de búsqueda binaria sin consultar un libro de texto. Para muchos programadores, escribir un programa como ese puede ser una experiencia humillante -incluso aunque la especificación no sea complicada en absoluto. Es más, muchos programadores que no sientan humillación escribiendo la función de búsqueda binaria, la sentirán cuando empiecen a *probar* el programa.

Probar, por supuesto, es parte de la asunción #4, como lo son ciertas técnicas de programación, tales como definir invariantes, que usamos para saber por adelantado como se comportará nuestro programa ante nuestros tests. Para poner la idea de los invariantes en perspectiva, debemos darnos cuenta de que se tratan solo de una parte muy pequeña de un problema mucho más grande.

La asunción #5 es sorprendentemente importante también. Cada vez que el hardware no funciona correctamente esta asunción cae por su propio peso. Es más, puede incluso caer cuando escribimos código que va más allá de lo que nuestro lenguaje de programación contempla. Si usamos un puntero sin inicializar, por ejemplo, no tenemos manera de saber qué va a hacer la máquina (y estamos seguros de que *probablemente* hará algo). En ese sentido, lo que quiera que haga es erróneo -o correcto- dependiendo de si estamos viendo la situación desde la perspectiva de quien prueba el programa o de la máquina.

La asunción #6 es complicada y creo que presenta un problema espinoso para los defensores de TDD (test-driven development). Es relativamente fácil verificar que un programa produce los resultados que esperas con una entrada específica. Es mucho más complicado verificar que el programa no haga nada más allá de las intenciones de sus usuarios. Como ejemplo sencillo, piensa en cómo probarías un sistema operativo para verificar que nadie ha instalado un keylogger.

Las asunciones #7 y #8 se refieren a la calidad de la implementación: ¿Devuelve el programa su resultado lo suficientemente rápido y exacto para un mundo en el que la gente tiene una paciencia limitada y las bases de datos no siempre corresponden exactamente con la realidad? Si vas a escribir un sistema basado en social-media que esperas que soporte cargas de hasta 50.000 usuarios simultáneos, ¿cómo verificarías que puede funcionar bien? ¿Qué ocurre si 20.000 de esos usuarios están viendo la televisión y, la próxima vez que ponen anuncios, todos comienzan a usar sus teléfonos para conectar con tu sistema al mismo tiempo?

Finalmente está la asunción #9, que tiene que ver con la seguridad. Ha aparecido en las noticias de todo el mundo un buen número de incidentes relacionados con cómo la gente se las ingenia para subvertir sistemas que han sido de uso general por varios años. Esos incidentes por si solos ya deberían ser suficiente para convencernos de que nuestra industria no hace un buen trabajo para defenderse de los chicos malos, creando condiciones extravagantes que hacen que el software se comporte de manera errática, permitiendo que esos chicos malos pueden explotar.

En definitiva, el software es difícil de desarrollar por varias razones: debemos saber qué hacer, hacerlo y asegurarnos de que lo hemos hecho correctamente. Los invariantes son sólo una de las muchas herramientas que podemos emplear durante el proceso. Pero, a pesar de ser una de esas muchas herramientas, los invariantes son particularmente importantes porque son mucho más poderosos que lo que su aparente simplicidad puede sugerir y porque muchos programadores no los entienden bien. Por esas razones, hablaremos de ellos; en futuros articulos trataremos sobre esos otros problemas. (N. del T. evidentemente no voy a traducir toda la serie de artículos :-)
