# 10. Repaso y recursos de consulta

*Fuente: [Unidad 4.01 - Widgets en Flutter - 10. Repaso y recursos de consulta](https://aules.edu.gva.es/fp/mod/book/view.php?id=11996459&chapterid=76818)*

En esta unidad hemos aprendido a construir interfaces con Flutter utilizando widgets, organizar el código en varios archivos, gestionar el estado y responder a las acciones del usuario.

### 10.1. Conceptos principales de la unidad

Estas son las ideas que debes comprender y saber aplicar:

- **Árbol de widgets:** una interfaz se construye combinando widgets dentro de otros. Las propiedades `child` y `children` permiten establecer estas relaciones.
- **Composición:** widgets como `Row`, `Column`, `Padding`, `Container` y `Expanded` permiten organizar y distribuir el contenido.
- **Estado:** `StatelessWidget` no gestiona estado mutable propio, mientras que `StatefulWidget` permite mantener datos que cambian. Con `setState()` indicamos que la interfaz necesita actualizarse.
- **Componentes reutilizables:** podemos crear nuestros propios widgets y configurar su contenido mediante propiedades obligatorias, opcionales y valores por defecto. También pueden tener métodos propios que usa su `build()`.
- **Eventos y callbacks:** propiedades como `onPressed` permiten responder a las acciones del usuario. Con `VoidCallback` podemos comunicar una acción desde un componente reutilizable a la pantalla que lo utiliza.
- **Formularios:** `TextField`, `TextFormField` y `Form` permiten recoger información y validar los datos introducidos.
- **Organización del código:** separar las pantallas, los componentes y las funciones de cálculo facilita el mantenimiento y la reutilización.
- **Depuración:** podemos utilizar `debugPrint()`, puntos de interrupción, `flutter analyze` y Flutter Inspector para investigar problemas.

### 10.2. Guía rápida: ¿qué widget o herramienta necesito?

Cuando desarrolles una interfaz, puedes utilizar estas referencias para elegir por dónde empezar:

- **Mostrar información:** `Text`, `Icon` y `Image`.
- **Organizar elementos:** `Row`, `Column`, `Center` y `Expanded`.
- **Mostrar una lista que se desplaza:** `ListView`.
- **Añadir espacios o estilos:** `SizedBox`, `Padding` y `Container`.
- **Permitir interacciones:** `ElevatedButton`, `OutlinedButton`, `TextButton` y `Slider`.
- **Recoger y validar datos:** `TextField`, `TextFormField` y `Form`.
- **Mostrar mensajes temporales:** `SnackBar` y `ScaffoldMessenger`.
- **Pedir una confirmación:** `AlertDialog` con `showDialog()`.
- **Crear componentes propios:** `StatelessWidget` o `StatefulWidget`, según las necesidades.

Esta lista es un punto de partida. Flutter incluye muchos más widgets, y es normal tener que consultar la documentación para encontrar el más adecuado.

### 10.3. Recursos de consulta

Estos recursos te permitirán ampliar información y consultar ejemplos cuando tengas dudas.

#### Documentación oficial de Flutter

- [**Catálogo de widgets de Flutter**](https://docs.flutter.dev/ui/widgets). Para descubrir widgets y consultar sus funciones principales.
- [**Widgets de composición y distribución**](https://docs.flutter.dev/ui/widgets/layout). Para resolver dudas sobre filas, columnas, tamaños y distribución del espacio.
- [**Interactividad y gestión del estado**](https://docs.flutter.dev/ui/interactivity). Para repasar `StatelessWidget`, `StatefulWidget` y las interacciones.
- [**Formularios en Flutter**](https://docs.flutter.dev/cookbook/forms). Incluye ejemplos para recoger y validar datos.
- [**Flutter con Visual Studio Code**](https://docs.flutter.dev/tools/vs-code). Para consultar herramientas de ejecución y depuración.
- [**Referencia de la API de Flutter**](https://api.flutter.dev/). Para consultar las propiedades, los constructores y los métodos de cada clase.

#### Lenguaje Dart y materiales complementarios

- [**Guía del lenguaje Dart**](https://dart.dev/language). Para consultar clases, funciones, constructores, tipos y parámetros.
- [**Introducción a los widgets, de José A. Múrcia**](https://joamuran.net/curs-flutter/materials/U3/3.1.IntroGinys.html). Material complementario en valenciano sobre interfaces declarativas y el árbol de widgets.

No necesitas leer toda la documentación antes de programar. Utilízala como referencia cuando tengas una duda concreta o quieras investigar otra forma de resolver un problema.
