# Client-Modules.JS

## Introducción

Biblioteca de javascript para cargar módulos en el lado del cliente,
al estilo node.js, usando promesas para descargarlos de manera asíncrona.

Actualmente está sufriendo cambios constantes y algunas de las funcionalidades
podrían no funcionar como están descritas.

## Dependencias

  * [jQuery](https://jquery.com/)

## Instalación

Poner el archivo client-modules.js en una carpeta de su projecto web.
Y despues importarlo en el archivo html.

```html
<!-- Previamente importar jQuery -->
<script type="text/javascript" src="/path/to/client-modules.js"></script>
```

## Configuración

## Módulos

El sistema de módulos de Client-Module.JS esta inspirado en
el sistema de módulos de node.js
(si no está familiarizado con los modulos de node.js
[está lectura](https://nodejs.org/api/modules.html)
podría facilitar la comprensión de este documento).
Pero los módulos de node se cargan de manera síncrona,
un comportamiento similar en el lado del cliente
no sería bién visto por el usuario,
entonces necesitamos un sistema de módulos asíncrono
por eso en Client-Module.JS se utilizán promesas.

### Definir módulos

Cuando escribe un módulo puede usar, al igual que en node.js,
el objeto module.exports para definir
lo que el módulo exportará.
```js
// module.js
module.exports.someFunction = function() {
 // do any thing
}
module.export.someObject = {
  //object content
}
```

Pero si lo que exportará el módelo depende de otros módelos
no podriamos usar simplemente require en la definición del módulo porque eso
ocacionaría la creación de una nueva promesa y la importación
del módulo actual pudiera darse por concluida antes de que el nuevo require sea
correctamente concluido,
finalmente tendriamos un resultado poco predecible.

Para resolver esté problema los módulos de Client-Module.JS tiene el atributo
requirements y prepare
```js
//module.js

// Nombre del Módulo requerido por esté módulo
module.requirements = 'module2';
// es posible requerir mas de uno, por ejemplo:
// module.requirements = ['module2', 'module3'];
// module.requirements = { two: 'module2', three: 'module3' };

// Esta función será ejecutada despues de resolver las dependencias
// especificadas en module.requeriments
// lo que haya sido importado será pasado como atributo de la función prepare
module.prepare = function(imports) {
  module.exports.dependent = {
    // Se puede usar lo que haya sido importado para definir esta función
  }
}
module.exports.independent = function() {
  // Si el elemento a exportar no depende de lo que se vaya a importar
  // puede definirse fuera de prepare o dentro si se desea...
}
```

### Importar módulos

Para importar un modulo use la función require como se indica
```js
require('modules/module')
  .then(function(imports) {
    // imports ese el objeto exports del modulo requerido
    // ahora puede hacer lo que necesite con el objeto importado
  })
  .catch(function(error) {
    console.log('error!!!', error);
  });
```

Si necesita importar mas de un módulo puede pasar un arreglo con los nombres
de los módulos deseados

```js
require(['modules/module1', 'modules/module2', 'modules/module3'])
  .then(function(imports) {
    // imports ese un Array que contiene cada objeto exports de los modulos requeridos
    var module1 = imports[0],
        module2 = imports[1],
        module2 = imports[2],
  })
  .catch(function(error) {
    console.log('error!!!', error);
  });
```