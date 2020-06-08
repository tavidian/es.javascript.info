# LocalStorage, sessionStorage

Los objetos de almacenaje web `localStorage` y `sessionStorage` permiten guardar pares de clave/valor en el navegador.

Lo que es interesante sobre ellos es que los datos sobreviven a una recarga de página (en el caso de `sessionStorage`) y hasta un reinicio completo de navegador (en el caso de `localStorage`). Lo veremos en breve.

Ya tenemos cookies. ¿Por qué tener objetos adicionales?

<<<<<<< HEAD
- Al contrario que las cookies, los objetos de almacenaje web no se envian al servidor en cada petición. Debido a esto, podemos almacenar mucha más información. La mayoría de navegadores permiten almacenar, como mínimo, 2 megabytes de datos (o más) y tienen opciones para configurar éstos límites.
- El servidor no puede manipular los objetos de almacenaje via cabezeras HTTP, todo se hace via JavaScript.
- El almacenaje está vinculado al orígen (al triplete dominio/protocolo/puerto). Esto significa que distintos protocolos o subdominios tienen distintos objetos de almacenaje, no pueden acceder a otros datos que no sean los suyos.
=======
- Unlike cookies, web storage objects are not sent to server with each request. Because of that, we can store much more. Most browsers allow at least 2 megabytes of data (or more) and have settings to configure that.
- Also unlike cookies, the server can't manipulate storage objects via HTTP headers. Everything's done in JavaScript.
- The storage is bound to the origin (domain/protocol/port triplet). That is, different protocols or subdomains infer different storage objects, they can't access data from each other.
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

Ámbos objetos de almacenaje proveen los mismos métodos y propiedades:

- `setItem(clave, valor)` -- almacenar un par clave/valor.
- `getItem(clave)` -- obtener el valor por medio de la clave.
- `removeItem(clave)` -- eliminar la clave y su valor.
- `clear()` -- borrar todo.
- `key(índice)` -- obtener la clave de una posición dada.
- `length` -- el número de ítems almacenados.

<<<<<<< HEAD
Vamos a ver cómo funciona.
=======
As you can see, it's like a `Map` collection (`setItem/getItem/removeItem`), but also allows access by index with `key(index)`.

Let's see how it works.
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

## Demo de localStorage

Las principales funcionalidades de `localStorage` son:

- Es compartido entre todas las pestañas y ventanas del mismo orígen.
- Los datos no expiran. Persisten reinicios de navegador y hasta del sistema operativo.

Por ejemplo, si ejecutas éste código...
```js run
localStorage.setItem('test', 1);
```

... y cierras/abres el navegador, o simplemente abres la misma página en otra ventana, puedes cojer el ítem que hemos guardado de éste modo:

```js run
alert( localStorage.getItem('test') ); // 1
```

<<<<<<< HEAD
Solo tenemos que estar en el mismo dominio/puerto/protocolo, la url puede ser distinta.

`localStorage` es compartido, de modo que si guardamos datos en una ventana, el cambio es visible en la otra.
=======
We only have to be on the same origin (domain/port/protocol), the url path can be different.

The `localStorage` is shared between all windows with the same origin, so if we set the data in one window, the change becomes visible in another one.
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

## Acceso tipo Objeto

Tambien podemos utilizar un modo de acceder/guardar claves del mismo modo que se hace con objetos, así:

```js run
// guarda una clave
localStorage.test = 2;

// coje una clave
alert( localStorage.test ); // 2

// borra una clave
delete localStorage.test;
```

<<<<<<< HEAD
Esto se permite por razones históricas, y principalmente funciona, pero en general no se recomienda por dos motivos:
=======
That's allowed for historical reasons, and mostly works, but generally not recommended, because:
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

1. Si la clave es generada por el usuario, puede ser cualquier cosa, como `length` o `toString`, u otro método propio de `localStorage`. En este caso `getItem/setItem` funcionan correctamente, mientras que el acceso tipo objeto falla;
    ```js run
    let key = 'length';
    localStorage[key] = 5; // Error, no se puede asignar 'length'
    ```
    
2. Existe un evento `storage`, que se dispara cuando modificamos los datos. Este evento no se dispara si utilizamos el acceso tipo objeto. Lo veremos más tarde en este capítulo.

## Iterando sobre las claves

<<<<<<< HEAD
Los métodos proporcionan la funcionalidad get / set / remove. ¿Pero cómo conseguimos todas las claves?
=======
As we've seen, the methods provide "get/set/remove by key" functionality. But how to get all saved values or keys?
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

Desafortunadamente, los objetos de almacenaje no son iterables.

<<<<<<< HEAD
Una opción es utilizar iteración "tipo array":
=======
One way is to loop over them as over an array:
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

```js run
for(let i=0; i<localStorage.length; i++) {
  let key = localStorage.key(i);
  alert(`${key}: ${localStorage.getItem(key)}`);
}
```

<<<<<<< HEAD
Otra opción es utilizar el loop específico para objetos `for key in localStorage`.

Ésta opción itera sobre las claves, pero también devuelve campos propios de `localStorage` que no necesitamos:
=======
Another way is to use `for key in localStorage` loop, just as we do with regular objects.

It iterates over keys, but also outputs few built-in fields that we don't need:
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

```js run
// mal intento
for(let key in localStorage) {
  alert(key); // muestra getItem, setItem y otros campos que no nos interesan
}
```

... De modo que necesitamos o bien filtrar campos des del prototipo con la validación `hasOwnProperty`:

```js run
for(let key in localStorage) {
  if (!localStorage.hasOwnProperty(key)) {
    continue; // se salta claves como "setItem", "getItem" etc
  }
  alert(`${key}: ${localStorage.getItem(key)}`);
}
```

... O simplemente acceder a las claves "propias" con `Object.keys` y iterar sobre éstas si es necesario:

```js run
let keys = Object.keys(localStorage);
for(let key of keys) {
  alert(`${key}: ${localStorage.getItem(key)}`);
}
```

Esta última opción funciona, ya que `Object.keys` solo devuelve las claves que pertenecen al objeto, ignorando el prototipo.

## Solo strings

Hay que tener en cuenta que tanto la clave como el valor deben ser strings.

<<<<<<< HEAD
Cualquier otro tipo, como un número o un objeto, se convierte a cadena de texto automáticamente:
=======
If were any other type, like a number, or an object, it gets converted to string automatically:
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

```js run
sessionStorage.user = {name: "John"};
alert(sessionStorage.user); // [object Object]
```

A pesar de eso, podemos utilizar `JSON` para almacenar objetos:

```js run
sessionStorage.user = JSON.stringify({name: "John"});

// en algún momento más tarde
let user = JSON.parse( sessionStorage.user );
alert( user.name ); // John
```

También es posible pasar a texto todo el objeto de almacenaje, por ejemplo para debugear:

```js run
// se ha añadido opciones de formato a JSON.stringify para que el objeto se lea mejor
alert( JSON.stringify(localStorage, null, 2) );
```


## sessionStorage

El objeto `sessionStorage` se utiliza mucho menos que `localStorage`.

Las propiedades y métodos son los mismos, pero es mucho más limitado:

<<<<<<< HEAD
- `sessionStorage` solo existe dentro de la pestaña actual.
  - Otra pestaña con la misma página tendrá un almacenaje distinto.
  - Pero se comparte entre iframes en la pestaña (asumiendo que tengan el mismo orígen).
- Los datos sobreviven un refresco de página, pero no cerrar/abrir la pestaña.
=======
- The `sessionStorage` exists only within the current browser tab.
  - Another tab with the same page will have a different storage.
  - But it is shared between iframes in the same tab (assuming they come from the same origin).
- The data survives page refresh, but not closing/opening the tab.
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

Vamos a verlo en acción.

Ejecuta éste código...

```js run
sessionStorage.setItem('test', 1);
```

... Y recarga la página. Aún puedes acceder a los datos:

```js run
alert( sessionStorage.getItem('test') ); // después de la recarga: 1
```

... Pero si abres la misma página en otra pestaña, y lo intentas de nuevo, el código anterior devuelve `null`, que significa que no se ha encontrado nada.

Esto es exactamente porque `sessionStorage` no está vinculado solamente al orígen, sino también a la pestaña del navegador. Por ésta razón `sessionStorage` se usa relativamente poco.

## Evento storage

Cuando los datos se actualizan en `localStorage` o en `sessionStorage`, el evento se dispara [storage](https://www.w3.org/TR/webstorage/#the-storage-event) con las propiedades:

<<<<<<< HEAD
- `key` – la clave que ha cambiado, (`null` si se llama `.clear()`).
- `oldValue` – el anterior valor (`null` si se añade una clave).
- `newValue` – el nuevo valor (`null` si se borra una clave).
- `url` – la url del documento donde ha pasado la actualización.
- `storageArea` – bien el objeto `localStorage` o `sessionStorage`, donde se ha producido la actualización.
=======
- `key` – the key that was changed (`null` if `.clear()` is called).
- `oldValue` – the old value (`null` if the key is newly added).
- `newValue` – the new value (`null` if the key is removed).
- `url` – the url of the document where the update happened.
- `storageArea` – either `localStorage` or `sessionStorage` object where the update happened.
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

El hecho importante es: el evento se dispara en todos los objetos `window` donde el almacenaje es accesible, excepto en el que lo ha causado.

Vamos a desarrollarlo.

Imagina que tienes dos ventanas con el mismo sitio en cada una, de modo que `localStorage` es compartido entre ellas.

```online
Quizá quieras abrir ésta página en dos ventanas distintas para probar el código que sigue.
```

<<<<<<< HEAD
Si ambas ventanas están escuchando el evento `window.onstorage`, cada una reaccionará a las actualizaciones que pasen en la otra.

```js run
// se dispara en actualizaciones hechas en el mismo almacenaje, desde otros documentos
window.onstorage = event => {
=======
If both windows are listening for `window.onstorage`, then each one will react on updates that happened in the other one.

```js run
// triggers on updates made to the same storage from other documents
window.onstorage = event => { // same as window.addEventListener('storage', () => {
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8
  if (event.key != 'now') return;
  alert(event.key + ':' + event.newValue + " at " + event.url);
};

localStorage.setItem('now', Date.now());
```

Hay que tener en cuenta que el evento también contiene: `event.url` -- la url del documento en que se actualizaron los datos.

<<<<<<< HEAD
También que `event.storageArea` contiene el objeto de almacenaje -- el evento es el mismo para `sessionStorage` y `localStorage`, de modo que `storageArea` referencia el que se modificó. Podemos hasta querer cambiar datos en él, para "responder" a un cambio.
=======
Also, `event.storageArea` contains the storage object -- the event is the same for both `sessionStorage` and `localStorage`, so `event.storageArea` references the one that was modified. We may even want to set something back in it, to "respond" to a change.
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

**Esto permite que distintas ventanas del mismo orígen puedan intercambiar mensajes.**

Los navegadores modernos también soportan la [API de Broadcast channel API](https://developer.mozilla.org/en-US/docs/Web/API/Broadcast_Channel_API), la API específica para la comunicación entre ventanas del mismo orígen. Es más completa, pero tiene menos soporte. Hay librerías que añaden polyfills para ésta API basados en `localStorage` para que se pueda utilizar en cualquier entorno.

## Resumen

Los objetos de almacenaje web `localStorage` y `sessionStorage` permiten guardar pares de clave/valor en el navegador.
- Tanto la `clave` como el `valor` deben ser strings, cadenas de texto.
- El límite es de más de 2mb; depende del navegador.
- No expiran.
- Los datos están vinculados al orígen (domínio/puerto/protocolo).

| `localStorage` | `sessionStorage` |
|----------------|------------------|
<<<<<<< HEAD
| Compartida entre todas las pestañas y ventanas que tengan el mismo orígen | Accesible en una pestaña del navegador, incluyendo iframes del mismo origen |
| Sobrevive a reinicios del navegador | Muere al cerrar la pestaña |

API:

- `setItem(clave, valor)` -- guarda pares clave/valor.
- `getItem(clave)` -- coje el valor de una clave.
- `removeItem(clave)` -- borra una clave con su valor.
- `clear()` -- bórralo todo.
- `key(índice)` -- coje la clave en una posición determinada.
- `length` -- el número de ítems almacenados.
- Utiliza `Object.keys` para conseguir todas las claves.
- Puede utilizar las claves como propiedades de objetor, pero en ese caso el evento `storage` no se dispara
=======
| Shared between all tabs and windows with the same origin | Visible within a browser tab, including iframes from the same origin |
| Survives browser restart | Survives page refresh (but not tab close) |

API:

- `setItem(key, value)` -- store key/value pair.
- `getItem(key)` -- get the value by key.
- `removeItem(key)` -- remove the key with its value.
- `clear()` -- delete everything.
- `key(index)` -- get the key number `index`.
- `length` -- the number of stored items.
- Use `Object.keys` to get all keys.
- We access keys as object properties, in that case `storage` event isn't triggered.
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8

Evento storage:

<<<<<<< HEAD
- Se dispara en las llamadas a `setItem`, `removeItem`, `clear`.
- Contiene todos los datos relativos a la operación, la `url` del documento y el objeto de almacenaje.
- Se dispara en todos los objetos `window` que tienen acceso al almacenaje excepto el que ha generado el evento (en una pestaña en el caso de `sessionStorage` o globalmente en el caso de `localStorage`).
=======
- Triggers on `setItem`, `removeItem`, `clear` calls.
- Contains all the data about the operation (`key/oldValue/newValue`), the document `url` and the storage object `storageArea`.
- Triggers on all `window` objects that have access to the storage except the one that generated it (within a tab for `sessionStorage`, globally for `localStorage`).
>>>>>>> d35baee32dcce127a69325c274799bb81db1afd8