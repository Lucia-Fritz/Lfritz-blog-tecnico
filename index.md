# Entrada del blog técnico

## Contexto

Durante el lanzamiento de la aplicación mobile de la red social **TeaHour**, los usuarios ya logueados notificaron sobre inconvenientes durante el proceso de publicación de comentarios en tiempo real. 

La aplicación permitía a los usuarios registrados crear publicaciones de texto, interactuar mediante "Me gusta" y agregar comentarios en tiempo real. Sin embargo, comenzaron a detectarse inconsistencias al utilizar la funcionalidad **_"Publicar comentario"_**, lo que ocasionaba fallas durante el proceso de publicación.

El objetivo del proyecto era garantizar una experiencia fluida, estable y consistente durante todas las interacciones.

---

## Problema

Se detectó que los usuarios podían escribir su comentario en la sección de **_“agregar comentario”_** sin inconvenientes. Sin embargo, en algunas ocasiones, el contenido desaparecía al momento de publicarlo. 

Esto generaba **frustración y confusión en los usuarios**, ya que el comentario se publicaba sin contenido de texto y la aplicación no mostraba mensajes de error ni alertas que explicaran lo sucedido durante el proceso de publicación de comentarios.

---

## Acciones realizadas

Para resolver el incidente se llevaron a cabo las siguientes medidas:

- Revisar el problema detectado.
- Realizar testeos para verificar el problema.
- Identificar en qué parte del código estaba el problema.
- Implementar un **_“fix”_** (parche) en el código.
- Realizar testeos para verificar la solución del problema.
- Documentar el cambio mediante control de versiones utilizando **Git** y **GitHub**.

---

## Post-mortem constructivo

### Resumen del incidente

En determinadas ocasiones, los usuarios autenticados publicaban comentarios cuyo contenido aparecía vacío.
El problema se debía a que la función encargada de **_limpiar el campo de texto_** se ejecutaba antes de que finalizara el envío del comentario a la base de datos.

---

### Causa raíz

Dentro del código, la función **_setComentario_**, que se utiliza para limpiar el texto después de presionar el botón **_“Publicar comentario”_**, se estaba ejecutando antes de que el comentario fuera enviado a la base de datos. Esto ocurría porque **_setComentario_** estaba ubicado más arriba en el código y esto generaba una condición de carrera (situación indeseable que ocurre en sistemas informáticos cuando múltiples procesos intentan acceder a un recurso compartido al mismo tiempo y el resultado final depende arbitrariamente de cuál proceso termine primero) debido a que las dos funciones son asíncronas, por lo que el problema podía ocurrir o no en un mismo dispositivo móvil.

---

### Impacto

- Inconsistencias durante la publicación de comentarios.
- Degradación de la experiencia de usuario al intentar publicar comentarios.
- Publicación de comentarios sin contenido.
- Pérdida de confianza en el servicio por ineficiencia de la funcionalidad.

---

### Acciones tomadas

Se reorganizó el flujo de ejecución para garantizar que la operación de envío del comentario a la base de datos se realizara antes de limpiar el contenido del campo de texto, para almacenarla correctamente antes . 

Además, se implementó un **_“sistema de flags”_** (detecta que recurso pasó primero, por lo que luego pasa el siguiente) para controlar la secuencia de ejecución de las operaciones asíncronas y evitar que se produzca la condición de carrera.

---

### Plan de acción

Para prevenir incidentes similares se decidió:

- Incorporar revisiones tempranas antes de publicar nuevas funcionalidades.
- Probar la navegación completa de la aplicación en el entorno.
- Ejecutar pruebas repetidas sobre un mismo dispositivo para detectar errores.
- Probar la aplicación en distintos sistemas operativos y escenarios de uso.

---

## Aprendizajes

Este incidente permitió comprender la importancia de identificar la causa raíz del problema antes de implementar una solución.

También demostró que es necesario validar las correcciones de la aplicación mediante pruebas repetidas en un mismo dispositivo y en diferentes sistemas operativos para detectar errores que pueden manifestarse de forma intermitente, como las condiciones de carrera. 

Además, se reforzó el uso del control de versiones para documentar las correcciones realizadas y mantener la trazabilidad de los cambios.

---

# Control de versiones

Durante el proyecto se documentaron todos los cambios mediante **GitHub**, registrando las mejoras a través de **_commits_** para mantener un historial de modificaciones del sistema.

---

# Reflexión sobre feedback radicalmente sincero

Durante el análisis del incidente se buscó aplicar un feedback claro, específico y respetuoso entre los integrantes del equipo. Evitando señalar errores, promoviendo un ambiente participativo y enfocado en encontrar soluciones.

#### Ejemplos de feedback aplicado:

- Se señaló que el flujo de publicación ejecutaba la limpieza del comentario antes de completar el envío a la base de datos.
- Se identificó la necesidad de revisar el orden de ejecución de las operaciones asíncronas para evitar condiciones de carrera.
- Se recomendó ampliar las pruebas en distintos escenarios y sistemas operativos para validar la estabilidad de la solución.

#### Como resultado:

- Se identificó con mayor rapidez la causa raíz del error.
- Se reorganizó el flujo de ejecución para evitar la condición de carrera.
- Se fortaleció la práctica de realizar pruebas más completas antes de integrar los cambios mediante control de versiones.
