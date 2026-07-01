# Superando la Migración de SASS: Un Reto de Arquitectura CSS en Equipo

## Contexto
Como equipo de desarrollo compuesto por tres integrantes, asumimos el reto de modernizar la hoja de estilos de nuestra plataforma web. El proyecto inicial arrastraba una arquitectura CSS monolítica y dependía de versiones obsoletas de SASS. Nuestro objetivo principal era migrar el código a las especificaciones modernas de Dart SASS para mejorar los tiempos de compilación y modularizar el diseño visual bajo buenas prácticas.

## Problema
El desafío crítico surgió al eliminar las directivas heredadas `@import`. Al intentar actualizar los archivos, el compilador comenzó a arrojar decenas de errores de variables globales indefinidas y dependencias cruzadas rotas. Al ser tres personas tocando las hojas de estilo simultáneamente, la falta de una estrategia clara generó conflictos masivos de fusión (merge conflicts) en Git, rompiendo el diseño de la interfaz y congelando nuestro flujo de trabajo.

## Acciones realizadas
Para resolver el caos y completar la migración de forma segura, nos dividimos las tareas y ejecutamos el siguiente plan:
1. **Migración Modular con @use y @forward**: Reemplazamos los viejos `@import` por el nuevo sistema de módulos de SASS. Asignamos a un integrante la gestión de los archivos base (variables y mixins) usando espacios de nombres estrictos.
2. **Paralelización por Componentes**: Los otros dos desarrolladores se dividieron los componentes visuales de la interfaz para refactorizar las llamadas de variables (por ejemplo, cambiando `color-primary` por `theme.$color-primary`).
3. **Flujo Riguroso en Git**: Establecemos una rama principal de migración y abrimos Pull Requests (PRs) individuales por cada módulo de estilos. Implementamos la regla de que ningún PR se fusionaba sin la aprobación y revisión de los otros dos miembros del equipo para evitar romper el diseño general.

## Aprendizajes
Esta migración nos dejó lecciones fundamentales como equipo de ingeniería:
* El sistema moderno de módulos de SASS (`@use`) es infinitamente superior para evitar la contaminación del alcance global de variables.
* En hojas de estilo grandes, la arquitectura y el orden de los archivos son tan importantes como la lógica del código de backend.
* El trabajo en equipo requiere una sincronización matemática en Git; de lo contrario, el código visual se desmorona rápidamente.

## Reflexión sobre feedback radicalmente sincero
Durante las primeras revisiones de código, el feedback entre nosotros fue radicalmente sincero y, por momentos, difícil de procesar. Recibimos críticas directas sobre la falta de consistencia en el nombramiento de las nuevas variables y el uso duplicado de mixins. Aunque inicialmente generó debate, esta honestidad brutal nos obligó a detenernos, unificar criterios en una guía de estilos y refactorizar de inmediato. Entendimos que dejar pasar los errores por "amabilidad" solo habría heredado un código defectuoso al proyecto.
