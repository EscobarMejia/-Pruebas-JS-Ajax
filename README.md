
# Testing Java Script y Ajax

Para empezar a utilizar Jasmine, añadi  ```gem jasmine``` a mi archivo Gemfile y ejecute ```bundle install```



## Gema Jasmine

![Archivo Gemfile]-Pruebas-JS-Ajax/img/1.jpg)

Se crea ahora en  ```spec/javascripts/ ``` el archivo  ```basic_check_spec.js ``` el cual contendra el siguiente codigo:
```
describe ('Jasmine basic check', function() { 
    it('works', function() { expect(true).toBe(true); }); 
}); 
```
## basic_check_spec.js

![basic_check_spec.js](img\2.jpg)

Procedemos a ejecutar ```rails server``` en consola en la dirección http://localhost:3000/specs

## Error
1. Gema Jasmine-Rails

Se presenta el siguiente error :
![Error](img\3.jpg)
cuando se ejecuta solo con la ```gem Jasmine``` por lo tanto es necesario agregar ```gem jasmine-rails```
![Archivo Gemfile](img\4.jpg)

2. boot0.js

El error que se nos presenta es el sigueinte:
![Error](img\5.jpg)

Es necesario agregar en la ruta ```app/assets/config/manifest.js``` la siguiente linea

```//= link boot0.js```

![Error](img\6.jpg)

## Prueba Jasmine
![Jasmine](img\7.jpg)

Se puede apreciar que se ve en blanco la pantalla de jasmin la solución que encontre fue
instalar ```gem 'rake', '13.1.0' ``` y ejecutar ``` rake jasmine ``` lo cual nos devolveria lo siguiente:

![Jasmine](img\8.jpg)


## Preguntas
Pregunta: ¿Cuáles son los problemas que se tiene cuando se debe probar Ajax?. Explica tu respuesta.


Los problemas que tuve fueron que jasmine no me funciona sola sino es necesario 
agregar otras gemas asi como hacer los cambios en manifest.js pero al final la solcuion
fue ejecutar comandos difrentes que estaban en la guia de la pagina web.


Pregunta: ¿Qué son los stubs, espias y fixture en Jasmine para realizar pruebas de Ajax?

1. Stubs :

Se usan los stubs para simular el comportamiento de funciones o métodos que interactúan con llamadas Ajax.

2. Espías :

Los espías son útiles para verificar si una función se ha llamado, con qué 
argumentos y cuántas veces. Se puede espiar las funciones que realizan llamadas 
Ajax para asegurarse de que se están invocando correctamente.

3. Fixtures:

Se puede usar fixtures para simular datos de respuesta de una llamada Ajax. 
En lugar de realizar la llamada Ajax real durante la prueba, puedes usar 
un conjunto de datos predefinido (fixture) como respuesta.

Pregunta: Experimenta el siguiente código de especificaciones (specs) de Jasmine del camino feliz del código AJAX llamado movie_popup_spec.js.
```describe('MoviePopup', function() {
  describe('setup', function() {
    it('adds popup Div to main page', function() {
      expect($('#movieInfo')).toExist();
    });
    it('hides the popup Div', function() {
      expect($('#movieInfo')).toBeHidden();
    });
  });
  describe('clicking on movie link', function() {
    beforeEach(function() { loadFixtures('movie_row.html'); });
    it('calls correct URL', function() {
      spyOn($, 'ajax');
      $('#movies a').trigger('click');
      expect($.ajax.calls.mostRecent().args[0]['url']).toEqual('/movies/1');
    });
    describe('when successful server call', function() {
      beforeEach(function() {
        let htmlResponse = readFixtures('movie_info.html');
        spyOn($, 'ajax').and.callFake(function(ajaxArgs) { 
          ajaxArgs.success(htmlResponse, '200');
        });
        $('#movies a').trigger('click');
      });
      it('makes #movieInfo visible', function() {
        expect($('#movieInfo')).toBeVisible();
      });
      it('places movie title in #movieInfo', function() {
        expect($('#movieInfo').text()).toContain('Casablanca');
      });
    });
  });
});
```
![prueba](img\9.jpg)


¿Que hacen las siguientes líneas del código anterior?.

En mi caso al usar ruby en windows me indica las sigueintes lineas
```
*** SIGUSR2 not implemented, signal based restart unavailable!
*** SIGUSR1 not implemented, signal based restart unavailable!
*** SIGHUP not implemented, signal based logs reopening unavailable!
```
Que me indica que el error es debido a que puma tiene limitantes 
en windows:

El código de pruebas usa los siguientes casos:

1. setup():
- Verifica que el elemento con ID 'movieInfo' exista. 
- Verifica que el elemento con ID 'movieInfo' esté oculto.

2. clicking on movie link:

- Carga una plantilla de fila de película antes de cada prueba.

- Verifica que al hacer clic en un enlace de película, se llame 
a la URL correcta a través de una llamada AJAX utilizando 
spyOn de Jasmine.

3. when successful server call:

- En el caso de una llamada AJAX exitosa, simula la respuesta del 
servidor con datos de película y ejecuta las pruebas siguientes:

  - Verifica que el elemento con ID 'movieInfo' sea visible.
  - Verifica que el contenido de 'movieInfo' contenga el título de la película ('Casablanca' en este caso).


¿Cuál es el papel de ```spyOn``` de Jasmine y los stubs en el código dado.

```spyOn``` se utiliza para espiar la función ```$.ajax```, lo que 
permite realizar un seguimiento de sus llamadas. Esto se emplea 
específicamente para verificar el comportamiento al hacer clic en 
un enlace de películas ```($('#movies a'))```. Además, se utiliza 
un stub con ```and.callFake``` para simular una respuesta exitosa 
del servidor cuando se llama a ```$.ajax```. Este stub reemplaza 
temporalmente la implementación original de ```$.ajax``` para 
controlar y simular el comportamiento deseado durante las pruebas.

```
it('calls correct URL', function() {
      spyOn($, 'ajax');
      $('#movies a').trigger('click');
      expect($.ajax.calls.mostRecent().args[0]['url']).toEqual('/movies/1');
    });
```
Pregunta:¿Qupe hacen las siguientes líneas del código anterior?.

Las líneas de código están probando que al hacer clic en un
enlace de películas ```($('#movies a'))```, se llama a la función 
```$.ajax``` de jQuery con la URL esperada ```('/movies/1')```.

```
 let htmlResponse = readFixtures('movie_info.html');
        spyOn($, 'ajax').and.callFake(function(ajaxArgs) { 
          ajaxArgs.success(htmlResponse, '200');
        });
        $('#movies a').trigger('click');
      });
      it('makes #movieInfo visible', function() {
        expect($('#movieInfo')).toBeVisible();
      });
      it('places movie title in #movieInfo', function() {
        expect($('#movieInfo').text()).toContain('Casablanca');

```

Pregunta: Dado que Jasmine carga todos los ficheros JavaScript antes de ejecutar ningún ejemplo, la llamada a ```setup``` (línea 34 del codigo siguiente llamado ```movie_popup.js```)ocurre antes de que se ejecuten nuestras pruebas, comprueba que dicha función hace su trabajo y muestra los resultados.
```
var MoviePopup = {
  setup: function() {
    // add hidden 'div' to end of page to display popup:
    let popupDiv = $('<div id="movieInfo"></div>');
    popupDiv.hide().appendTo($('body'));
    $(document).on('click', '#movies a', MoviePopup.getMovieInfo);
  }
  ,getMovieInfo: function() {
    $.ajax({type: 'GET',
            url: $(this).attr('href'),
            timeout: 5000,
            success: MoviePopup.showMovieInfo,
            error: function(xhrObj, textStatus, exception) { alert('Error!'); }
            // 'success' and 'error' functions will be passed 3 args
           });
    return(false);
  }
  ,showMovieInfo: function(data, requestStatus, xhrObject) {
    // center a floater 1/2 as wide and 1/4 as tall as screen
    let oneFourth = Math.ceil($(window).width() / 4);
    $('#movieInfo').
      css({'left': oneFourth,  'width': 2*oneFourth, 'top': 250}).
      html(data).
      show();
    // make the Close link in the hidden element work
    $('#closeLink').click(MoviePopup.hideMovieInfo);
    return(false);  // prevent default link action
  }
  ,hideMovieInfo: function() {
    $('#movieInfo').hide();
    return(false);
  }
};
$(MoviePopup.setup);
```

Pregunta: Indica cuales son los stubs y fixtures disponibles en Jasmine y Jasmine-jQuery.

Jasmine:

- Spies: Rastrean llamadas a funciones sin reemplazar su implementación.

  - spyOn(obj, 'method');
- Matchers: Realizan afirmaciones en pruebas.

  - expect(actual).toEqual(expected);

Jasmine-jQuery:

- Fixtures: Cargan fragmentos HTML para simular contenido de página.

  - loadFixtures('myFixture.html');
- Spies y Matchers (Jasmine-jQuery): Funciones específicas para jQuery.

  - spyOnEvent(element, 'click');
  - expect('click').toHaveBeenTriggeredOn(element);

Pregunta: Como en RSpec, Jasmine permite ejecutar código de inicialización y desmantelamiento de pruebas utilizando ```beforeEach``` y ```afterEach```. El código de inicialización carga el fixture HTML mostrado en el código siguiente, para imitar el entorno que el manejador ```getMovieInfo``` vería si fuera llamado después de mostrar la lista de películas.
```
<div id="movies">
  <div class="row">
    <div class="col-8"><a href="/movies/1">Casablanca</a></div>
    <div class="col-2">PG</div>
    <div class="col-2">1943-01-23</div>
  </div>
</div>
```
## Ejercicios

1. Un inconveniente de la herencia de prototipos es que todos los atributos (propiedades) de los objetos son públicos. (Recuerda que en Ruby, ningún atributo era
público). Sin embargo, podemos aprovechar las clausuras para obtener atributos privados. Crea un sencillo constructor para los objetos `User` que acepte un nombre de usuario y una contraseña, y proporciona un método `checkPassword` que indique si la contraseña proporcionada es correcta, pero que deniegue la inspección de la contraseña en sí. Esta expresión de `sólo métodos de acceso` se usa ampliamente en jQuery.

2. SupongaMOS que no puede modificar el código del servidor para añadir la clase CSS `adult` a las filas de la tabla `movies`. ¿Cómo identificaría las filas que están ocultas utilizando sólo código JavaScript del lado cliente?

3. Escribe el código AJAX necesario para crear menús en cascada basados en una asociación `has_many`. Esto es, dados los modelos de Rails A y B, donde A `has_many` (tiene muchos) B, el primer menú de la pareja tiene que listar las opciones de A, y cuando se selecciona una, devolver las opciones de B correspondientes y rellenar el menú B.

4. Extienda la función de validación en ActiveModel para generar automáticamente código JavaScript que valide las entradas del formulario antes de que sea enviado. Por ejemplo, puesto que el modelo `Movie` de `RottenPotatoes` requiere que el título de cada película sea distinto de la cadena vacía, el código JavaScript debería evitar que el formulario `Add New Movie` se enviara si no se cumplen los criterios de validación, mostrar un mensaje de ayuda al usuario, y resaltar el(los) campo(s) del formulario que ocasionaron los problemas de validación. Gestiona, al menos, las validaciones integradas, como que los títulos sean distintos de cadena vacía, que las longitudes máxima y mínima de la cadena de caracteres sean correctas, que los valores numéricos estén dentro de los límites de los rangos, y para puntos adicionales, realiza las validaciones basándose en expresiones regulares.
