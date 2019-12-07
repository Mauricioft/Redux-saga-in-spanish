# Read Me

redux-saga es una biblioteca que tiene como objetivo hacer que los efectos secundarios de la aplicación \(es decir, cosas asincrónicas como la obtención de datos y cosas impuras como acceder al caché del navegador\) sean más fáciles de administrar, más eficientes de ejecutar, fáciles de probar y mejores para manejar fallas.

El modelo mental es que una saga es como un hilo separado en su aplicación que es el único responsable de los efectos secundarios. redux-saga es un middleware redux, lo que significa que este hilo puede iniciarse, pausarse y cancelarse desde la aplicación principal con acciones redux normales, tiene acceso al estado completo de la aplicación redux y puede enviar acciones redux también.

Es posible que haya usado redux-thunk antes para manejar su recuperación de datos. Contrariamente al redux thunk, no terminas en el infierno de devolución de llamada, puedes probar tus flujos asincrónicos fácilmente y tus acciones se mantienen puras.

