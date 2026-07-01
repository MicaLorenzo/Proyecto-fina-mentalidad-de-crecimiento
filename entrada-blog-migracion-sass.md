# Migrar a SASS en equipo: cuando el problema no es el código, son los tipeos

## Contexto

Como parte del proyecto final, mi equipo (tres integrantes) tomó un sitio web ya existente, escrito en CSS plano, y nos propusimos migrarlo a **SASS/SCSS** para aprovechar variables, anidamiento y partials, y así ordenar mejor los estilos antes de seguir sumando funcionalidades.

Nos dividimos el trabajo: un compañero armó la estructura inicial de carpetas y el archivo `main.scss` con las variables base (colores, tipografías, breakpoints), otro se encargó de migrar los componentes de layout, y yo quedé a cargo de los componentes de UI (botones, formularios, tarjetas). Todo se manejaba con control de versiones desde el principio, usando ramas por persona y Pull Requests para integrar a `main`.

## Problema

Apenas empezamos a trabajar en paralelo, aparecieron los problemas. El más grave: **los cambios que hacíamos en los archivos `.scss` no se veían reflejados en el CSS final.** Cambiábamos un color, recompilábamos, y en el navegador seguía apareciendo el estilo viejo. Al principio pensamos que era caché del navegador, pero el problema persistía incluso en modo incógnito.

Después de perder bastante tiempo, nos dimos cuenta de que el problema real estaba en el **código base que uno de los integrantes había compartido con el equipo**: tenía varios errores de tipeo que no generaban un error visible, pero rompían la compilación silenciosamente. Por ejemplo:

- Una variable definida como `$primary-clor` (en vez de `$primary-color`), usada correctamente en otros archivos, hacía que SASS generara el valor por defecto en lugar de tirar error.
- Un `@import` apuntaba a `_buttoms.scss` en vez de `_buttons.scss`, así que ese partial ni siquiera se estaba incluyendo en la compilación final, y nosotros seguíamos editando un archivo que jamás llegaba al CSS.
- Un selector escrito como `.btn-Primary` (con mayúscula) no coincidía con el HTML, que usaba `.btn-primary`, por lo que los estilos "existían" pero nunca se aplicaban.

Cada persona del equipo terminó perdiendo tiempo debuggeando el mismo tipo de problema por separado, sin saber que la causa era compartida.

## Acciones realizadas

1. Nos juntamos como equipo a revisar el árbol de imports completo (`main.scss` y todos los `@import`/`@use`) en vez de seguir revisando archivo por archivo de forma aislada.
2. Detectamos el import roto de `_buttoms.scss` comparando los nombres de archivo reales en la carpeta contra lo que se importaba.
3. Usamos el modo watch de Sass (`sass --watch`) mostrando la salida en consola, en vez de compilar "a ciegas", para detectar si un archivo realmente se estaba procesando.
4. Hicimos una revisión cruzada de variables: cada integrante comparó los nombres de variables que usaba contra el archivo `_variables.scss` original, y así aparecieron los otros tipeos.
5. Corregimos los nombres (`$primary-color`, `_buttons.scss`, `.btn-primary`) y agregamos un comentario en el PR explicando exactamente qué se había corregido y por qué.
6. Como medida preventiva, agregamos un `.stylelintrc` básico para que errores de nombres de clases y variables mal escritas se marquen automáticamente antes de mergear.

## Aprendizajes

- **Los errores de tipeo en SASS pueden ser silenciosos.** A diferencia de un error de sintaxis, un nombre de variable o import mal escrito no siempre rompe la compilación; simplemente no hace lo que uno espera.
- **Trabajar en equipo sobre el mismo código base amplifica los errores pequeños.** Un solo tipeo en un archivo compartido nos hizo perder tiempo a los tres, no a una sola persona.
- **Revisar el árbol de imports completo antes de debuggear archivo por archivo** ahorra mucho tiempo: el problema muchas veces no está en el CSS que se ve, sino en lo que nunca llegó a incluirse.
- Automatizar el chequeo de nombres (linter) evita que este tipo de error dependa solo de la atención de cada persona.

## Reflexión sobre feedback radicalmente sincero

Cuando encontramos el origen del problema, tuvimos que tener una conversación incómoda dentro del equipo: había que decirle al compañero que había compartido el código base que esos errores de tipeo nos habían costado horas de trabajo a todos. Fue un feedback directo, sin vueltas: "el código funcionaba en tu máquina, pero no lo revisaste antes de compartirlo, y eso nos afectó a todos".

Al principio hubo algo de tensión, porque nadie se equivoca a propósito y la corrección se sintió como una crítica personal. Pero encararlo de forma sincera, en vez de simplemente arreglar el error en silencio, nos sirvió para establecer una práctica nueva: antes de compartir código base con el equipo, alguien más lo revisa o al menos se corre el build una vez para confirmar que compila sin errores.

Ese intercambio nos enseñó que el feedback radicalmente sincero no busca señalar culpables, sino evitar que el mismo error se repita. Y en un trabajo en equipo, decir las cosas claramente a tiempo termina ahorrando mucho más tiempo (y frustración) que evitar el tema por incomodidad.
