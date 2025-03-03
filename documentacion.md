# Documentación de “Nexus Anime”

**Autor:** Jhon Gonzales Velarde  
**Materia:** Desarrollo de Interfaces  

Este documento reúne toda la información referente al proyecto **Nexus Anime**, incluyendo explicación general, detalles de código, capturas y conclusiones.

---

## 1. Descripción General

**Nexus Anime** es un portal web donde se encuentran reseñas, noticias, galerías y un formulario de registro relacionado con el mundo del anime. Sus características más destacadas incluyen:

## Funcionalidades Principales

1. **Cambio de Tema (Claro / Oscuro)**  
   Permite alternar la apariencia de la página entre un modo claro y uno oscuro. Se basa en una función como `switchTheme()`, que añade o quita clases (p. ej. `.claro` / `.oscuro`) en el `<body>` para cambiar colores de fondo, texto y elementos destacados.

2. **Cambio de Idioma (Español / Inglés)**  
   Mediante un diccionario de textos (`languageData`), los elementos del DOM se traducen dinámicamente. Al hacer clic en un enlace con `data-lang="es"` o `data-lang="en"`, se llama a `changeLanguage(lang)` y se reemplazan los textos de la interfaz según el idioma seleccionado.

3. **Formulario de Registro con Validaciones**  
   Incluye validaciones en tiempo real (p. ej. no permitir números en el nombre, correos válidos y contraseñas coincidentes). Se usan clases de Bootstrap (`.is-valid`, `.is-invalid`) para marcar los campos, y funciones específicas (`validateName.js`, `validateEmail.js`, etc.) para cada requisito.

4. **Consumo de API**  
   Se obtiene información (p. ej. una lista de usuarios o animes) desde un endpoint externo (`fetch`). Los datos se inyectan en una tabla HTML (`renderTable()`) o se usan para generar un gráfico con Chart.js (`renderChart()`), ofreciendo contenido dinámico y actualizado.

5. **Búsqueda con Desplazamiento**  
   El usuario ingresa un término en un buscador (`<input type="search">`), y la página hace scroll suave a la sección que coincida con la palabra buscada. Se implementa un mapeo de títulos (p. ej. `"Solo Leveling" -> "solo-leveling"`) y al detectar coincidencia, se ejecuta `scrollIntoView()` para llevar la vista a la sección correspondiente.


## Estructura Principal del Proyecto

La aplicación se organiza en varios archivos que trabajan de manera conjunta para brindar la experiencia completa de **Nexus Anime**:

1. **HTML**  
   - **`index.html`**  
     - Actúa como la **página de inicio**.  
     - Incluye un **Post Destacado**, donde se muestra contenido principal (por ejemplo, la última novedad o anime destacado).  
     - Presenta una sección de **“Publicaciones Recientes”** con tarjetas que enlazan a reseñas o noticias relacionadas.  
   - **`reseña.html`**  
     - Funciona como página **secundaria** orientada a las reseñas.  
     - Contiene un **formulario** que permite al usuario agregar una reseña (previa validación de campos como nombre y correo).  
     - Cuenta con **tablas** para mostrar listados (p. ej. “Top 10 Animes”) y un **carrusel** que destaca los más populares o mejor valorados.  
     - También implementa el **consumo de API** (por ejemplo, con `fetch`) para mostrar datos dinámicos en una tabla o gráfico (Chart.js).

2. **CSS**  
   - **`index.css`** y **`reseña.css`**  
     - Hojas de estilo personalizadas para **dar formato** y **estética** a cada página.  
     - Pueden incluir reglas para la versión **claro/oscuro** (aunque el conmutado se hace vía JavaScript y clases CSS).  
     - Aseguran una **presentación coherente** con la estructura de Bootstrap utilizada.

3. **JavaScript** (dividido en varios archivos para mayor organización):  
   - **`main.js`**  
     - Contiene la lógica principal:  
       - **Validaciones** de formularios en tiempo real (p. ej., nombre sin dígitos, contraseñas iguales).  
       - **Envío de formularios** (evitar submit si hay errores).  
       - **Listeners** para la búsqueda, el cambio de idioma, o cualquier otra interacción global.  
   - **`changeTheme.js`**  
     - Define la función `switchTheme()` que **alterna** entre el modo **claro** y **oscuro**.  
     - Cambia clases del `<body>` (p. ej., `.oscuro`, `.claro`), lo cual ajusta los colores y el fondo (definidos en CSS).  
   - **`changeLanguage.js`**  
     - Implementa la función `changeLanguage()` para cambiar entre **español** e **inglés**.  
     - Usa un **diccionario** de textos, y mediante `document.getElementById()` actualiza la interfaz en tiempo real.  
   - **`searchAnime.js`**, **`validateName.js`**, etc.  
     - **Funciones específicas** de validación (por ejemplo, prohibir números en el nombre),  
     - Búsqueda con desplazamiento (mapear títulos a secciones y llamar a `scrollIntoView()`),  
     - Cualquier otra lógica modularizada para mantener el código organizado y facilitar su mantenimiento.

Esta separación de responsabilidades (HTML para contenido, CSS para diseño y JavaScript para lógica) hace el proyecto más **escalable**, **fácil de entender** y **modificar** de manera independiente.  

---

## 2. Código Crítico

En esta sección se describen algunos de los **métodos más críticos** del proyecto, mostrando su **código** y explicando su **función** dentro de la aplicación. Estas funciones evidencian cómo se realizan el cambio de idioma, la validación de formularios y el consumo de datos desde una API para generar tablas y gráficos.

---

### 2.1. `changeLanguage(lang)`

**Descripción:**  
Este método se encarga de **cambiar todo el texto de la interfaz** entre español e inglés. Se basa en un **diccionario** que mapea cada `id` de HTML con su correspondiente texto en cada idioma. Una vez que el usuario selecciona “Español” o “English”, la función actualiza el contenido de cada elemento en tiempo real.

```js
// changeLanguage.js
function changeLanguage(lang) {
  const languageData = {
    es: {
      navbarBrand: "Nexus Anime",
      navInicio: "Inicio",
      navResenas: "Reseñas",
      // ...
      featuredPostTitle: "\"Solo Leveling\" temporada 2",
      featuredPostDesc: "Nosotros creemos que a sus personajes protagonistas...",
      featuredPostBtn: "Leer más"
      // ... (resto de claves)
    },
    en: {
      navbarBrand: "Nexus Anime",
      navInicio: "Home",
      navResenas: "Reviews",
      // ...
      featuredPostTitle: "\"Solo Leveling\" Season 2",
      featuredPostDesc: "We believe its protagonists still lack some charisma...",
      featuredPostBtn: "Read more"
      // ... (resto de claves)
    }
  };
  
  const data = languageData[lang];
  if (!data) return;  // Si no existe el idioma, salir

  // Actualiza los elementos del DOM según su id
  document.getElementById('navbarBrand').textContent = data.navbarBrand;
  document.getElementById('navInicio').textContent = data.navInicio;
  document.getElementById('navResenas').textContent = data.navResenas;
  // ...continúa para todos los elementos (featuredPostTitle, featuredPostDesc, etc.)
}
```
### 2.2. Consumo de API en `main.js`

Este fragmento ilustra el uso de `fetch` para obtener datos de una API externa. Una vez que la información se recibe, se transforma a JSON y se llama a funciones como `renderTable(data)` y `renderChart(data)` para mostrar los resultados en la interfaz.

```js
// Fragmento en main.js
fetch('https://jsonplaceholder.typicode.com/users')
  .then(response => response.json())
  .then(data => {
    // Llamamos a nuestras funciones para mostrar estos datos
    renderTable(data);
    renderChart(data);
  })
  .catch(err => console.error("Error al obtener la API:", err));
```

### 2.3. Generación de Tabla: `renderTable(users)`

Este método construye de forma dinámica el HTML de una tabla para mostrar una lista de objetos (por ejemplo, usuarios) obtenidos desde una API u otra fuente de datos.

```js
// main.js (o script aparte)
function renderTable(users) {
  let tableHTML = '<table class="table table-striped table-dark">';
  tableHTML += '<thead><tr><th>ID</th><th>Nombre</th></tr></thead><tbody>';
  
  users.forEach(user => {
    tableHTML += `
      <tr>
        <td>${user.id}</td>
        <td>${user.name}</td>
      </tr>
    `;
  });
  
  tableHTML += '</tbody></table>';
  
  // Inyectamos la tabla en el contenedor de la página
  document.getElementById('apiTableContainer').innerHTML = tableHTML;
}
```



## Tabla de Casos de Prueba

A continuación se presenta una tabla con diferentes casos de prueba realizados sobre la aplicación **Nexus Anime**, cubriendo escenarios de validación, cambio de tema/idioma, y consumo de datos desde la API.

| **ID** | **Caso de Prueba**                                   | **Pasos**                                                                                                                             | **Resultado Esperado**                                                                               | **Resultado Obtenido**                                                                              | **Observaciones**                                                                                           |
|--------|-------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| CP01   | Validar nombre con dígitos                           | 1. Abrir la página con el formulario de registro <br> 2. Ingresar un nombre con números (p. ej.: "Juan123") <br> 3. Perder el foco del campo o intentar enviar el formulario | El campo se debe marcar como inválido (`is-invalid`) y no permitir el envío hasta corregir el error.  | El campo se muestra en rojo y la clase `is-invalid` se aplica correctamente.                             | Se cumple la validación y evita un nombre con caracteres no deseados.                                        |
| CP02   | Validar nombre sin dígitos                           | 1. Ingresar un nombre válido (p. ej.: "Juan Perez") <br> 2. Perder el foco o enviar el formulario                                                                           | El campo se marca como válido (`is-valid`) y se permite avanzar en el proceso.                        | El sistema reconoce el nombre como correcto y no muestra errores.                                         | Validación de nombre sin dígitos funciona correctamente.                                                    |
| CP03   | Validar correos con formato incorrecto               | 1. Ingresar un correo con errores (p. ej.: "correo@sinpunto") <br> 2. Enviar el formulario                                                                                   | El campo se marca como inválido, mostrando mensaje de error si lo hubiera, y bloqueando el envío.     | Se aplica `is-invalid`, el envío es bloqueado y se indica que el correo es inválido.                     | Confirmado que el formulario no avanza con correos mal formateados.                                          |
| CP04   | Validar contraseñas distintas                       | 1. Ingresar “Contraseña” y “Confirmación” diferentes <br> 2. Enviar el formulario                                                                                           | Debe bloquear el envío y marcar el campo de confirmación como inválido (`is-invalid`).               | El formulario rechaza el envío y muestra el campo de confirmación en rojo.                                | Correcta detección de contraseñas distintas.                                                                 |
| CP05   | Validar contraseñas iguales                         | 1. Ingresar “Contraseña” y “Confirmación” coincidentes <br> 2. Enviar el formulario                                                                                         | Permitir el envío, ambas contraseñas se consideran válidas.                                           | El formulario se envía exitosamente cuando las contraseñas coinciden.                                     | Acepta contraseñas coincidentes.                                                                             |
| CP06   | Cambio a Tema Claro                                 | 1. Seleccionar la opción “Tema → Claro” en la navbar <br> 2. Observar el fondo y los colores                                          | El sistema cambia la clase del `<body>` a `.claro`, mostrándose un fondo claro y texto más oscuro.    | La página adopta el gradiente y colores claros, confirmando la clase `.claro`.                           | Funciona el conmutado de tema.                                                                               |
| CP07   | Cambio a Tema Oscuro                                | 1. Seleccionar la opción “Tema → Oscuro” en la navbar <br> 2. Verificar el nuevo estilo                                                | El `<body>` cambia a la clase `.oscuro`, mostrando fondo oscuro y texto contrastante.                | El tema se actualiza adecuadamente a un gradiente oscuro.                                               | Se confirma que se alternan correctamente las clases de tema.                                                |
| CP08   | Cambio de Idioma a Español                          | 1. Presionar “Idioma → Español” <br> 2. Revisar los textos en la navbar, post destacado, etc.                                          | Todos los elementos traducibles (navbar, botones, etc.) aparecen en español.                         | Se actualizan textos como “Inicio”, “Reseñas”, “Iniciar Sesión”, etc.                                    | Traducción a español confirmada.                                                                             |
| CP09   | Cambio de Idioma a Inglés                           | 1. Presionar “Idioma → English” <br> 2. Revisar los textos en la navbar, post destacado, etc.                                          | Todos los elementos traducibles cambian al idioma inglés.                                             | “Home”, “Reviews”, “Login”, etc. se reflejan correctamente.                                            | Se verifica la traducción exitosa a inglés.                                                                  |
| CP10   | Búsqueda y desplazamiento a sección específica       | 1. En el buscador, ingresar “One Piece” <br> 2. Enviar el formulario de búsqueda                                                       | La vista hace scroll suave hasta la sección identificada como `id="one-piece"` o similar.             | La página se desplaza automáticamente a la sección que habla de “One Piece”.                            | El mapeo de títulos y el uso de `scrollIntoView()` son correctos.                                            |
| CP11   | Consumo de API y renderizado de tabla                | 1. Cargar la sección donde se llama a `fetch` (p. ej. `reseña.html`) <br> 2. Esperar la respuesta de la API `https://jsonplaceholder.typicode.com/users`                     | Debe mostrarse una tabla con filas correspondientes a los objetos devueltos por la API.              | La tabla se construye dinámicamente con `renderTable(data)`, mostrando ID y nombre de los usuarios.     | Se confirman datos reales obtenidos de la API y mostrados en la página.                                     |
| CP12   | Generación de gráfico (Chart.js) tras consumir datos | 1. Tras la carga de la API, verificar si `renderChart(data)` dibuja un gráfico <br> 2. Revisar la exactitud en base a la información    | Se genera un gráfico (barra, línea, etc.) mostrando datos clave, p. ej. longitud del nombre de usuario. | El gráfico aparece con las etiquetas correctas y valores que coinciden con los datos de la API.         | Confirmada la integración de Chart.js y su correcta actualización con la data recibida.                     |

**Conclusión:**  
Se han cubierto los escenarios principales del uso de la aplicación, confirmando que los flujos de validación, cambio de tema/idioma, búsqueda, y consumo de API funcionan de manera estable y cumplen las expectativas de diseño y usabilidad.



