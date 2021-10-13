# Ej. 01 - Homologando los formularios

Dado que tenemos múltiples formularios en nuestra web que invitan al usuario a
dejar su email y suscribirse a nuestros servicios, debemos de homologarlos de
alguna manera para que todos al hacer click en el botón de suscribirse puedan
realizar la misma acción (enviar un correo de bienvenida).

Para lograr este objetivo, podemos asignar una clase a todas las etiquetas
`<form />` o contenedores del formulario en caso de no contar con esta etiqueta.

La clase que vamos a asignar será `signup-form`, dado que el servicio que
agregaremos posteriormente buscará todos los formularios con esta clase para
realizar el envío de correo de bienvenida correctamente.

Debería quedarte algo como:

```html
<form class="signup-form">
  <p>Start publishing today:</p>
  <input type="email" id="email" placeholder="Enter email address" />
  <button type="submit">
    Try it now &rarr;
  </button>
</form>
```

<br/>

## Agregando el script que enviará el correo

Una vez que todos los formularios tienen el mismo identificador, agregaremos un
script de JavaScript que se encargará de detectar estos formularios y agregarle
la funcionalidad enviar el correo de bienvenida.

Para esto, debemos de crear un archivo llamado `app.js` al mismo nivel que los
archivos `index.html` y `styles.css`. Posteriormente copiaremos el siguiente
código y lo pegaremos en este nuevo archivo:

```js
// app.js
const $forms = document.querySelectorAll(".signup-form");

const getTemplate = () => {
  return fetch("./template.html").then((response) => response.text());
};

const sendEmailToApi = (address, template) => {
  fetch(`https://bedu-email-sender-api.herokuapp.com/send`, {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      address: address,
      template: template,
    }),
  })
    .then((results) => {
      console.log(results);
      document.getElementById("email").value = ""
      alert("E-mail send!!!")
    })
    .catch((error) => {
      console.error(error);
      document.getElementById("email").value = ""
      alert("Send failed")
    });
};

const sendEmail = (event) => {
  event.preventDefault();
  const email = event.target.querySelector("input").value;
  getTemplate()
    .then((template) => {
      sendEmailToApi(email, template);
    })
    .catch((error) => {
      console.log(error, "error al convertir el template.");
    });
};

for (let i = 0; i < $forms.length; i++) {
  $forms[i].addEventListener("submit", sendEmail);
}
```

Este script es el código que será responsable de obtener el email que ingrese el
usuario y el template que nosotros crearemos para que se envíe al email.

Para que este script se pueda ejecutar en nuestra página es necesario que lo
agreguemos en nuestro HTML, al igual que hicimos con los scripts de Bootstrap,
debemos de agregalos antes de cerrar la etiqueta `</body>`, haciendo uso de la
etiqueta `<script></script>`. Debemos de agregarlo de la siguiente forma:

```html
<body>
  <!-- Contenido de nuestra página -->
  <script src="./app.js"></script>
</body>
```

Con esto ya tenemos todo listo para dedicarnos a crear nuestra plantilla del
email de bienvenida y probar que se envíe correctamente.

<br/>

## Creación de archivo de plantilla de email

Dado que nuestro script busca el template (plantilla) de nuestro email, debemos
de seguir las convenciones que esta exige para que funcione correctamente, por
lo que vamos a crear un archivo llamado `template.html`, este archivo deberá de
ser creado a la misma altura que los archivos `index.html`, `styles.css` y
`app.js`. La estructura debe verse algo como:

```text
.
+-- app.js
+-- index.html
+-- styles.css
+-- template.html
```

En este archivo escribiremos nuestro HTML y CSS necesario para el email de
bienvenida.

<br/>

## Creando email de prueba

Comencemos por crear un mensaje de prueba que podamos ver en nuestra bandeja de
entrada. Para esto, debemos de agregar algo de HTML en nuestro archivo
`template.html`.

```html
<!-- Template de email de bienvenida -->
<h2>¡Bienvenid@!</h2>
<p>
  Gracias por suscribirte a
  <a href="https://fervent-almeida-b80b1e.netlify.com/">Matcha</a>, te
  aseguramos que fue la mejor opción que existe en el mercado para lograr lo que
  estás buscando.
</p>
```

Para probarlo, vamos a tener que levantar un servidor que se encargue de mostrar
nuestro proyecto, esto debido a que para enviar el correo, nuestra web se tiene
que comunicar con un servidor remoto a través del protocolo HTTP. Aprovecharemos
que instalamos Node.js en la sesión pasada para usar Saas e instalaremos
temporalmente un servidor. Primero, debes de ingresar en tu terminal y asegurarte
que te ubicas en la carpeta del proyecto y luego ejecutar el siguiente comando:

```bash
$ pwd # asegúrate de estar en la ruta del proyecto
/ruta/del/proyecto
$ npx lite-server # esto instalará un servidor de manera temporal
Did not detect a `bs-config.json` or `bs-config.js` override file. Using lite-server defaults...
** browser-sync config **
{ injectChanges: false,
  files: [ './**/*.{html,htm,css,js}' ],
  watchOptions: { ignored: 'node_modules' },
  server:
   { baseDir:
      '.',
     middleware: [ [Function], [Function] ] } }
[Browsersync] Access URLs:
 ------------------------------------
       Local: http://localhost:3000
    External: http://192.168.0.3:3000
 ------------------------------------
          UI: http://localhost:3001
 UI External: http://localhost:3001
 ------------------------------------
[Browsersync] Serving files from: .
[Browsersync] Watching files...
```

`npx` es un software que se instaló junto con Node.js y npm, y lo que hace es
instalar el módulo que nosotros le digamos, en nuestro caso [`lite-server`](https://www.npmjs.com/package/lite-server),
que se encargará de levantar un servidor para nuestro proyecto y nos agilizará
las pruebas dado que si hacemos cambio en nuestros archivos, este servidor
recargará el navegador automáticamente. Probablemente después de que salió el
mensaje en la terminal, te abrió una página en el navegador automáticamente,
dicha página debería estar apuntando a la URL `http://localhost:3000`. Si no se
abrió, asegúrate de abrir manualmente el navegador e ingresar la URL en mención,
el resultado debería ser nuestro proyecto web de Matcha.

!Con esto ya estamos listos! ¿Tú estás list@? Ingresa tu correo electrónico en
el formulario y dale click al botón de suscripción. Luego ingresa a tu bandeja
de entrada, espera un poco y deberías obtener un mensaje de un email llamado
_bedu.sender@gmail.com_ con el mensaje que tu escribiste en el archivo
`template.html` 🎉.

![Primer email enviado](../assets/second-email.png)

Ahora que tal si incrementamos un poco el tamaño de fuente del párrafo y
cambiamos de colores a los textos. ¿Esto sería con CSS, verdad? Y si bien lo
vamos a usar, la forma será a través del atributo [`style`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/style)
en cada una de las etiquetas de HTML que personalicemos.

```html
<h2 style="color: #017374;">¡Bienvenid@!</h2>
<p style="font-size: 16px; color: #1F3B4D;">
  Gracias por suscribirte a
  <a
    href="https://fervent-almeida-b80b1e.netlify.com/"
    style="text-decoration: none; color: #017374; font-weight: 500;"
  >
    Matcha
  </a>
  , te aseguramos que fue la mejor opción que existe en el mercado para lograr
  lo que estás buscando.
</p>
```

¡Wohoo 🥳! Ya logramos poner algo de estilo, prueba tus cambios enviando el
correo, este es el resultado del código de ejemplo:

![Email con estilos](../assets/styled-email.png)

Ahora es el turno de crear un e-mail un poco más de acorde a lo que hemos ido
construyendo durante este curso, por ello, vamos a desarrollar el siguiente
email de bienvenida:

![Matcha - Email de bienvenida](../assets/welcome-email.png)

Empecemos por acomodar todo dentro de una tabla, como hemos revisado en el
prework, las tablas son elementos que nos permiten estructurar nuestra página
web y que garantiza que funcionará en la mayoría de clientes de correo.
Adicionalmente, también es recomendable usar un ancho máximo de `600px` dado que
algunos clientes de correo restringen el ancho a dicha medida. Así que borremos
lo que tenemos en el `template.html` y escribamos lo siguiente:

```html
<table
  style="width: 100%; max-width: 600px; text-align: center; magin: 0 auto;"
>
  <tr>
    <!-- Aquí irá nuestra primera fila -->
  </tr>
</table>
```

Hemos aprovechado en indicar que la alineación de los textos serán centrados,
dado que nuestro diseño así lo usa y también centrar la tabla agregando un margen
automático a los lados. Ahora, para determinar en cuántas filas distribuiremos
nuestras tablas, es relativo, y normalmente puede distribuirse cuando queremos
agrupar distintas porciones de nuestra interfaz. En nuestro caso, vamos a usar 5
filas:

```html
<table
  style="width: 100%; max-width: 600px; text-align: center; magin: 0 auto;"
>
  <tr>
    <!-- Aquí irá la imagen del encabezado -->
  </tr>
  <tr>
    <!-- Aquí irá la imagen descriptiva de Matcha -->
  </tr>
  <tr>
    <!-- Aquí irá el texto de bienvenida y el cta -->
  </tr>
  <tr>
    <!-- Aquí irá las características que Matcha provee -->
  </tr>
  <tr>
    <!-- Aquí irá el pie de página con enlaces a redes sociales -->
  </tr>
</table>
```

Comencemos por definir la imagen junto con el enlace al que redirigirá cuando
le hagan click:

```html
<table
  style="width: 100%; max-width: 600px; text-align: center; margin: 0 auto; background-color: #fffbf7; color: #025157;"
>
  <tr>
    <td>
      <a
        href="https://fervent-almeida-b80b1e.netlify.com/"
        target="_blank"
        style="display:block;"
      >
        <img
          style="width: 50px; margin-top: 15px; margin-bottom: 15px;"
          src="https://getmatcha.com/wp-content/uploads/2020/01/Icon-green.png"
          alt="Matcha"
        />
      </a>
    </td>
  </tr>
  <tr>
    <!-- Aquí irá la imagen descriptiva de Matcha -->
  </tr>
  <tr>
    <!-- Aquí irá el texto de bienvenida y el cta -->
  </tr>
  <tr>
    <!-- Aquí irá las características que Matcha provee -->
  </tr>
  <tr>
    <!-- Aquí irá el pie de página con enlaces a redes sociales -->
  </tr>
</table>
```

Hemos aprovechado en ponerle el color de fondo a la tabla genérica y también el
color de el texto. Por otro lado, hemos creado una etiqueta de ancla para
redireccionar a nuestra página de Matcha si en caso el usuario le da click y
hemos insertado la imagen con un ancho específico además de una ligera separación
al elemento superior e inferior.

<br/>

[Siguiente](../reto-01)